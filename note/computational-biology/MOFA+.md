---
layout: default
title: MOFA+
permalink: /notes/MOFA+/
---

<div style="padding: 12px 0; border-bottom: 1px solid #ddd; margin-bottom: 24px;">
  <a href="/" style="margin-right: 22px; text-decoration: none; color: #444;">主页</a>
  <a href="/study/" style="margin-right: 22px; text-decoration: none; color: #444;">学习</a>
  <a href="/paper-study/" style="margin-right: 22px; text-decoration: none; color: #444;">论文学习</a>
  <span style="font-weight: bold; color: #111;">MOFA+</span>
</div>


## 论文信息
- 原始链接：https://link.springer.com/article/10.1186/s13059-020-02015-1
- 发表期刊：Genome Biology
- 方向：多模态统一
- 官网：https://biofam.github.io/MOFA2/

## 在线阅读

<iframe
  src="/paper/MOFA+.pdf"
  width="100%"
  height="800px"
  style="border: 1px solid #ddd; border-radius: 8px;"
>
</iframe>


## 1.aiming problem

MOFA（Multi-Omics Factor Analysis） 需要解决的问题是：

对某一个批次sample（通常研究的是single cell），进行了不同modality（view），可理解为通过不同技术手段获取了同一批次的不同features（组学数据），如：

scRNA-seq， DNA methylation， chromatin accessibility

不同的modality所获得的数据都具有不同的生物学意义，导致在进行后续下游分析的时候无法简单整合

MOFA+ 构建的统计学模型目的就是将多个modality整合在一起，筛选出“共同因子“（factor），类似于在多个不同组学数据重进行“降维”，找到一个可以表征所有数据的共同子空间

这可以看作一个，PCA（或因子分析）拓展到多个modality的拓展应用

模型可视化：

<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+1.png" width="700">
</div>





## 2.数理逻辑



首先对于整个大pipeline，我们最终需要拟合的模型：
$$
Y_{gm}=Z_gW_m^T+\epsilon_{gm}
$$
其中（以下都是矩阵，忽略掉矩阵形状）：

$Y_{gm}$: 第g个group里第m个view的观测值

$Z_g$: 第g个group的因子矩阵

$W_m$: 第m个view的因子质量矩阵

$\epsilon_{gm}$: 第g个group里第m个view的扰动（可以根据不同数据类型选择不同的结构性扰动）


数据模型简单易懂，解决方法采用传统的贝叶斯：


在此先补充两种基于贝叶斯的稀疏特征选择先验：

**Spike and Slab：**

假设我们求解的是一个简单线性回归模型：
$$
y = X\beta + \epsilon
$$
我们选择先验结构:
$$
\beta_j = (1-\pi_j)\delta_0+\pi_jN(0, \tau^2)
$$
也就是我们在某种包含概率之下，把先验采样为spike（尖峰）和slab（厚尾）

在后续贝叶斯迭代过程中，通过MCMC继续学习迭代的参数可以根据需要自行选择

**ARD：**

对于同一个问题，我们选择先验：
$$
\beta_j \sim N(0, (\alpha_j)^{-1})
$$
在后续迭代过程中，形状参数$\alpha$是需要不断迭代学习的参数。在学习过程中我们会决定参数选择逻辑，可以设定threshold，超过某个threshold的变量得到选择，也可使用top-k等等逻辑



回到MOFA+：

### 先验：


**Z先验：**

在这里我们采用双重稀疏性控制：
$$
p(\hat{z}_{nk}^g,s_{nk}^g)=N(\hat{z}_{nk}^g|0, (\alpha_k^g)^{-1})Ber(s_{nk}^g|\theta_k^g)
$$
这里对Z的先验我们使用双重稀疏性控制：Spike and Slab和ARD（Automatic Relevance Determination）自动相关性确认

在Spike and Slab角度：我们以选择概率$\theta_k^g$选择此为非稀疏变量，即先验地选择此因子，以概率$1-\theta_k^g$先验弃用此因子并设置为恒0

在ARD角度：无论是否选择先验选择此因子，我们都对其进行分布控制，给予所有学习参数一个**超先验$\alpha$**（并非超参数而是贝叶斯学习参数，在后续迭代过程中进行学习更新）

<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+2.png" width="600">
</div>

**W先验**

和Z一样采用双重稀疏先验，不多赘述


### 更新

MOFA+采用SVI（stochastic variational inference）更新。首先原本的变分法（VI）在此不过多赘述，简单来说就是我们需要选择拟合不好解析表达的后验，采用期望差以及散度惩罚项找到最合适的参数，也就是我们需要优化的是**某一泛函而非具体参数**

SVI之于VI十分类似SGD（stochastic gradient descent）和GDM（gradient descent with momentum）对于GD（gradient descent）的优化逻辑：通过：

1. 引入保留动量
2. 在小批次上收集信息并处理
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+3.png" width="700">
</div>

其中在这里我们可以看到，我们有两个很基础的假定：



**1.mini-batch的选取非常重要，需要在小区域的情形下和原样本保持一致**

这就导致了一个很尖锐的问题： 要保证SVI的鲁棒性，样本维度比是一个很重要的衡量指标。

单看样本容量这一限制，若在样本容量小的时候，再取小mini-batch进行更新会导致很大程度上放大极端数据带来的影响，使得完全变为噪声主导，模型不可用

再看样本维度比：
$$
\alpha = N/p
$$
在文章【Sharp thresholds for high-dimensional and noisy recovery of sparsity】中，稀疏lasso的支撑集恢复问题若想要达到足够的鲁棒性，需要满足相变点：
$$
N > 2(\theta_u+\nu)log(p-s) + s+1
$$
这也使得这类SSHD（small sample high dimension）数据处理起来十分麻烦且不稳定。

值得一提的是，文章【The Spike-and-Slab Lasso】研发出了基于此的改进方法：spike and slab lasso，通过加入惩罚项的方式有着更强健的鲁棒性。但是在此我们需要谨慎对待验证集和训练集划分问题。在此问题上可以通过重抽样的多模型平均的方法适当缓解。但是它同样存在问题，其**非凸性**可能导致局部最优，因此需配合良好初始化或重复抽样（平均模型法）。



**2.使用的是mean-field VI**

也就是，我们在这里假定所有我们需要优化的参数在某个分布族下是可分离的（弱于独立但是本质上相差无几），这个假定可能本身不成立，即后验分布不一定可以拆为：
$$
E_{f}[q(Z, W, \alpha)] = E_{f}[q(Z)]E_{f}[q(W)]E_{f}[q(\alpha)]
$$
但是这也是通常我们无法避免的假定，若不经此独立性假设，我们难以独立进行取样、拟合计算等，所以可以尝试通过施加结构性正规化等进行一定程度的缓解


对于后验分布：
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+4.png" width="500">
</div>


文中提到，似然也可根据数据不同使用非高斯似然，如伯努利、泊松分布等，但是会加大计算量，MOFA+中采用的是变分下界做近似，具体数学细节不在此过多赘述。

（注：在此如果是高斯似然，由于存在共轭的高斯先验，导致后验也是高斯，可以很好的进行MCMC采样。若非特殊分布族，只能使用计算负担更大的如Metropolis-Hasting采样等。可以使用如引入Poly-Gamma数据增强等方式引入额外变量进行简化计算，相关数据增强有很充分的理论体系）

## 3.相较于MOFA的改进

1. MOFA稀疏性先验仅仅对于矩阵Z，MOFA+将此稀疏先验同时应用于Z，M
2. MOFA不支持多group的情形，在多group的情形下只能同时处理或进行混合（或许可以尝试如现正进行的StablePCA之类的统一方法），但MOFA+可以同时处理组分、模态差异。同时由于支持多group数据，可以在不同group之间有着具有差异化的因子状态。
3. MOFA+使用GPU加速处理
4. MOFA使用VI，MOFA+使用SVI，可加速处理速度（在低batch的情形下还是尽量避免）

独特优势：

1.可处理缺失值多的数据

2.可处理大样本量的数据

3.可处理分组数据
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+5.png" width="700">
</div>


## 4.例子

**1. multiple mouse embryos**

input： 单modality，仅使用scRNA-seq data作为输入
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+6.png" width="500">
</div>


**2.neurons数据**

input：不同区域的mCH和mCG甲基化信号
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+7.png" width="500">
</div>

**3.哺乳动物胚胎分子特征**

input：5 modalities

RNA expression, DNA methylation（甲基化）, chromatin（染色质） accessibility 

3 groups： 不同发育时期
<div style="text-align: center;">
  <img src="/images/paper/MOFA+/MOFA+8.png" width="500">
</div>
可一定程度上识别细胞谱系结构
