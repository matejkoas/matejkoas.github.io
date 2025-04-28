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

<h2>Мои кейсы</h2>

<div class="case-grid">

  <div class="case-card">
    <a href="/cases/bi_case/">
      <img src="assets/images/bi_img.png" alt="BI Проект 1">
      <h3>Аналитика Power BI. Анализ эффективности рекламы Яндекс.Директ </h3>
        <p><strong>Стек:</strong> Power BI, DAX, М, Python</p>
        <p>Построение отчёта по продажам для маркетплейса: визуализация динамики продаж, выявление топ-товаров и категорий.</p>
      </a>
  </div>

  <div class="case-card">
    <a href="/cases/python_project1">
      <img src="/assets/images/python_project1.png" alt="Python Проект 1">
      <h3>Парсер отзывов на Python</h3>
    </a>
    <p><strong>Стек:</strong> Python, BeautifulSoup, Pandas</p>
    <p>Сбор отзывов с сайтов, очистка текста и анализ частотности ключевых слов для выявления потребностей клиентов.</p>
    </a>
  </div>

  <div class="case-card">
    <a href="/cases/sql_project1">
      <img src="/assets/images/sql_project1.png" alt="SQL Проект 1">
      <h3>Оптимизация запросов в PostgreSQL</h3>    
        <p><strong>Стек:</strong> PostgreSQL, индексация, CTE</p>
        <p>Оптимизация выборок в аналитической базе: сокращение времени выполнения сложных отчётных запросов на 40%.</p>
    </a>
  </div>

</div>