# 操作日志

按时间追加记录 `ingest`、`query`、`lint` 三类操作。

## 2026-04-05 init

- type: setup
- summary: 初始化 LLM Wiki 目录结构、首页索引、日志页与模板。
- changed:
  - `AGENTS.md`
  - `00-首页/index.md`
  - `05-日志/log.md`
  - `90-模板/资料模板.md`
  - `90-模板/主题模板.md`
  - `90-模板/实体模板.md`
  - `90-模板/综合模板.md`

## 2026-04-05 ingest

- type: ingest
- summary: 根据 Karpathy 的 LLM Wiki 思路创建首批资料页、主题页、实体页和综合页。
- source:
  - `01-资料库/2026-04-05-Karpathy-How I use LLMs.md`
- changed:
  - `02-主题/LLM Wiki.md`
  - `02-主题/RAG.md`
  - `02-主题/个人知识库.md`
  - `03-实体/Karpathy.md`
  - `03-实体/Obsidian.md`
  - `04-综合/LLM Wiki-搭建路线图.md`
  - `00-首页/index.md`

## 2026-04-05 setup-projects

- type: setup
- summary: 新增项目目录与项目模板，并将入口加入首页和规则说明。
- changed:
  - `06-项目/README.md`
  - `90-模板/项目模板.md`
  - `AGENTS.md`
  - `00-首页/index.md`

## 2026-04-05 create-project

- type: setup
- summary: 创建首个实际项目页“我的 LLM Wiki 搭建”，用于追踪知识库系统建设。
- changed:
  - `06-项目/我的 LLM Wiki 搭建.md`
  - `06-项目/README.md`
  - `00-首页/index.md`

## 2026-04-06 ingest

- type: ingest
- summary: 导入本地 Agent Harness 学习资料，并创建对应主题页。
- source:
  - `01-资料库/2026-04-06-Agent Harness 讲解页.md`
- changed:
  - `02-主题/Agent Harness.md`
  - `06-项目/我的 LLM Wiki 搭建.md`
  - `00-首页/index.md`

## 2026-04-07 setup

- type: setup
- summary: 新增个人上下文页面，让知识库开始承载“关于我”的长期信息。
- changed:
  - `00-首页/我是谁.md`
  - `00-首页/我的偏好.md`
  - `00-首页/我的目标.md`
  - `06-项目/当前学习地图.md`
  - `00-首页/index.md`
  - `06-项目/README.md`

## 2026-04-07 setup-dream

- type: setup
- summary: 新增“做梦层”，包括梦境目录、模板、使用说明和首篇梦境页。
- changed:
  - `AGENTS.md`
  - `04-综合/梦境/README.md`
  - `04-综合/梦境/2026-04-07-梦境.md`
  - `00-首页/如何做梦.md`
  - `90-模板/梦境模板.md`
  - `04-综合/README.md`
  - `00-首页/index.md`

## 2026-04-07 dream

- type: dream
- summary: 生成首篇梦境页，综合当前知识库对“项目正在从知识整理转向自我建模”的判断。
- changed:
  - `04-综合/梦境/2026-04-07-梦境.md`

## 2026-04-07 dream-2

- type: dream
- summary: 生成第二篇梦境页，强调系统正在从知识管理转向认知压缩与方向判断。
- changed:
  - `04-综合/梦境/2026-04-07-梦境-2.md`
  - `04-综合/梦境/README.md`
