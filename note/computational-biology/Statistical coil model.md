---
layout: default
title: Statistical coil model
permalink: /notes/statistical-coil-model/
---

<div style="padding: 12px 0; border-bottom: 1px solid #ddd; margin-bottom: 24px;">
  <a href="/" style="margin-right: 22px; text-decoration: none; color: #444;">主页</a>
  <a href="/study/" style="margin-right: 22px; text-decoration: none; color: #444;">学习</a>
  <a href="/paper-study/" style="margin-right: 22px; text-decoration: none; color: #444;">论文学习</a>
  <span style="font-weight: bold; color: #111;">Statistical coil model</span>
</div>



## 论文信息
- 原始链接：https://www.pnas.org/doi/abs/10.1073/pnas.0506078102
- 发表期刊：PNAS
- 方向：蛋白结构统计量化

## 在线阅读

<iframe
  src="/paper/Statistical_coil_model.pdf"
  width="100%"
  height="800px"
  style="border: 1px solid #ddd; border-radius: 8px;"
>
</iframe>




## MOTIVATION:
构建了一个“统计线团模型（statistical coil model）”，用于刻画蛋白质“未折叠态（unfolded state）”的真实结构，并解释实验中的矛盾现象



## 参照数据库：
文章首先通过筛选原始数据库吗，构建出未折叠蛋白的结构集合

数据来源：https://www.rcsb.org/#Category-download

使用PISCES数据库筛选工具，选择：分辨率优于 2 Å，R factor ≤ 0.3的共 2020 条蛋白链，从中提取coil library（无规则结构库）， 只保留不属于 α-helix、β-sheet、turn 的且连续 ≥ 4 个残基区，并去掉靠近结构区的残基（避免边界效应），最后根据每个氨基酸残基在上述数据库的统计频率，将其分配到5个Ramachandran basins中




## 随机采样构象：

蒙特卡洛采样，总能量函数为：

$$
U_{total} = \sum_{i}U(a_i, b_i) +\sum_{i}\delta U(a_i, b_i, a_{i+1}, b_{i+1}) 
\\
U(a_i, b_i) = -RT\ln P(a_i, b_i)
\\
\delta U(a_i, b_i, a_{i+1}, b_{i+1})  = -RT \ln \frac{P(a_i, b_i, a_{i+1}, b_{i+1}) }{P(a_i, b_i) P( a_{i+1}, b_{i+1})}
$$

其中$\delta U(a_i, b_i, a_{i+1}, b_{i+1})$是互信息， $RT$为参数, $a_i$为第 i 个氨基酸类型， $b_i$为构象区域（Ramachandran区域）

根据此选择完所有构象区域后接下来构造旋转角，每个残基对应的具体主链角度（φ, ψ）会从coil library中选取，该过程不考虑邻近残基效应（NN effects）

为了消除空间上的重叠（steric overlap），我们在每个氨基酸所属的构象区域内，对 φ、ψ 角进行微调。这是通过最小化一个简单的**排除体积（excluded volume）能量函数**来实现的，该函数用于描述构象区域内部的结构优化（intrabasin relaxation）

最后，通过使用 SCWRL和 REDUCE分别为链骨架添加侧链和氢原子。

## 检测构象合理性：

酰胺氮原子和质子之间的RDC（残余偶极耦合）可以计算为：
$$
D_{NH}=D_{max}<P_2(cos \theta)>
\\
P_2(x) = \frac{3x^2 - 1}{2}
$$
$P_2$为二阶勒让德多项式，描述方向各向异性

对整个ensemble取平均RDC可以进行更客观的评估

还可以评估ensemble的大小：
$$
R_g = a N^b
$$
折叠蛋白一般b为1/3， ideal chain一般为1/2， 随机线团一般为0.6，可以通过计算结果评估模拟合理性



（！！！论文代码源头网站已失效，谨慎参考）

