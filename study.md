---
layout: default
title: Study
permalink: /study/
---

<style>
.study-nav {
  padding: 12px 0;
  border-bottom: 1px solid #ddd;
  margin-bottom: 24px;
  font-size: 17px;
}

.study-nav a {
  margin-right: 22px;
  text-decoration: none;
  color: #444;
}

.study-nav a.active {
  font-weight: bold;
  color: #111;
}

.study-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 28px;
  margin-top: 30px;
}

.study-card {
  background: #f5f7fb;
  padding: 30px;
  border-radius: 12px;
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  min-height: 180px;
  box-sizing: border-box;
}

.study-card:hover {
  transform: translateY(-6px);
  box-shadow: 0 8px 22px rgba(0, 0, 0, 0.10);
}

.study-card h2 {
  margin-top: 0;
  margin-bottom: 14px;
  color: #0b5cad;
  font-size: 28px;
}

.study-card p {
  margin: 0;
  color: #444;
  line-height: 1.7;
  font-size: 16px;
}

.study-link {
  text-decoration: none;
  color: inherit;
  display: block;
}

@media (max-width: 900px) {
  .study-grid {
    grid-template-columns: 1fr;
  }
}
</style>

<div class="study-nav">
  <a href="/">主页</a>
  <a href="/study/" class="active">学习</a>
</div>

# 学习

这里记录我的学习内容与研究笔记，当前主要分为以下三个方向。

<div class="study-grid">

  <a href="/neural-network/" class="study-link">
    <div class="study-card">
      <h2>神经网络</h2>
      <p>
        包括深度学习基础、Transformer、表示学习、论文阅读与相关方法整理。
      </p>
    </div>
  </a>

  <a href="/computational-biology/" class="study-link">
    <div class="study-card">
      <h2>计算生物</h2>
      <p>
        涵盖单细胞分析、蛋白结构、计算生物相关模型与学习笔记。
      </p>
    </div>
  </a>

  <a href="/probability-statistics/" class="study-link">
    <div class="study-card">
      <h2>概率统计</h2>
      <p>
        整理概率论、统计推断、线性模型、生物统计等基础内容与总结。
      </p>
    </div>
  </a>

</div>
