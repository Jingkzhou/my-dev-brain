---
type: 主题
keywords: [Gemma, 多模态微调, LoRA, PEFT, Apple Silicon, MPS]
---

# Gemma多模态微调

## 定义

Gemma 多模态微调，指在 Gemma 3n / Gemma 4 这类同时具备文本、图像、音频处理能力的模型上，基于监督数据做参数高效微调。当前较常见的工程路径不是全参数训练，而是 `Transformers + PEFT LoRA`，只在注意力或前馈模块上挂适配器。

## 当前结论

- `mattmireles/gemma-tuner-multimodal` 的方法本质上是标准 HF LoRA，而不是新的 Gemma 专属训练算法。
- 它的核心训练入口在 `gemma_tuner/models/gemma/finetune.py`：先加载 Gemma 基座模型，再根据 profile 注入 `LoraConfig`，最后用 Hugging Face `Trainer` 进行 supervised fine-tuning。
- 训练模式分三类：
  - `modality = text`：文本监督微调，支持 `instruction` 与 `completion`
  - `modality = image`：图像加文本监督微调，支持 `caption` 与 `vqa`
  - `modality = audio`：音频加文本监督微调，默认是“请转写这段音频”
- 这个仓库统一采用 chat template 组织训练样本。也就是说，不是简单拼接字段，而是把 user / assistant 轮次显式构造成对话，再编码成 `input_ids` 与 `labels`。
- 它明确做了 prompt masking：训练损失主要落在 assistant 回复上，而不是用户输入上。这一点对 instruction tuning 很关键。
- 图像和音频并不是自己手写特征抽取逻辑，而是依赖 `AutoProcessor` 做多模态打包，然后用自定义 collator 补齐 `labels`、`token_type_ids`、`mm_token_type_ids` 等训练所需字段。
- Apple Silicon 是这个仓库的重点工程目标，所以很多设计是围绕 MPS 稳定性展开的，例如优先 `bf16`、强制 `attn_implementation = eager`、限制 batch size、倾向开启 gradient checkpointing。
- 这个仓库默认不是 QLoRA / 4-bit 路线，而是常规 LoRA。它解决的是“Gemma 多模态 + MPS 能稳定跑起来”，不是“极限压缩显存”。

## 关键问题

### 1. 它到底怎么组织训练数据？

- 文本模式：
  - instruction：CSV 至少有 `prompt` 和 `response`
  - completion：CSV 至少有 `text`
- 图像模式：
  - caption：CSV 至少有 `image_path` 和描述列
  - vqa：CSV 至少有 `image_path`、`question`、`answer`
- 音频模式：
  - CSV 至少有 `audio_path`、转写文本、可选 `language`、`duration`
- 所有模式最终都落到 `data/datasets/<name>/train.csv` 和 `validation.csv`

### 2. LoRA 打在哪些层？

- 默认 profile 示例里是 `q_proj,k_proj,v_proj,o_proj`
- 代码会先扫描模型真实模块，再校验这些 target modules 是否存在
- 如果配置的模块不存在，会直接报错，而不是静默忽略
- 对 Gemma 4，还额外防守了 `Gemma4ClippableLinear` 这类 PEFT 不兼容层

### 3. 图像和音频是怎么喂给模型的？

- 图像：
  - collator 把样本包装成 `user: [image, text] -> assistant: [text]`
  - caption 模式会固定加一句 `Describe this image.`
  - VQA 模式则把 question 放到用户侧文本
  - `image_token_budget` 决定视觉 token 预算，支持 `70/140/280/560/1120`
- 音频：
  - collator 把样本包装成 `user: [audio, "Please transcribe this audio."] -> assistant: [text]`
  - 音频波形由预处理函数加载，再交给 processor 编码

### 4. 为什么这个方法适合学习？

- 它把多模态 SFT 里最关键的几个真实问题都暴露出来了：
  - 数据列怎么设计
  - chat template 怎么构造
  - prompt token 怎么 masking
  - processor 不返回完整字段时怎么补
  - LoRA target modules 怎么验证
  - 训练完怎么 merge / export
- 所以它适合拿来学“Gemma 多模态微调的工程骨架”，而不只是学命令行。

## 争议与待确认

- 这个仓库强调 Apple Silicon + MPS 场景，因此其中的 batch size、dtype、attention backend 选择，不一定直接适用于 CUDA 服务器。
- README 里把 Gemma 4 与 Gemma 3n 放到统一训练入口下，但不同版本的 `transformers` / `peft` 兼容性仍可能变化，后续需要结合具体环境验证。
- 该方法是 LoRA 路线，不覆盖 QLoRA、全参数微调、DPO、GRPO 等后续训练阶段。

## 关联实体

- [[01-资料库/2026-04-09-Gemma Multimodal Fine-Tuner|Gemma Multimodal Fine-Tuner]]

## 关联资料

- [[01-资料库/2026-04-09-Gemma Multimodal Fine-Tuner|Gemma Multimodal Fine-Tuner]]

## 下一步

- 补一页 “Gemma 多模态微调最小实战步骤”，把仓库方法压缩成 5 到 8 步。
- 对比 Gemma 的 `processor + chat template + collator` 路径，与 Qwen2.5-VL / Llama 3.2 Vision 的训练路径有什么相同和不同。
- 如果后续真要动手跑一次，再补充环境搭建、样本格式和训练参数的实测经验。
