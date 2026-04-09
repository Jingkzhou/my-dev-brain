# Gemma Multimodal Fine-Tuner

## 元信息

- 来源：https://github.com/mattmireles/gemma-tuner-multimodal
- 作者：mattmireles
- 发布时间：仓库持续更新中
- 获取日期：2026-04-09
- 类型：GitHub 仓库 / 实战型训练框架

## 核心观点

- 这个仓库的训练核心不是自研算法，而是 `Hugging Face Transformers + PEFT LoRA + 自定义 multimodal collator` 的工程化组合。
- 它面向 Gemma 3n / Gemma 4 的多模态监督微调，支持三种模式：`text`、`image`、`audio`。
- 文本模式支持 instruction tuning 与 completion tuning；图像模式支持 caption 与 VQA；音频模式默认做转写式 audio-to-text 微调。
- 训练数据统一走 CSV，图像和文本主要是本地 `data/datasets/<name>/train.csv` / `validation.csv`；音频额外支持预处理、GCS、BigQuery 与流式加载。
- LoRA 默认挂到 `q_proj,k_proj,v_proj,o_proj`，并在训练前检查目标模块是否真实存在，避免直接对不兼容层注入。
- 这个仓库的重点优势不在“更强的微调算法”，而在“让 Apple Silicon 上的 Gemma 多模态 LoRA 训练可跑、可调、可导出”。

## 关键摘录

- README 明确写到训练路径是 “Hugging Face Gemma checkpoints + PEFT LoRA, supervised fine-tuning in `gemma_tuner/models/gemma/finetune.py`”。
- `config/config.ini.example` 给出的 profile 结构说明了训练入口是“模型段 + 数据集段 + profile 段”的组合，LoRA 超参数直接在 profile 中配置。
- `gemma_tuner/models/common/collators.py` 显示，这个项目把图像、音频、文本都统一转成 chat-template 形式，再交给 processor / tokenizer 编码，并显式做 prompt masking。

## 可更新页面

- [[02-主题/Gemma多模态微调|Gemma多模态微调]]
- [[06-项目/当前学习地图|当前学习地图]]

## 关联页面

- [[02-主题/Gemma多模态微调|Gemma多模态微调]]
- [[06-项目/当前学习地图|当前学习地图]]

## 备注

- 如果后续继续深入，可以补一页 “Gemma vs Qwen vs Llama 的多模态微调路径对比”。
- 这个仓库的结论主要适用于 Apple Silicon + MPS 场景，不等于 CUDA / QLoRA 的常规最佳实践。
