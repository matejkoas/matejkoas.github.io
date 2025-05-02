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
На выходе: SQL-запросы, ER-диаграмма, дашборд Power BI и краткие выводы.

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

ER-диаграмма создана в DBeaver (см. скрин ниже).

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


-- Всего лидов  
```sql
SELECT COUNT(*) AS total_leads FROM leads_qualified;
```

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step1.png" alt="кейсы по Power BI" class="img-fluid">
    </div>


-- Квалифицированные лиды  
```sql
SELECT COUNT(DISTINCT mql_id) AS qualified_leads FROM leads_closed WHERE won_date IS NOT NULL;
```

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case6_step2.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

-- Сделавшие заказы  
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


-- Оплата заказов  
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

### 2. Клиентская аналитика



