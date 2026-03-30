# Claude Code 项目指引

## 项目概述

本项目是一篇博士毕业论文，将三篇已发表论文（NAP, CADS, SWaST）整合为以"深度学习全流程数据选择"为主线的完整论文。写作语言为中文。

## 写作时必读文件

在进行任何论文写作、改编、润色任务时，必须先阅读以下文件：

- `WRITING_PRINCIPLES.md` — 写作质量核心原则（七条原则，判断写作质量的根本标准）
- `WRITING_GUIDELINES.md` — 写作规范与指导方针（格式、结构、各章写作要点）
- `thesis/notation.md` — 统一符号表（符号冲突的解决方案）
- `PROGRESS.md` — 当前写作进度（每次会话开始时检查）
- `thesis/citation-index.md` — 引用文献索引（写草稿时查阅可用引用）
- `thesis/references.bib` — 统一参考文献库（三篇论文 bib 合并去重）

## 写作工作流

- 所有写作产出必须**写入对应章节目录下的文件**，而非仅在对话中展示
- 写作四步细化流程：
  1. **骨架**（`skeleton.md`）：每小节 1-2 句话的内容摘要，明确"写什么"
  2. **要点**（`points.md`）：每段落 1-3 句话的具体论述要点，明确"怎么论证"。在此阶段标注图表插入位置（如 `[图X-Y: 描述]`）
  3. **草稿**（`draft.md`）：完整中文正文（markdown 格式），包含 `\cite{key}` 引用和图表位置标注
  4. **定稿**（`final.tex`）：LaTeX 格式正文，可直接复制到 Overleaf。对于需要用户手动绘制的图表，生成占位 tex 代码（`\begin{figure}...\placeholder...\end{figure}`）
- 每个章节的文件路径：`thesis/ch{N}-{name}/{skeleton,points,draft}.md` 和 `thesis/ch{N}-{name}/final.tex`
- 写作以**迭代修改**为主：先写入文件，用户在文件中审阅/批注，再根据反馈修改文件内容
- 引用规范：在 draft.md 和 final.tex 中使用 `\cite{key}` 格式引用，key 来自 `thesis/references.bib`。写草稿前查阅 `thesis/citation-index.md` 找到合适的引用
- 图表规范：在 points.md 或 draft.md 阶段确定图表插入位置和内容描述；在 final.tex 中，能自动生成的图表直接写 tex 代码，需用户手动绘制的图表用占位代码标注（含图表说明和预期内容描述）
- 每完成一个阶段性产出，更新 `PROGRESS.md`

## 关键规则

- 核心章节是**重新叙述**，不是英文论文的直译
- 每段文字必须服务于"数据选择"统一主线
- 先讲动机，再讲方法；公式用来精确表达直觉
- 一次只处理单个章节或小节
- 涉及实验数字的内容标注 `[VERIFY]`，需与原论文核对
