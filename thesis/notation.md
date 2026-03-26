# 统一符号表

本文档梳理三篇小论文中使用的符号，确定博士论文中的统一符号方案。

---

## 通用符号

| 统一符号 | 含义 | 备注 |
|----------|------|------|
| $\boldsymbol{\theta}$ | 模型参数 | 三篇论文一致 |
| $\mathcal{D}$ | 完整训练数据集 | 三篇论文一致 |
| $\mathcal{L}$ | 损失函数（通用） | 三篇论文一致 |
| $f$ | 神经网络模型 | 三篇论文一致 |
| $x$, $y$ | 输入样本、标签 | 三篇论文一致 |

---

## 第三章 CADS 专用符号

| 符号 | 含义 | 原论文来源 |
|------|------|-----------|
| $\bm{m} \in \{0,1\}^N$ | 二值数据选择向量（样本掩码） | 论文二 |
| $\bm{s}$ | 可学习的采样分布参数 | 论文二 |
| $C$ | 计算预算（前向-反向传播总步数） | 论文二 |
| $\bm{\theta}_C(\bm{m})$ | 在预算 $C$ 下用子集 $\bm{m}$ 训练得到的模型参数 | 论文二 |
| $\mathcal{L}_{\mathrm{val}}$ | 验证集损失 | 论文二 |
| $\mathcal{L}_{\mathrm{trn}}$ | 训练集损失 | 论文二 |
| $p(\bm{m} \mid \bm{s})$ | 采样分布 | 论文二 |
| $s_i$ | 第 $i$ 个样本的纳入概率 | 论文二 CADS-E |
| $\bm{r} \in [0,1]^n$ | 来源级采样比例向量 | 论文二 CADS-S |
| $\{\mathcal{D}_i\}_{i=1}^n$ | 数据源划分 | 论文二 CADS-S |
| $\sigma$ | 截断高斯分布的尺度参数 | 论文二 CADS-S |
| $\alpha$ | 惩罚项系数 | 论文二 |
| $l(\cdot)$ | 训练损失的尺度依赖代理函数 | 论文二 |
| $K$ | Monte-Carlo 采样数（bilevel）/ 训练步数（penalty） | 论文二（注意上下文区分） |
| $\bm{u}$ | 辅助变量（代理充分训练参数） | 论文二 |

---

## 第四章 SWaST 专用符号

| 符号 | 含义 | 原论文来源 |
|------|------|-----------|
| $\hat{\mathcal{D}}$ | 选出的核心子集（coreset） | 论文三 |
| $\tilde{\mathcal{D}}$ | 存储的 logits 集合（状态保护） | 论文三 |
| $\mathcal{X}$ | 当前训练使用的数据子集 | 论文三 |
| $\mathcal{K}$ | 热身（warm-up）训练的 epoch 数 | 论文三 |
| $\mathcal{R}$ | 执行 coreset 选择的 epoch 间隔 | 论文三 |
| $\alpha$ | coreset 占全集的比例 | 论文三 |
| $\mathcal{P}$ | 剪枝算法 | 论文三 |
| $\mathcal{S}$ | coreset 选择算法 | 论文三 |
| $\lambda$ | 状态保护损失的权重（默认0.1） | 论文三 |
| $\mathcal{L}_{\text{CE}}$ | 交叉熵损失 | 论文三 |
| $\mathcal{L}_{\text{SP}}$ | 状态保护损失（KL散度） | 论文三 |
| $\mathcal{L}_{\text{total}}$ | 总损失 = $\mathcal{L}_{\text{CE}} + \lambda \mathcal{L}_{\text{SP}}$ | 论文三 |
| $\mathbf{z}_i$ | 保存的 logits：$\mathbf{z}_i = f_{\boldsymbol{\theta}_{\text{pre}}}(\mathbf{x}_i)$ | 论文三 |
| $\boldsymbol{\theta}_{\text{pre}}$ | 剪枝/选择前的模型参数（用于状态记录） | 论文三 |
| $\mathcal{I}(\mathcal{D}, \hat{\mathcal{D}})$ | coreset 选择难度指标 | 论文三 |
| $\ell(\boldsymbol{\theta}; x_i, y_i)$ | 单样本损失函数 | 论文三 |
| $\epsilon$ | 近似误差上界 | 论文三 |

---

## 第五章 NAP 专用符号

| 符号 | 含义 | 原论文来源 |
|------|------|-----------|
| $\mathbf{A}$ | 全局池化层前的激活张量，维度 $C \times H \times W$ | 论文一 |
| $\mathbf{A}_j$ | 第 $j$ 个通道的激活张量 | 论文一 |
| $C$ | 通道数 | 论文一 |
| $H$, $W$ | 激活图的空间维度 | 论文一 |
| $D$ | 输入维度 | 论文一 |
| $K$ | 输出类别数（logits维度） | 论文一 |
| $\text{Max}(\mathbf{A}_j)$ | 第 $j$ 通道的最大激活值 | 论文一 |
| $\text{Mean}(\mathbf{A}_j)$ | 第 $j$ 通道的平均激活值 | 论文一 |
| $S_{\text{NAP}}(x; f)$ | NAP 评分函数（CNN） | 论文一 |
| $S_{\text{NAP}}^{\text{Former}}$ | NAP 评分函数（Transformer） | 论文一 |
| $E(x; f)$ | 能量评分函数 | 论文一 |
| $S_{\text{NAP-E}}(x; f, w)$ | NAP与能量评分的加权几何均值组合 | 论文一 |
| $w$ | 组合权重参数 | 论文一 |
| $\epsilon$ | 数值稳定性常数 | 论文一 |

---

## 符号冲突与解决方案

| 冲突符号 | 论文二 (CADS) 含义 | 论文三 (SWaST) 含义 | 论文一 (NAP) 含义 | 统一方案 |
|----------|-------------------|-------------------|------------------|----------|
| $\alpha$ | 惩罚项系数 | coreset比例 | — | 各章保留原义，在章节首次使用时重新定义。CADS中用 $\alpha_{\text{penalty}}$，SWaST中用 $\alpha_{\text{ratio}}$ 以示区分 |
| $C$ | 计算预算 | — | 通道数 | CADS中保留 $C$ 表示计算预算；NAP中改用 $N_c$ 表示通道数 |
| $K$ | MC采样数/训练步数 | — | 输出类别数 | CADS中保留 $K$；NAP中改用 $N_{\text{cls}}$ 表示类别数 |
| $\epsilon$ | — | 近似误差上界 | 数值稳定常数 | SWaST中保留 $\epsilon$；NAP中用 $\epsilon_0$ 表示数值稳定常数 |
| $\sigma$ | 截断高斯尺度 | — | softmax 函数 | CADS中保留 $\sigma$ 表示尺度参数；SWaST中改用 $\text{softmax}(\cdot)$ 替代 $\sigma(\cdot)$ |

---

## 备注

- 各章方法介绍时，在首次出现处以**定义环境**或**段首黑体**重新定义所有符号
- 跨章引用时使用统一后的符号名称
- 本表将在写作过程中持续更新
