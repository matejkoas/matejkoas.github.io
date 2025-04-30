---
layout: case
title: "Аналитика Python. Анализ веб-сайта и его трафика"
description: "Анализ веб-сайта и его трафика с использованием Python, Pandas, NumPy, SciPy, Seaborn и Matplotlib."
date: 2023-02-20
categories: python
---

#### Стек:
- Jupyter Notebook
- Python
- Pandas
- numpy 
- scipy 
- seaborn
- matplotlib

## Краткое описание решенной задачи:
- Исследована выгрузка сырых данных из Google Analytics.
- Дан ответ на вопрос: Где больше конверсия - по органическому или платному трафику?
- Выяснено, в каких регионах конверсия выше. В Москве и Санкт-Петербурге или в других регионах?

## 1. Data Understanding
Исходная информация представляла из себя выгрузку из Google Analytics по хитам и по сессиям. Эти две выгрузки содержались в двух больших файлах.
### Загрузка датасетов

```python
<pre class="bg-dark text-white p-3 rounded">
    <code class="language-python">
        df_hits = pd.read_csv('ga_hits.csv')
        df_sessions = pd.read_csv('ga_sessions.csv')
    </code>
  </pre>
  <p>Размеры датасетов:</p>
    <pre class="bg-dark text-white p-3 rounded">
        <code class="language-python">
            # Размеры датасетов
           print(df_hits.shape)
           print(df_sessions.shape)
        </code>

  <div class="case-image">
    <img src="{{ site.baseurl }}/assets/images/sports_binary.png" alt="кейс по python" class="img-fluid w-50">
  </div>

  <section>
    <h2>Основная информация по датасетам</h2>
    <p>Для начала посмотрим на общую информацию по двум датасетам: <code>df_hits</code> и <code>df_sessions</code>.</p>
    
    <div class="img-container">
      <img src="path_to_image_1.png" alt="df_hits Info">
      <p>Информация о <code>df_hits</code></p>
    </div>
  
    <div class="img-container">
      <img src="path_to_image_2.png" alt="df_sessions Info">
      <p>Информация о <code>df_sessions</code></p>
    </div>
  
    <h3>Процент пропущенных значений по датасетам</h3>
    <p>Для того, чтобы оценить процент пропущенных значений, используется функция <code>missing_values_check</code>:</p>
    
    <pre><code class="python">
  # Процент пропущенных значений по датасетам
  def missing_values_check(data):    
      missing_values = data.isnull().sum()
      total_cells = np.product(data.shape)
      total_missing = missing_values.sum()
      missing_percentage = (total_missing / total_cells) * 100
      print(f"Процент пропущенных данных: {missing_percentage:.2f}%")
    
  print(missing_values_check(df_hits))
  print(missing_values_check(df_sessions))
    </code></pre>
    
    <p>Результаты выполнения:</p>
    <pre><code>
  Процент пропущенных данных: 20.19%
  None
  Процент пропущенных данных: 14.69%
  None
    </code></pre>
  
    <h3>Подсчет уникальных значений в столбцах</h3>
    <p>Теперь подсчитаем уникальные значения в столбцах каждого датасета с помощью функции <code>nunique_for_columns</code>:</p>
  
    <pre><code class="python">
  # Подсчет уникальных значений в столбцах
  def nunique_for_columns(data):    
      for column_name in data.columns:
          unique_values = data[column_name].nunique()
          print(f"Уникальных значений {column_name}: {unique_values}")
  
  nunique_for_columns(df_sessions)
    </code></pre>
    
    <p>Результат выполнения для <code>df_sessions</code>:</p>
    <pre><code>
  Уникальных значений session_id: 1860042
  Уникальных значений client_id: 1391717
  Уникальных значений visit_date: 226
  Уникальных значений visit_time: 85318
  Уникальных значений visit_number: 537
  Уникальных значений utm_source: 293
  Уникальных значений utm_medium: 56
  Уникальных значений utm_campaign: 412
  Уникальных значений utm_adcontent: 286
  Уникальных значений utm_keyword: 1219
  Уникальных значений device_category: 3
  Уникальных значений device_os: 13
  Уникальных значений device_brand: 205
  Уникальных значений device_model: 104
  Уникальных значений device_screen_resolution: 5039
  Уникальных значений device_browser: 57
  Уникальных значений geo_country: 166
  Уникальных значений geo_city: 2548
    </code></pre>
  
    <p>Результат выполнения для <code>df_hits</code>:</p>
    <pre><code>
  Уникальных значений session_id: 1734610
  Уникальных значений hit_date: 226
  Уникальных значений hit_time: 925887
  Уникальных значений hit_number: 500
  Уникальных значений hit_type: 1
  Уникальных значений hit_referer: 37873
  Уникальных значений hit_page_path: 342715
  Уникальных значений event_category: 52
  Уникальных значений event_action: 230
  Уникальных значений event_label: 39825
  Уникальных значений event_value: 0
    </code></pre>
  </section>
  
  <section>
    <h2>Очистка данных</h2>
  
    <h3>Проверка на дубликаты строк</h3>
    <p>Для начала проверим, есть ли дубликаты в строках обоих датасетов:</p>
    
    <pre><code class="python">
  # Проверка на дубликаты строк
  df_sessions.duplicated().unique()
  # Результат:
  # array([False])
  
  df_hits.duplicated().unique()
  # Результат:
  # array([False])
    </code></pre>
  
    <h3>Проверка и заполнение пропусков</h3>
    <p>Теперь рассмотрим процент пропущенных значений по признакам в каждом из датасетов:</p>
  
    <pre><code class="python">
  # Процент пропущенных значений по признакам
  missing_percentage = df_hits.isnull().mean() * 100
  print(missing_percentage)
    </code></pre>
  
    <p>Результаты для <code>df_hits</code>:</p>
    <pre><code>
  session_id          0.000000
  hit_date            0.000000
  hit_time           58.247795
  hit_number          0.000000
  hit_type            0.000000
  hit_referer        39.899634
  hit_page_path       0.000000
  event_category      0.000000
  event_action        0.000000
  event_label        23.909905
  event_value       100.000000
  dtype: float64
    </code></pre>
  
    <pre><code class="python">
  missing_percentage = df_sessions.isnull().mean() * 100
  print(missing_percentage)
    </code></pre>
  
    <p>Результаты для <code>df_sessions</code>:</p>
    <pre><code>
  session_id                   0.000000
  client_id                    0.000000
  visit_date                   0.000000
  visit_time                   0.000000
  visit_number                 0.000000
  utm_source                   0.005215
  utm_medium                   0.000000
  utm_campaign                11.806346
  utm_adcontent               18.043410
  utm_keyword                 58.174009
  device_category              0.000000
  device_os                   57.533002
  device_brand                19.740307
  device_model                99.121633
  device_screen_resolution     0.000000
  device_browser               0.000000
  geo_country                  0.000000
  geo_city                     0.000000
  dtype: float64
    </code></pre>
  
    <h3>Заполнение пропусков в <code>df_hits</code></h3>
    <p>Для заполнения пропусков используем медиану для числовых признаков и моду для категориальных.</p>
  
    <pre><code class="python">
  # Пропуски время хита меняем на медиану. Медиана нечувствительна к выбросам.
  df_hits['hit_time'].fillna(df_hits['hit_time'].median(), inplace=True)
  
  # Функция заполнения пропусков модой в категориальных признаках:
  def fill_missing_with_mode(df, columns):
      for column in columns:
          df[column].fillna(df[column].mode()[0], inplace=True)
      return df
  
  # Список категориальных признаков для заполнения модой
  columns_to_fill = ['hit_referer', 'event_label']
  df_hits = fill_missing_with_mode(df_hits, columns_to_fill)
  
  # Удаляем пустую колонку event_value
  df_hits.drop(columns='event_value', inplace=True)
    </code></pre>
  
    <p>Проверяем процент пропущенных значений после заполнения:</p>
    <pre><code>
  missing_percentage = df_hits.isnull().mean() * 100
  print(missing_percentage)
    </code></pre>
  
    <p>Результат:</p> 
    <pre><code>
  session_id        0.0
  hit_date          0.0
  hit_time          0.0
  hit_number        0.0
  hit_type          0.0
  hit_referer       0.0
  hit_page_path     0.0
  event_category    0.0
  event_action      0.0
  event_label       0.0
  dtype: float64
    </code></pre>
  
    <h3>Заполнение пропусков в <code>df_sessions</code></h3>
    <p>Теперь заполним пропуски в <code>df_sessions</code> с использованием моды для категориальных признаков. Также удалим колонку <code>device_model</p>, так как в ней почти все значения пропущены.</p>
  
    <pre><code class="python">
  # Заполнение пропусков модой для категориальных признаков
  columns_to_fill = ['utm_campaign', 'utm_source', 'utm_adcontent', 'utm_keyword', 'device_os', 'device_brand']
  df_sessions = fill_missing_with_mode(df_sessions, columns_to_fill)
  
  # Удаляем колонку device_model
  df_sessions.drop(columns='device_model', inplace=True)
    </code></pre>
  
    <p>Проверяем процент пропущенных значений после заполнения:</p>
    <pre><code>
  missing_percentage = df_sessions.isnull().mean() * 100
  print(missing_percentage)
    </code></pre>
  
    <p>Результат:</p>
    <pre><code>
  session_id                  0.0
  client_id                   0.0
  visit_date                  0.0
  visit_time                  0.0
  visit_number                0.0
  utm_source                  0.0
  utm_medium                  0.0
  utm_campaign                0.0
  utm_adcontent               0.0
  utm_keyword                 0.0
  device_category             0.0
  device_os                   0.0
  device_brand                0.0
  device_screen_resolution    0.0
  device_browser              0.0
  geo_country                 0.0
  geo_city                    0.0
  dtype: float64
    </code></pre>
  
    <h3>Преобразование колонки <code>hit_page_path</code></h3>
    <p>Теперь преобразуем значения в колонке <code>hit_page_path</code>. Мы будем извлекать информацию о модели автомобиля, если она присутствует в пути, и очищать ненужные параметры URL.</p>
  
    <pre><code class="python">
  # Функция преобразования признака с информацией об автомобилях
  def extract_car_info(page_path):
      if 'cars' not in page_path:
          page_path = 'other page'
      else:
          page_path = page_path.split('cars')[-1]
      if 'all' in page_path:
          page_path = page_path.split('all')[-1]
      if '?' in page_path:
          page_path = page_path.split('?')[0]
      page_path = page_path.replace('/', ' ')
      if '&' in page_path:
          page_path = 'other page'
      return page_path
  
  # Применяем функцию к признаку 'hit_page_path'
  df_hits['car_info'] = df_hits['hit_page_path'].apply(extract_car_info)
  
  df_hits.head(20)
    </code></pre>
  </section>
  




