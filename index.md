---
layout: default
title: Аналитика данных - сайт портфолио
---

<style>
    .case-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
  margin-top: 2rem;
}

.case-card {
  background: white;
  padding: 1rem;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
  text-align: center;
  text-decoration: none;
}

.case-card img {
  max-width: 100%;
  height: auto;
  border-radius: 8px;
}

.case-card h3 {
  margin-top: 1rem;
}

.case-card p {
  font-size: 0.9rem;
  color: #555;
}


</style>


<div class="case-grid">
  <div class="case-card">
    <a href="/porfolio/cases/bi_case">
      <img src="assets/images/bi_img.png" alt="BI Проект 1">
      <h3>Аналитика Power BI. Анализ эффективности рекламы Яндекс.Директ </h3>
      <p><strong>Стек:</strong> Power BI, DAX, М, Python</p>
      <p>Нахождение точек роста в рекламе Яндекс.Директ по продаже межкомнатных дверей на основе анализа данных за длительный период работы.</p>
    </a>
  </div>

  <div class="case-card">
    <a href="/porfolio/cases/sports-goods">
      <img src="assets\images\sports_clustering3.png" alt="Python Проект 1">
      <h3>Анализ продаж на python c кластеризацией и моделированием</h3>
    </a>
    <p><strong>Стек:</strong> Python, Jupyter Notebook, Pandas, sklearn, scipy</p>
    <p>Кластеризация данных с моделированием вероятности покупки. Очистка данных. Восстановление данных</p>
  </div>


</div>