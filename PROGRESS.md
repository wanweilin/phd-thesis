# 博士论文写作进度

## 当前阶段
Phase 5.5: 核心章节扩展 — 进行中（第3-5章从小论文简略版扩展为完整大论文）

## 各章进度
| 章节 | Phase 1 全文骨架 | Phase 2 章节骨架 | Phase 3 段落要点 | Phase 4 正文草稿 | Phase 5 LaTeX初版 | Phase 5.5 内容扩展 | Phase 6 润色 |
|------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 第一章 绪论 | done | done | done | done | done | — | |
| 第二章 相关工作 | done | done | done | done | done | — | |
| 第三章 CADS | done | done | done | done | done | **待开始** | |
| 第四章 SWaST | done | done | done | done | done | 待开始 | |
| 第五章 NAP | done | done | done | done | done | 待开始 | |
| 第六章 总结与展望 | done | done | done | done | done | — | |

### Phase 5.5 扩展进度（按写作四步流程）
| 章节 | skeleton.md | points.md | draft.md | LaTeX更新 |
|------|:---:|:---:|:---:|:---:|
| 第三章 CADS | 待开始 | | | |
| 第四章 SWaST | | | | |
| 第五章 NAP | | | | |

## Phase 0 清单
- [x] 通读三篇论文，列出符号差异表 → `thesis/notation.md`
- [x] 确定统一符号方案 → `thesis/notation.md` 符号冲突与解决方案
- [x] 搭建 `thesis/` 目录结构
- [x] 创建 `PROGRESS.md`（本文件）

## Phase 1 清单
- [x] 确认6章大纲及各章定位
- [x] 设计双主线叙事框架（阶段视角 + 交互变量视角）
- [x] 设计章节间过渡逻辑
- [x] 构思研究地图
- [x] 输出 → `thesis/outline-v3.md`（定稿版），另保留 v1/v2 供参考

## 关键决策记录
- [2026-03-26] 写作策略：自顶向下五层细化（全文骨架→章节骨架→段落要点→正文草稿→润色）
- [2026-03-27] 写作产出文件规范：skeleton.md（骨架）→ points.md（要点）→ draft.md（草稿），存于各章目录下
- [2026-03-26] 写作语言：中文，术语首次出现附英文
- [2026-03-26] 内容优先，格式/模板后续整理
- [2026-03-26] 符号冲突解决：$C$（CADS=计算预算, NAP改用$N_c$表通道数）；$K$（CADS保��, NAP改用$N_{\text{cls}}$）；$\alpha$（各章加下标区分）
- [2026-03-26] 论文标题（暂定）：数据选择驱动的深度学习效率与可靠性优化研究
- [2026-03-26] 双主线叙事：主线A按阶段（训练侧→推理侧），主线B按交互变量（计算资源→模型容量→推理时分布偏移）
- [2026-03-26] 第三、四章同属训练侧，不强求每章对应独立阶段
- [2026-03-26] 第三、四章各增加讨论小节（loss predictor / scaling laws）
- [2026-03-26] 写作质量核心原则 → `WRITING_PRINCIPLES.md`（七条原则）

## Phase 5 清单（LaTeX初版 — 已完成框架）
- [x] 解压fduthesis复旦模板到 `latex/` 目录
- [x] 创建 `latex/main.tex` 主文件（fduthesis doctor模板）
- [x] 合并参考文献 → `latex/main.bib`（199条）
- [x] 转换6章 draft.md → ch1~ch6 .tex 文件
- [x] 撰写中英文摘要
- [x] 添加已发表论文清单（6篇）
- [x] 修复编译错误（unicode-math冲突、方程格式）
- [x] 调整封面标题宽度（cls修改）
- [ ] 填写论文元信息（导师、学号等，作者已填：万玮霖）
- [ ] 编译测试并修复剩余格式问题

## Phase 5.5 清单（核心章节扩展）
**目标**: 将第3-5章从小论文简略版扩展为包含原论文正文+appendix全部内容的完整大论文
**详细计划**: 见 `todo/2026-03-31-expand-ch3-ch5.md`
**工作流**: 每章按 skeleton → points → draft → LaTeX 四步执行

### 第3章 CADS 扩展
- [ ] 更新 skeleton.md（新增：算法伪代码、损失预测器推导、截断高斯、扩展实验设置、局限性）
- [ ] 更新 points.md
- [ ] 更新 draft.md
- [ ] 更新 ch3-cads.tex

### 第4章 SWaST 扩展
- [ ] 更新 skeleton.md（新增：完整数学框架、算法、RigL/GradMatch介绍、扩展消融、泛化性）
- [ ] 更新 points.md
- [ ] 更新 draft.md
- [ ] 更新 ch4-swast.tex

### 第5章 NAP 扩展
- [ ] 更新 skeleton.md（新增：形式化定义、SNR框架、权重选择、扩展多架构实验、失败案例）
- [ ] 更新 points.md
- [ ] 更新 draft.md
- [ ] 更新 ch5-nap.tex

### 后续工作
- [ ] 填充所有实验表格数据（从原论文tex源码提取）
- [ ] 绘制/导入所有图片
- [ ] 核实所有 [VERIFY] 标注的实验数据

## 待解决问题
- [ ] 与导师确认论文大纲和双主线叙事逻辑
- [x] 确认论文目标字数/页数要求 → 下限约6万字（不含参考文献和附录），不限上限
