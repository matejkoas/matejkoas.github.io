---
layout: case
title: "BI-исследование ecommerse проекта"
date: 2025-05-02
categories: [portfolio, SQL, powerbi]
tags: [SQL, Power BI, аналитика, e-commerce, кейс]
noindex: true
---

## 📋 Техническое задание

**Цель:**  
Понять поведение клиентов, построить воронку продаж, определить ключевые метрики, влияющие на выручку.  
На выходе: подключение базы SQLlyte к Power BI. Настройка связей. Нахождение показателей. Построение визуализации в Power BI.

---

## 🧩 Структура базы данных

База содержит следующие таблицы:

- `customers`
- `leads_qualified` (лиды верхнего уровня воронки, подтвержденные и неподтвержденные)
- `leads_closed` (подтвержденные лиды)
- `orders`
- `order_items`
- `order_payments`
- `order_reviews`
- `products`
- `product_category_name_translations` 
- `sellers`

## 📊 Аналитика по шагам

### 1. Подключение БД к Power BI с помощью Python-скрипта.

```python
import sqlite3
import pandas as pd

# Подключаем базу SQLlyte к Power BI:
conn = sqlite3.connect(r"C:\Users\user\Documents\sql cases\archive\olist.sqlite")

# Загружаем датафреймы таблиц
orders = pd.read_sql_query("SELECT * FROM orders", conn)
customers = pd.read_sql_query("SELECT * FROM customers", conn)
order_items = pd.read_sql_query("SELECT * FROM order_items", conn)
order_reviews = pd.read_sql_query("SELECT * FROM order_reviews", conn)
order_payments = pd.read_sql_query("SELECT * FROM order_payments", conn)
leads_qualified = pd.read_sql_query("SELECT * FROM leads_qualified", conn)
leads_closed = pd.read_sql_query("SELECT * FROM leads_closed", conn)
sellers = pd.read_sql_query("SELECT * FROM sellers", conn)
product_category_name_translation = pd.read_sql_query("SELECT * FROM product_category_name_translation", conn)
products = pd.read_sql_query("SELECT * FROM products", conn)

conn.close()
```

После импортирования таблиц в Power BI связи между таблицами расставились автоматически, но после проверки частично были откорректированы - заменен тип связи кое-где вместо "1 к 1" - "1 ко многим".

   <div class="case-image">
      <img src="{{ site.baseurl }}/assets/images/case7_1.png" alt="кейсы по Power BI" class="img-fluid">
    </div>

### 2. Создание мер.
<br>
--- Количество лидов

leads_qualified - таблица, содержащая лиды верхнего уровня воронки с указанием источника трафика.
Мера, считающая количество входяших лидов:

```sql
Leads = COUNTROWS(leads_qualified)
```
<br>
--- Количество заказов
```sql
Count of orders = COUNT(orders[order_id])
```
<br>
--- Конверсия из лида в подтвержденный лид
```sql
CR_to_won_Leads = DIVIDE(COUNTROWS(leads_qualified), COUNTROWS(leads_closed), 0) 
```
<br>
--- Конверсия из подтвержденного лида в заказ
```sql
CR_wonlead_to_order = DIVIDE(COUNTROWS(leads_closed), COUNTROWS(orders), 0)
```
<br>
--- Конверсия из неподтвержденного лида в заказ
```sql
CR_lead_to_order = DIVIDE(COUNTROWS(orders), COUNTROWS(leads_qualified), 0)
```








