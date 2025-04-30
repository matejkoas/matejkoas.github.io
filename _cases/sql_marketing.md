---
layout: case
title: "SQL-кейс: Анализ эффективности маркетинговых каналов"
date: 2025-04-30
tags: [SQL, аналитика, маркетинг]
excerpt: "Кейс демонстрирует владение SQL на примере анализа эффективности источников трафика и поведения пользователей на сайте."
---

## 🎯 Цель

Показать практическое применение SQL для анализа маркетинговых данных: источников трафика, поведения пользователей, устройства, географии и конверсий.

## 📦 Описание данных

В кейсе использовались три таблицы:

- **`sessions`** — данные о сессиях:
  - `session_id`, `client_id`, `utm_source`, `utm_medium`, `device`, `geo_city`, `visit_date`
- **`hits`** — события в рамках сессии:
  - `session_id`, `event_time`, `event_type`, `page_url`
- **`conversions`** — данные о целевых действиях:
  - `session_id`, `conversion_type`, `revenue`

## 🧩 Бизнес-задачи

- Оценить **конверсию** по каналам (`utm_source`, `utm_medium`)
- Определить **города и устройства с лучшими CR**
- Оценить вклад каналов в **выручку**
- Построить SQL-воронку

## 🛠 Примеры SQL-запросов

```sql
-- Конверсия по источникам трафика
SELECT 
  s.utm_source,
  COUNT(DISTINCT s.session_id) AS total_sessions,
  COUNT(DISTINCT c.session_id) AS conversions,
  ROUND(100.0 * COUNT(DISTINCT c.session_id) / COUNT(DISTINCT s.session_id), 2) AS conversion_rate
FROM sessions s
LEFT JOIN conversions c ON s.session_id = c.session_id
GROUP BY s.utm_source
ORDER BY conversion_rate DESC;

-- CR по устройствам
SELECT 
  s.device,
  COUNT(DISTINCT s.session_id) AS total_sessions,
  COUNT(DISTINCT c.session_id) AS conversions,
  ROUND(100.0 * COUNT(DISTINCT c.session_id) / COUNT(DISTINCT s.session_id), 2) AS cr
FROM sessions s
LEFT JOIN conversions c ON s.session_id = c.session_id
GROUP BY s.device
ORDER BY cr DESC;

-- Общая выручка по каналам
SELECT 
  s.utm_source,
  SUM(c.revenue) AS total_revenue
FROM sessions s
JOIN conversions c ON s.session_id = c.session_id
GROUP BY s.utm_source
ORDER BY total_revenue DESC;

## Выводы
SQL-запросы позволяют быстро оценить эффективность каналов и выявить точки роста.

Использование оконных функций и группировок помогает строить метрики без дополнительных инструментов BI.

Такой подход удобен для автоматизации отчетов и A/B-тестов.