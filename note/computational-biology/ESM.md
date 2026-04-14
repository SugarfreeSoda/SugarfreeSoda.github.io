---
layout: default
title: ESM
permalink: /notes/esm/
---

<div style="padding: 12px 0; border-bottom: 1px solid #ddd; margin-bottom: 24px;">
  <a href="/" style="margin-right: 22px; text-decoration: none; color: #444;">主页</a>
  <a href="/study/" style="margin-right: 22px; text-decoration: none; color: #444;">学习</a>
  <a href="/paper-study/" style="margin-right: 22px; text-decoration: none; color: #444;">论文学习</a>
  <span style="font-weight: bold; color: #111;">ESM</span>
</div>

# ESM

## 论文信息
- 标题：ESM
- 方向：蛋白语言模型

## 在线阅读

<iframe
  src="/paper/esm.pdf"
  width="100%"
  height="800px"
  style="border: 1px solid #ddd; border-radius: 8px;"
>
</iframe>


## 模型用途

本模型可以通过蛋白氨基酸序列预测蛋白空间结构，主要分为两个模块。

### 1. 隐藏层embedding训练：ESM-2

主要任务是预测蛋白氨基酸序列。

随机 mask 掉一部分（15%）氨基酸，其训练目标可写为：

$$
L = - \sum_{i \in M} \log p(x_i \mid x_{\text{not in } M})
$$

模型本质上是一个 mask language model，底层基于 Transformer。

其中，模型可以通过 attention 学到不同氨基酸位点之间的关联，并进一步构建接触概率矩阵，用来捕捉序列中的长程依赖关系。其背后的生物学直觉是：蛋白序列中往往存在共进化（co-evolution）现象，因此某些位置之间会表现出较强的统计关联。

<div style="text-align: center;">
  <img src="/images/paper/esm/esm1.png" width="500">
</div>
使用UniRef数据库进行模型训练
训练完成后ESM-2提供蛋白序列的embedding

### 2. 结构预测：ESMfold
<div style="text-align: center;">
  <img src="/images/paper/esm/esm2.png" width="500">
</div>


交互式网址：https://forge.biohub.ai/tools/inference

可以尝试以下蛋白序列（绿色荧光蛋白，GFP）：
MSKGEELFTGVVPILVELDGDVNGHKFSVSGEGEGDATYGKLTLKFICTTGKLPVPWPTLVTTLTYGVQCFSRYPDHMKQHDFFKSAMPEGYVQERTIFFKDDGNYKTRAEVKFEGDTLVNRIELKGIDFKEDGNILGHKLEYNYNSHNVYIMADKQKNGIKVNFKIRHNIEDGSVQLADHYQQNTPIGDGPVLLPDNHYLSTQSALSKDPNEKRDHMVLLEFVTAAGITHGMDELYK
