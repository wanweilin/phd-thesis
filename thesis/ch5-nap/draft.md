# 第五章 推理阶段的数据相关性判断

## 5.1 章节引言

前两章解决的核心问题属于训练侧：第三章研究如何在计算预算约束下选择最优训练数据，第四章研究数据选择与模型压缩如何协同。训练阶段的数据选择从根本上界定了模型的能力边界——模型只在训练数据所覆盖的分布上可靠。

模型部署后，输入数据的来源不再可控。当输入样本偏离训练数据分布时，模型可能产生过度自信的错误预测\cite{hendrycks2016msp}。这一问题在安全关键应用中尤为危险：自动驾驶系统\cite{filos2020can, janai2020computer}可能对从未见过的路况做出错误判断，医学诊断系统\cite{pooch2020can}可能对训练分布外的病例给出误导性结论。因此，模型需要一个"守门人"机制来判断输入是否处于其有效数据域内。

分布外检测（Out-of-Distribution Detection, OOD Detection）正是这样的守门人机制\cite{yang2021generalized_survey}。从数据选择的统一视角看，OOD检测可被理解为推理时的数据"选择"——选择（接受）属于训练分布内（In-Distribution, ID）的输入，拒绝偏离训练分布的输入。训练时的数据选择界定了模型有效域的边界，推理时的OOD检测则守护这一边界。二者是同一问题在不同阶段的体现。

现有后处理OOD检测方法利用的信号主要来自两个层面：最终输出层（如MSP\cite{hendrycks2016msp}、Energy\cite{liu2020energy}）和全局池化后的中间特征（如ReAct\cite{sun2021react}、DICE\cite{sun2022dice}、ASH\cite{djurisic2022extremely}、KNN\cite{sun2022knn}）。然而，这些方法共享一个信息盲区：全局平均池化将$C \times H \times W$的特征图压缩为$C$维向量，丢失了通道内的空间激活分布信息。图5-1展示了这一信息丢失。

<!-- [图5-1: 全局池化导致通道内激活分布信息丢失的示意——ID和OOD样本的激活图对比（对应原论文Fig.1）。需手绘。] -->

本章提出神经激活先验（Neural Activation Prior, NAP），利用这一被忽视的池化前通道内信息，提供与现有方法正交的新检测信号。图5-2展示了NAP评分的区分效果：单独使用时NAP即具有区分能力，与现有方法组合后区分度进一步增强。

<!-- [图5-2: NAP评分的区分效果——(a) Energy Score分布；(b) NAP Score分布；(c) Energy × NAP组合分布，对比ID与OOD（对应原论文Fig.2）。需手绘。] -->

## 5.2 基础知识

### 5.2.1 问题定义

给定在ID数据集$\mathcal{D}_{\text{in}}$上训练的分类器$f$，OOD检测的目标是设计评分函数$S(x; f)$，使得ID样本的得分系统性地高于OOD样本（$S(x_{\text{in}}) > S(x_{\text{out}})$）。通过阈值$\tau$实现判别：$S(x) \geq \tau$则接受输入，否则拒绝。

本文聚焦后处理方法（post-hoc methods）——不修改模型训练过程，不需要OOD数据参与训练，仅利用已训练模型在推理时的输出设计评分函数。这一约束具有重要的实际意义：部署后的模型通常不允许为检测需求而重新训练。

### 5.2.2 评价指标

OOD检测的两个主要评价指标为：

**FPR95（False Positive Rate at 95% True Positive Rate）**：在保证95%的ID样本被正确接受的前提下，误接受OOD样本的比例。越低越好。FPR95在安全关键应用中尤为重要——高真阳性率（TPR）是刚需（不能拒绝太多正常输入），因此在固定高TPR下的假阳性率更具实际意义。

**AUROC（Area Under the Receiver Operating Characteristic curve）**：综合衡量在不同阈值下的ID/OOD区分能力。越高越好。AUROC提供了不依赖特定阈值的全局性能评估。

## 5.3 神经激活先验

### 5.3.1 核心观察

NAP的观察对象是全连接层前的特征图，形状为$C \times H \times W$（$C$个通道，每个通道是$H \times W$的空间特征图）。图5-3展示了NAP在CNN和Transformer中的关注区域。

<!-- [图5-3: NAP在分类神经网络中的关注区域示意——(a) CNN中的NAP位置；(b) Transformer中的NAP位置（对应原论文Fig.3）。需手绘。] -->

核心发现如下：对于ID样本，每个通道内仅少量空间位置具有显著高于均值的激活值——呈现**尖锐激活模式**；对于OOD样本，通道内的激活更加均匀分散，缺乏尖锐响应。为量化这一差异，定义两个逐通道统计量：

$$\text{Max}(A_j) = \max_{k,l} A_{jkl}, \quad \text{Mean}(A_j) = \frac{1}{HW}\sum_{k,l} A_{jkl}$$

其中$A_{jkl}$为第$j$个通道在空间位置$(k,l)$的激活值。ID样本的$\text{Max}/\text{Mean}$比值显著高于OOD样本。

### 5.3.2 直觉解释

为何ID样本产生尖锐激活？训练过程使得每个通道的卷积滤波器学会检测特定的视觉模式\cite{zeiler2014visualizing, yosinski2015understanding_visual}（边缘、纹理、物体部件等）。ID样本包含这些训练所学的模式，在对应的空间位置触发强响应，其余位置保持低激活。

为何OOD样本激活更分散？OOD样本不包含训练数据中的典型视觉模式。滤波器无法找到精准匹配，激活值在空间上更均匀分布且整体较弱——滤波器对每个位置都给出"不确定"的弱响应，而非在特定位置给出确信的强响应。

这一先验是训练的**自然副产品**：不需要任何额外训练或OOD数据的参与，在标准训练的模型中天然存在。

### 5.3.3 层级选择

NAP的检测信号强度与特征层的深度密切相关。浅层特征（如第一个卷积层、早期模块）捕获低级视觉特征（边缘、颜色、简单纹理），这些基础特征为ID和OOD样本所共享，因此区分度低。深层特征（全连接层前的倒数第二层）捕获高级语义特征，这些特征对训练数据分布高度特异——模型已学会"只对训练类别产生尖锐响应"。

图5-4展示了DenseNet\cite{huang2017densely}不同层上ID与OOD样本激活分布的对比，验证了ID/OOD区分度随网络深度单调递增 [VERIFY: 层级分析实验]。

<!-- [图5-4: DenseNet不同层的激活分布对比——ID数据（CIFAR-10）与OOD数据（Places365）在各层的Max与Mean激活分布（对应原论文Fig.4）。需手绘。] -->

### 5.3.4 向Transformer的扩展

Vision Transformer\cite{dosovitskiy2020image}中没有传统的全局池化层，但NAP的思想可以迁移：cls token对其他token的注意力权重反映了模型对不同输入区域的"关注集中度"。

ID样本的注意力更加集中——少数token获得高注意力权重，表明模型找到了明确的目标区域；OOD样本的注意力更加分散——权重在各token间近似均匀分布，表明模型无法找到焦点。

由于注意力权重经softmax归一化后均值为固定常数$1/(l+1)$（$l$为token数量），NAP在Transformer中简化为关注最大注意力权重$\text{Max}(A)$——Mean的归一化作用已被softmax隐式完成。

## 5.4 方法：基于NAP的评分函数设计

### 5.4.1 CNN评分函数

受信噪比（Signal-to-Noise Ratio, SNR）的启发，NAP的CNN评分函数定义为：

$$S_{\text{NAP}}(x; f) = \frac{1}{C}\sum_{j=1}^{C}\left(\frac{\text{Max}(A_j)}{\text{Mean}(A_j) + \epsilon}\right)^2$$

其中$\text{Max}$作为"信号"——目标模式的响应强度，$\text{Mean}$作为"噪声"——背景激活水平。$\epsilon = 1.0$保证数值稳定性。

设计动机如下：不直接使用$\text{Max}$是因为不同通道的激活尺度差异很大，直接取$\text{Max}$会被高激活尺度通道主导。除以$\text{Mean}$实现逐通道归一化，使所有通道的贡献更加均衡。取平方是为了将$\text{Max}/\text{Mean}$比值在ID和OOD之间的差异非线性放大，提高区分度。跨通道取平均聚合所有通道的信息，减少单个通道的随机波动。

### 5.4.2 Transformer评分函数

在Vision Transformer上，NAP评分简化为：

$$S_{\text{NAP}}^{\text{Former}} = \text{Max}(A)$$

即取最终Transformer块中cls token注意力权重的最大值。简化的原因是：注意力权重经softmax归一化后，均值为固定常数$1/(l+1)$，SNR形式的$\text{Max}/\text{Mean}$退化为$\text{Max}$的常数倍。

### 5.4.3 与现有方法的组合

NAP设计为即插即用的评分函数，可与现有OOD检测方法通过加权几何均值组合：

$$S_{\text{NAP-}\star}(x; f, w) = -S_{\star}(x; f)^w \cdot S_{\text{NAP}}(x; f)^{1-w}$$

其中$\star$代表MSP、Energy、ReAct等任意现有方法，$w \in [0,1]$为权重参数。

选择几何均值而非线性组合的原因是：两个评分的量纲和数值范围可能差异很大，几何均值对这种尺度差异更为鲁棒。

**正交互补性。** NAP与现有方法的组合有效性源于信息来源的正交：NAP利用池化前的通道内分布信息（空间维度），现有方法利用池化后的全局特征或输出层信息（通道维度）。二者捕获不同层面的ID/OOD差异 [VERIFY: 组合提升的具体数字]。

权重参数$w$的优化通过数据增强构造伪OOD样本\cite{du2022vos}，用二分搜索确定最优$w$值，无需真实OOD数据的参与。

## 5.5 实验

### 5.5.1 实验设置

**ID数据集：** CIFAR-10\cite{krizhevsky2009cifar}、CIFAR-100\cite{krizhevsky2009cifar}和ImageNet-1K\cite{deng2009imagenet}。

**OOD数据集：** CIFAR基准使用SVHN\cite{netzer2011svhn}、Textures\cite{cimpoi2014texture}、iSUN\cite{xu2015isun}、LSUN（Crop/Resize）\cite{yu2015lsun}和Places365\cite{zhou2017places}；ImageNet基准使用iNaturalist\cite{van2018inaturalist}、SUN\cite{xiao2010sun}、Places\cite{zhou2017places}和Textures\cite{cimpoi2014texture}。

**网络架构：** DenseNet\cite{huang2017densely}、ResNet-50\cite{he2016deep}、MobileNetV2\cite{sandler2018mobilenetv2}、VGG-16\cite{simonyan2015very}（CNN）；ViT-B/16\cite{dosovitskiy2020image}（Transformer）。

**基线方法：** MSP\cite{hendrycks2016msp}、Energy\cite{liu2020energy}、ODIN\cite{liang2018odin_confi}、ReAct\cite{sun2021react}、DICE\cite{sun2022dice}、ASH\cite{djurisic2022extremely}、KNN\cite{sun2022knn}、Mahalanobis\cite{lee2018maha_dist}。

**NAP组合变体：** NAP-M（+MSP）、NAP-E（+Energy）、NAP-R（+ReAct）、NAP-D（+DICE）、NAP-A（+ASH）、NAP-K（+KNN） [VERIFY: 完整设置]。

### 5.5.2 CIFAR基准结果

表5-1展示了CIFAR-10和CIFAR-100基准上NAP与各后处理方法的组合效果。

<!-- [表5-1: CIFAR-10和CIFAR-100基准上NAP与各后处理方法的组合效果（对应原论文Table 1）。] -->

在CIFAR-10上，NAP-K将平均FPR95从15.05%降至7.79%；NAP-E实现了最大��对降幅66.03% [VERIFY: 详细数据]。在CIFAR-100上，NAP-R将平均FPR95从41.40%降至25.71%，最大相对降幅达58.71% [VERIFY]。

一个值得注意的观察是：NAP的提升在不同OOD数据集上并不均匀。对Textures等纹理类OOD数据集的改善最为显著——这类数据在池化后的全局特征上与ID数据难以区分（因为纹理在每个通道上的平均激活可能与ID数据相似），但在通道内的空间激活模式上差异显著（纹理产生均匀分散的激活，而ID物体产生局部尖锐的激活）。这一观察直接印证了NAP利用池化前信息的价值。

### 5.5.3 ImageNet基准结果

表5-2展示了在大规模ImageNet-1K基准上的验证结果。

<!-- [表5-2: ImageNet-1K基准上NAP与各方法在四个OOD数据集上的结果（对应原论文Table 2）。] -->

NAP-A（与ASH组合）将平均FPR95从35.66%降至29.86% [VERIFY: 详细数据]。在Textures和iNaturalist上的改善最为显著，与CIFAR基准上的观察一致。ImageNet实验使用了四种不同的网络架构（ResNet-50、MobileNetV2\cite{sandler2018mobilenetv2}、VGG-16\cite{simonyan2015very}、DenseNet\cite{huang2017densely}），验证了NAP在不同架构上的稳健性。

### 5.5.4 Transformer实验

表5-3展示了ViT-B/16\cite{dosovitskiy2020image}上的OOD检测结果。

<!-- [表5-3: ViT-B/16上NAP与Energy、MSP基线的OOD检测结果（对应原论文Table 3）。] -->

NAP-M将FPR95从61.72%（MSP）降至54.40% [VERIFY]。这一结果验证了NAP的核心思想——激活集中度作为ID/OOD检测信号——从CNN的空间激活到Transformer的注意力权重，具有架构迁移性。

### 5.5.5 层级分析

在DenseNet的不同层（第一卷积层、transition block 1-2、倒数第二层）分别计算NAP评分。结果表明，ID/OOD区分度随网络深度单调递增，倒数第二层效果最好——浅层特征对ID/OOD几乎无区分力（因为共享低级视觉特征），而深层特征高度特异于训练分布 [VERIFY: 层级分析图]。

## 5.6 章节小结

本章发现并形式化了神经激活先验（NAP）：在全局池化前的特征图中，ID样本在各通道内呈现尖锐的空间激活模式（少量位置产生���激活），而OOD样本的激活更为分散。这一先验是标准训练的自然副产品，被现有OOD检测方法完全忽视。

基于NAP，本文设计了即插即用的评分函数，同时适用于CNN（SNR形式的$\text{Max}^2/\text{Mean}^2$）和Vision Transformer（最大注意力权重）。NAP与现有方法通过加权几何均值实现正交互补——利用信息来源的互补性（池化前通道内信息 vs. 池化后全局信息），在CIFAR和ImageNet基准上与多种基线方法组合后均实现显著提升，FPR95最高降低66.03% [VERIFY]。

至此，三章工作完成了对数据选择问题的双重覆盖：阶段上从训练侧（第三、四章）延伸到推理侧（第五章），交互维度上从计算资源（第三章）到模型容量（第四章）再到推理时分布偏移（第五章）。第六章将从这两个视角统一回顾三项工作的贡献与局限，并展望未来研究方向。
