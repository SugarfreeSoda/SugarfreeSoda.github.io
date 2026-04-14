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







