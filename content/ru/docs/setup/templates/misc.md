---
title: "Вспомогательные функции и методы"
weight: 10
toc_hide: true
hide_summary: true
---

## `misc`

Объект `misc` предоставляет доступ к вспомогательным функциям из модуля `Core::Utils`. Эти функции помогают работать с датами, строками, JSON, файлами и другими полезными операциями прямо в шаблонах.

## Работа с датами и временем

### `now()`
Возвращает текущую дату и время в формате "YYYY-MM-DD HH:MM:SS".

**Синтаксис:**
```
{{ misc.now }}
```

**Пример:**
```
Текущее время: {{ misc.now }}
```
**Результат:** `Текущее время: 2025-10-20 15:30:45`

### `utime_to_string(timestamp, format)`
Преобразует Unix timestamp в строку с датой.

**Параметры:**
- `timestamp` - Unix timestamp (необязательный, по умолчанию текущее время)
- `format` - формат даты (необязательный, по умолчанию "%Y-%m-%d %H:%M:%S")

**Синтаксис:**
```
{{ misc.utime_to_string(1698756000) }}
{{ misc.utime_to_string(1698756000, "%d.%m.%Y") }}
```

### `string_to_utime(date_string)`
Преобразует строку с датой в Unix timestamp.

**Параметры:**
- `date_string` - строка с датой в формате "YYYY-MM-DD HH:MM:SS"

**Синтаксис:**
```
{{ misc.string_to_utime("2025-01-01 12:00:00") }}
```

### `parse_date(date_string)`
Разбирает строку с датой и возвращает хеш с компонентами.

**Параметры:**
- `date_string` - строка с датой (необязательный, по умолчанию текущая дата)

**Синтаксис:**
```
{{ SET date_parts = misc.parse_date("2025-12-31 23:59:59") }}
Год: {{ date_parts.year }}, Месяц: {{ date_parts.month }}
```

### `add_date_time(date, options)`
Добавляет указанное количество времени к дате.

**Параметры:**
- `date` - исходная дата (необязательный, по умолчанию текущая дата)
- `options` - хеш с параметрами: year, month, day, hour, min, sec

**Синтаксис:**
```
{{ misc.add_date_time(misc.now, { day = 7 }) }}
{{ misc.add_date_time("2025-01-01 00:00:00", { month = 1, day = 15 }) }}
```

### `start_of_month(date)`
Возвращает начало месяца для указанной даты.

**Синтаксис:**
```
{{ misc.start_of_month("2025-03-15 14:30:00") }}
```
**Результат:** `2025-03-01 00:00:00`

### `start_of_day(date)`
Возвращает начало дня для указанной даты.

**Синтаксис:**
```
{{ misc.start_of_day("2025-03-15 14:30:00") }}
```
**Результат:** `2025-03-15 00:00:00`

### `end_of_month(date)`
Возвращает конец месяца для указанной даты.

**Синтаксис:**
```
{{ misc.end_of_month("2025-02-15 14:30:00") }}
```
**Результат:** `2025-02-28 23:59:59`

### `days_in_months(date)`
Возвращает количество дней в месяце для указанной даты.

**Синтаксис:**
```
{{ misc.days_in_months("2025-02-15") }}
```
**Результат:** `28`

### `format_time_diff(target_date)`
Форматирует разность между указанной датой и текущим временем в читаемом виде.

**Параметры:**
- `target_date` - целевая дата в формате "YYYY-MM-DD HH:MM:SS"

**Синтаксис:**
```
{{ misc.format_time_diff("2025-12-31 23:59:59") }}
```
**Результат:** `2 месяца, 11 дней, 8 часов`

## Работа с JSON

### `encode_json(data, options)`
Преобразует данные в JSON строку с UTF-8 кодировкой.

**Параметры:**
- `data` - данные для преобразования
- `options` - хеш с параметрами (pretty - для красивого форматирования)

**Синтаксис:**
```
{{ misc.encode_json({ name = "Test", value = 123 }) }}
{{ misc.encode_json(data, { pretty = 1 }) }}
```

### `encode_json_perl(data, options)`
Преобразует данные в JSON строку с внутренней кодировкой Perl (для совместимости с кириллицей).

**Синтаксис:**
```
{{ misc.encode_json_perl({ name = "Тест", value = 123 }) }}
```

### `decode_json(json_string)`
Преобразует JSON строку в структуру данных.

**Синтаксис:**
```
{{ SET data = misc.decode_json('{"name":"Test","value":123}') }}
{{ data.name }}
```

## Работа со строками и кодированием

### `html_escape(string)`
Экранирует HTML символы в строке.

**Синтаксис:**
```
{{ misc.html_escape('<script>alert("XSS")</script>') }}
```
**Результат:** `&lt;script&gt;alert(&quot;XSS&quot;)&lt;/script&gt;`

### `encode_base64(string)`
Кодирует строку в Base64.

**Синтаксис:**
```
{{ misc.encode_base64("Hello World") }}
```

### `decode_base64(encoded_string)`
Декодирует строку из Base64.

**Синтаксис:**
```
{{ misc.decode_base64("SGVsbG8gV29ybGQ=") }}
```

### `encode_base64url(string)`
Кодирует строку в Base64 URL-safe формат.

**Синтаксис:**
```
{{ misc.encode_base64url("Hello World!") }}
```

### `decode_base64url(encoded_string)`
Декодирует строку из Base64 URL-safe формата.

**Синтаксис:**
```
{{ misc.decode_base64url("SGVsbG8gV29ybGQh") }}
```

### `to_query_string(hash)`
Преобразует хеш в query string для URL.

**Синтаксис:**
```
{{ misc.to_query_string({ name = "test", value = "123" }) }}
```
**Результат:** `name=test&value=123`

## Генерация данных

### `passgen(length)`
Генерирует случайный пароль.

**Параметры:**
- `length` - длина пароля (необязательный, по умолчанию 10)

**Синтаксис:**
```
{{ misc.passgen(12) }}
{{ misc.passgen() }}
```

### `uuid_gen()`
Генерирует UUID.

**Синтаксис:**
```
{{ misc.uuid_gen() }}
```
**Результат:** `123e4567-e89b-12d3-a456-426614174000`

### `get_random_value(value)`
Возвращает случайное значение из массива или само значение, если это не массив.

**Синтаксис:**
```
{{ misc.get_random_value(["red", "green", "blue"]) }}
{{ misc.get_random_value("single_value") }}
```

## Работа с массивами и хешами

### `hash_merge(hash1, hash2, ...)`
Объединяет несколько хешей в один.

**Синтаксис:**
```
{{ SET merged = misc.hash_merge(hash1, hash2, hash3) }}
```

### `uniq_by_key(array, key)`
Возвращает уникальные элементы массива по указанному ключу.

**Синтаксис:**
```
{{ SET unique_items = misc.uniq_by_key(items, "id") }}
```

## Валидация

### `is_email(email)`
Проверяет, является ли строка корректным email адресом.

**Синтаксис:**
```
{{ IF misc.is_email("test@example.com") }}
  Корректный email
{{ END }}
```

### `is_host(hostname)`
Проверяет, является ли строка корректным hostname или IP адресом.

**Синтаксис:**
```
{{ IF misc.is_host("example.com") }}
  Корректный хост
{{ END }}
```

### `is_ip_allowed(ip, networks)`
Проверяет, входит ли IP адрес в список разрешенных сетей.

**Синтаксис:**
```
{{ IF misc.is_ip_allowed("192.168.1.1", ["192.168.1.0/24", "10.0.0.0/8"]) }}
  IP разрешен
{{ END }}
```

## Работа с периодами

### `parse_period(period)`
Разбирает строку периода в формате "месяцы.дни.часы".

**Параметры:**
- `period` - строка в формате "M.DD.HH"

**Синтаксис:**
```
{{ SET period = misc.parse_period("3.15.12") }}
Месяцы: {{ period.months }}, Дни: {{ period.days }}, Часы: {{ period.hours }}
```

## Полезные функции

### `dots_str_to_sql(string)`
Преобразует строку с точками в SQL запрос для JSON полей.

**Синтаксис:**
```
{{ SET sql_info = misc.dots_str_to_sql("settings.api.key") }}
Поле: {{ sql_info.field }}, Запрос: {{ sql_info.query }}
```

### `get_user_ip()`
Возвращает IP адрес пользователя.

**Синтаксис:**
```
{{ misc.get_user_ip() }}
```

## Примеры использования

### Работа с датами
```
Сегодня: {{ misc.now }}
Завтра: {{ misc.add_date_time(misc.now, { day = 1 }) }}
Начало месяца: {{ misc.start_of_month(misc.now) }}
```

### Генерация паролей и UUID
```
Новый пароль: {{ misc.passgen(16) }}
UUID для записи: {{ misc.uuid_gen() }}
```

### Работа с JSON
```
{{ SET user_data = { name = "Иван", age = 30 } }}
JSON: {{ misc.encode_json_perl(user_data, { pretty = 1 }) }}
```

### Валидация данных
```
{{ IF misc.is_email(user.email) }}
  Email корректен: {{ user.email }}
{{ ELSE }}
  Некорректный email
{{ END }}
```

## Важные замечания

### Кодировка и локализация
- Используйте `encode_json_perl()` для данных с кириллицей в шаблонах
- `encode_json()` подходит для API с UTF-8 кодировкой
- Функция `format_time_diff()` возвращает текст на русском языке

### Безопасность
- Всегда используйте `html_escape()` для пользовательских данных перед выводом в HTML
- Функции валидации (`is_email`, `is_host`) обеспечивают базовую проверку формата

### Производительность
- UUID генерация читает системный файл `/proc/sys/kernel/random/uuid`

### Совместимость
- Все функции даты работают с форматом "YYYY-MM-DD HH:MM:SS"
- Base64 функции совместимы со стандартом RFC 4648
- JSON функции поддерживают вложенные структуры данных


