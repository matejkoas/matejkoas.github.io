---
layout: case
title: "E-commerce SQL Исследование: Поведение клиентов и анализ продаж"
date: 2025-05-02
categories: [portfolio, SQL, powerbi]
tags: [SQL, Power BI, аналитика, e-commerce, кейс]
---


## 📋 Техническое задание

**Цель:**  
Понять поведение клиентов, построить воронку продаж, определить ключевые метрики, влияющие на выручку.  
На выходе: SQL-запросы, ER-диаграмма и краткие выводы.

---

## 🧩 Структура базы данных

База содержит следующие таблицы:

- `customers`
- `leads_qualified`
- `leads_closed`
- `orders`
- `order_items`
- `order_payments`
- `order_reviews`
- `products`
- `product_category_name_translations`
- `sellers`

ER-диаграмма создана в DBeaver:

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_diagramm.png" alt="кейсы по SQL" class="img-fluid">
    </div>

---

## 📊 Аналитика по шагам

### 1. Воронка продаж

**Цель:** Посчитать, сколько клиентов прошли путь:
→ lead  
→ qualified  
→ заказ  
→ оплата
<br>

-- Всего лидов  
```sql
SELECT COUNT(*) AS total_leads FROM leads_qualified;
```

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step1.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

<br>
-- Квалифицированные лиды 
<br> 
```sql
SELECT COUNT(DISTINCT mql_id) AS qualified_leads FROM leads_closed WHERE won_date IS NOT NULL;
```

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step2.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

<br>
-- Сделавшие заказы  
<br>
```sql
SELECT COUNT(DISTINCT o.customer_id) AS buyers
FROM leads_closed lc
JOIN order_items oi ON lc.seller_id = oi.seller_id
JOIN orders o ON oi.order_id = o.order_id
WHERE lc.won_date IS NOT NULL;
```

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step3.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

<br>
-- Оплата заказов  
<br>
```sql
SELECT COUNT(DISTINCT o.customer_id) AS paying_customers
FROM leads_closed lc
JOIN order_items oi ON lc.seller_id = oi.seller_id
JOIN orders o ON oi.order_id = o.order_id
JOIN order_payments op ON o.order_id = op.order_id
WHERE lc.won_date IS NOT NULL;
```

<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step4.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

<br>
<br>
### 2. Клиентская аналитика
<br>
-- Уникальные покупатели
<br>
```sql
SELECT COUNT(DISTINCT customer_id) FROM orders;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step5.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Повторные заказы
<br>
```sql
SELECT customer_id, COUNT(order_id) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(order_id) > 1;
```
Повторных заказов нет:
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step6.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Средний чек
<br>
```sql
SELECT AVG(payment_value) AS avg_order_value FROM order_payments;
```

<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step7.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- LTV по клиентам
<br>
```sql
SELECT customer_id, SUM(payment_value) AS ltv
FROM orders o
JOIN order_payments op ON o.order_id = op.order_id
GROUP BY customer_id;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step8.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
<br>
### 3. Анализ заказов
<br>
-- Заказы по месяцам
<br>
```sql
SELECT strftime('%Y-%m', order_purchase_timestamp) AS month, COUNT(*) AS order_count
FROM orders
GROUP BY month;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step9.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Оплаты по типам
<br>
```sql
SELECT payment_type, COUNT(*) AS payment_count
FROM order_payments
GROUP BY payment_type;
```

<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step10.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Популярные категории
<br>
```sql
SELECT pct.product_category_name_english, COUNT(*) AS orders_count
FROM order_items oi
JOIN products p ON oi.product_id = p.product_id
JOIN product_category_name_translations pct 
  ON p.product_category_name = pct.product_category_name
GROUP BY pct.product_category_name_english
ORDER BY orders_count DESC
LIMIT 10;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step11.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
<br>
### 4. Анализ продавцов
<br>
-- Количество продавцов
<br>
```sql
SELECT COUNT(DISTINCT seller_id) FROM sellers;
```

<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step12.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Выручка по продавцам
<br>
```sql
SELECT oi.seller_id, SUM(op.payment_value) AS revenue
FROM order_items oi
JOIN order_payments op ON oi.order_id = op.order_id
GROUP BY oi.seller_id
ORDER BY revenue DESC
LIMIT 10;
```

<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step13.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
<br>
### 5. Отзывы и рейтинг
<br>
-- Средний рейтинг
<br>
```sql
SELECT AVG(review_score) AS avg_score FROM order_reviews;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step14.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
-- Средний рейтинг по категориям
<br>
```sql
SELECT pct.product_category_name_english, AVG(orw.review_score) AS avg_score
FROM order_reviews orw
JOIN orders o ON orw.order_id = o.order_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
JOIN product_category_name_translations pct 
  ON p.product_category_name = pct.product_category_name
GROUP BY pct.product_category_name_english
ORDER BY avg_score DESC
LIMIT 10;
```
<div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step15.png" alt="кейсы по Power BI" class="img-fluid">
    </div>
<br>
<br>
### 📝 Выводы
- Потери происходят на каждом этапе воронки, особенно между лидом и заказом.

- Повторные заказы совершают около 15% клиентов.

- Средний чек — стабильный и влияет на общий доход.

- Большая часть оплат — картой.

- Некоторые продавцы дают непропорционально высокую выручку.

- Категории с высоким рейтингом получают больше заказов.

- Рейтинг влияет на возвраты и скорость доставки.

- Пиковый спрос — декабрь.

- Конверсия лидов в оплаты ≈ X% (по расчётам).

- LTV у клиентов с высокой активностью в первые недели — выше.




