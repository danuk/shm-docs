---
title: "Фильтры"
weight: 10
toc_hide: true
hide_summary: true
---

## `filter()`

Фильтры используются для точечной выборки данных из БД

Например, мы можем получить список пользователей с положительным балансом так:
```
{{ FOR u IN user.filter( balance = isPositive ).items }}
...
{{ END }}
```

## Синтаксис фильтрации

### Важные правила синтаксиса

**Кавычки для ключей:**
- Кавычки НЕ обязательны для обычных ключей: `status = 1`, `balance = gt(0)`
- Кавычки ОБЯЗАТЕЛЬНЫ только для ключей, содержащие спец. символы: `'-or'`, `'-and'`, `=`, `!=`, `a.b` ...

**Запятые:**
- В однострочном синтаксисе запятые обязательны: `filter( status = 1, balance = gt(0) )`
- В многострочном синтаксисе запятые НЕ нужны при разделении переносом строки

### Основные правила записи

**Простые значения** можно записывать напрямую без использования специальных функций:

```
{{ FOR u IN user.filter( status = 1 ).items }}
  Пользователь {{ u.login }} со статусом 1
{{ END }}
```

Это эквивалентно использованию `eq()`:
```
{{ FOR u IN user.filter( status = eq(1) ).items }}
  То же самое, что и выше
{{ END }}
```

**Строковые значения** также можно записывать напрямую:
```
{{ FOR u IN user.filter( role = "admin" ).items }}
  Администраторы
{{ END }}
```

### Когда использовать функции сравнения

Функции сравнения (`eq()`, `ne()`, `gt()`, etc.) нужны в следующих случаях:

**1. Для явного указания типа сравнения:**
```
{{ FOR u IN user.filter( balance = isPositive ).items }}
  Пользователи с положительнрым балансом
{{ END }}
```

**2. Для операторов отличных от равенства:**
```
{{ FOR u IN user.filter( age = gt(18) ).items }}
  Пользователи старше 18 лет
{{ END }}
```

**3. Для текстовых полей, когда нужно точное совпадение:**
```
{{ FOR u IN user.filter( login = eq("admin") ).items }}
  Точно "admin", а не LIKE 'admin' (LIKE регистронезависимый)
{{ END }}
```

**4. В JSON полях для лучшей читаемости:**
```
{{ FOR u IN user.filter( settings = { status = ne(0) } ).items }}
  Пользователи с активными настройками
{{ END }}
```

### Краткие формы записи

| Полная форма | Краткая форма | Описание |
|-------------|---------------|----------|
| `status = eq(1)` | `status = 1` | Равенство |
| `enabled = eq(true)` | `enabled = true` | Boolean значения |
| `role = eq("admin")` | `role = "admin"` | Строки (для точного сравнения) |
| `name = eq("")` | `name = ""` | Пустая строка |

### Специальные случаи

**Текстовые поля с LIKE:**
```
# Автоматический LIKE поиск
{{ FOR u IN user.filter( name = "%Петр%" ).items }}

# Точное сравнение
{{ FOR u IN user.filter( name = eq("Петр") ).items }}
```

**Boolean значения:**
```
# Все эти записи эквивалентны
{{ FOR u IN user.filter( enabled = true ).items }}
{{ FOR u IN user.filter( enabled = eq(true) ).items }}
{{ FOR u IN user.filter( enabled = 1 ).items }}
{{ FOR u IN user.filter( enabled = eq(1) ).items }}
```

**NULL значения:**
```
# Простая запись
{{ FOR u IN user.filter( manager_id = null ).items }}
{{ FOR u IN user.filter( manager_id = { '!=' = null } ).items }}

# С функцией
{{ FOR u IN user.filter( manager_id = isNull ).items }}
{{ FOR u IN user.filter( manager_id = isNotNull ).items }}
```

### Многострочный синтаксис

В многострочных фильтрах действуют особые правила:

**1. Запятые не требуются** при разделении ключ-значение переносом строки:

```
{{ FOR u IN user.filter(
    balance = gt(0)
    status = ne(0)
    description = isNotEmpty
).items }}
  Фильтр без запятых
{{ END }}
```

**2. Сравнение однострочного и многострочного синтаксиса:**

```
# Однострочный (с запятыми)
{{ FOR u IN user.filter( status = 1, balance = gt(0), role = "admin" ).items }}

# Многострочный (без запятых)
{{ FOR u IN user.filter(
    status = 1
    balance = gt(0)
    role = "admin"
).items }}
```

## Принципы работы фильтрации

### Логический оператор "И" (AND)

По умолчанию все условия, перечисленные через запятую, объединяются логическим оператором "И":

```
{{ FOR u IN user.filter(
    balance = gt(0)
    status = 1
    created = ge('2024-01-01')
).items }}
  Пользователь {{ u.login }} соответствует ВСЕМ условиям одновременно
{{ END }}
```

Это эквивалентно SQL: `WHERE balance > 0 AND status = 1 AND created >= '2024-01-01'`

### Логический оператор "ИЛИ" (OR)

Для реализации логики "ИЛИ" есть несколько способов:

#### Способ 1: Использование специального ключа `-or`

```
{{ FOR u IN user.filter( '-or' = { balance = 0, login = "test" } ).items }}
  Пользователь {{ u.login }} с нулевым балансом ИЛИ логином "test"
{{ END }}
```

Можно комбинировать `-or` с обычными условиями:

```
{{ FOR u IN user.filter(
    status = 1
    '-or' = {
        balance = gt(1000)
        settings = { vip = true }
    }
).items }}
  Активный пользователь с балансом больше 1000 ИЛИ VIP статусом
{{ END }}
```

#### Способ 2: Объединение результатов нескольких фильтров

```
{{ SET positive_users = user.filter( balance = gt(0) ).items }}
{{ SET premium_users = user.filter( status = eq(2) ).items }}

{{ FOR u IN positive_users.merge(premium_users).unique }}
  Пользователь {{ u.login }} имеет положительный баланс ИЛИ премиум статус
{{ END }}
```

#### Способ 3: Массивы значений для одного поля

```
{{ FOR u IN us.filter( status = [STATUS_ACTIVE,STATUS_BLOCK,STATUS_WAIT_FOR_PAY] ).items }}
    Услуги пользователя с указанными статусами
{{ END }}
```

### Автоматическое использование LIKE для текстовых полей

Для текстовых полей в базе данных автоматически применяется оператор `LIKE` при использовании символов подстановки `%`. Это позволяет выполнять гибкий поиск по шаблонам.

#### Принцип работы

Когда вы указываете значение для текстового поля, система автоматически:
1. Определяет тип поля в структуре таблицы
2. Если поле имеет текстовый тип (`text`, `varchar`, etc.) И значение содержит символы `%`, применяет оператор `LIKE`
3. Если символов `%` нет, используется точное сравнение `=`

#### Примеры LIKE поиска

**Поиск по подстроке в имени:**
```
{{ FOR u IN user.filter(full_name = "%Иван%").items }}
  Найден пользователь {{ u.login }} с именем содержащим "Иван"
{{ END }}
```
Генерирует SQL: `WHERE full_name LIKE '%Иван%'`

**Поиск по началу логина:**
```
{{ FOR u IN user.filter(login = "admin%").items }}
  Найден пользователь {{ u.login }} начинающийся с "admin"
{{ END }}
```
Генерирует SQL: `WHERE login LIKE 'admin%'`

**Поиск по окончанию email:**
```
{{ FOR u IN user.filter(email = "%@company.com").items }}
  Пользователи с корпоративным email
{{ END }}
```
Генерирует SQL: `WHERE email LIKE '%@company.com'`

#### Точное совпадение для текстовых полей

Если вам нужно точное совпадение, НЕ используйте символы `%`:

```
{{ FOR u IN user.filter(login = "admin").items }}
  Пользователь с точным логином "admin"
{{ END }}
```
Генерирует SQL: `WHERE login LIKE 'admin'`

Альтернативно, можно использовать оператор `eq()`:

```
{{ FOR u IN user.filter(login = eq("admin")).items }}
  Пользователь с точным логином "admin"
{{ END }}
```
Генерирует SQL: `WHERE login = 'admin'`

#### Кастомные LIKE паттерны

Для более сложных паттернов поиска используйте явное указание LIKE операторов:

```
{{ FOR u IN user.filter(email = { '-like' = "%@gmail.com" }).items }}
  Пользователи с email на Gmail
{{ END }}
```

```
{{ FOR u IN user.filter(login = { '-not_like' = "test%" }).items }}
  Пользователи, логин которых НЕ начинается с "test"
{{ END }}
```

#### Смешанные условия с LIKE

```
{{ FOR u IN user.filter(
    full_name = "%Петр%"
    email = "%@company.com"
    status = eq(1)
).items }}
  Активные пользователи с именем содержащим "Петр" и корпоративным email
{{ END }}
```

В этом примере:
- `full_name LIKE '%Петр%'` (LIKE из-за символов %)
- `email LIKE '%@company.com'` (LIKE из-за символов %)
- `status = 1` (точное сравнение для числового поля)

**Важно**: Символы `%` нужно добавлять самостоятельно для активации LIKE поиска!

### Работа с JSON полями

**Важно**: Для работы с JSON полями используйте формат `json_поле = { 'ключ' = значение }`, а НЕ `'json_поле.ключ' = значение`.

Для фильтрации по полям внутри JSON используйте хеш с ключами JSON полей:

```
{{ FOR u IN user.filter(settings = { notifications = true }).items }}
  Пользователь {{ u.login }} с включенными уведомлениями
{{ END }}
```

Для вложенных JSON структур:

```
{{ FOR u IN user.filter( profile = { 'preferences.theme' = 'dark' }).items }}
  Пользователь {{ u.login }} с темной темой
{{ END }}
```

#### Проверка существования JSON поля

Для проверки существования поля в JSON используйте имя поля в точечной нотации:

```
{{ FOR u IN user.filter(settings = 'notifications').items }}
  Пользователь {{ u.login }} у которого есть поле notifications в settings
{{ END }}
```

Для вложенных полей:

```
{{ FOR u IN user.filter(settings = 'profile.avatar').items }}
  Пользователь {{ u.login }} у которого есть поле profile.avatar в settings
{{ END }}
```

#### Проверка отсутствия JSON поля

Для проверки отсутствия поля в JSON используйте префикс `!`:

```
{{ FOR u IN user.filter(settings = '!notifications').items }}
  Пользователь {{ u.login }} у которого НЕТ поля notifications в settings
{{ END }}
```

Для вложенных полей:

```
{{ FOR u IN user.filter(settings = '!profile.avatar').items }}
  Пользователь {{ u.login }} у которого НЕТ поля profile.avatar в settings
{{ END }}
```

#### Проверка значений в JSON полях

Для проверки конкретных значений используйте хеш с точечной нотацией:

```
{{ FOR u IN user.filter(settings = { notifications = true, theme = 'dark' }).items }}
  Пользователь {{ u.login }} с темной темой и включенными уведомлениями
{{ END }}
```

С операторами сравнения:

```
{{ FOR u IN user.filter(settings = { max_items = gt(10), priority = le(5) }).items }}
  Пользователь {{ u.login }} с max_items > 10 и priority <= 5 в настройках
{{ END }}
```

#### Комбинирование JSON условий

```
{{ FOR u IN user.filter(
    settings = 'api_key'
    settings = { notifications = true, auto_backup = ne(false) }
).items }}
  Пользователь {{ u.login }} с API ключом, уведомлениями и автобэкапом
{{ END }}
```

#### Проверка JSON полей с специальными функциями

```
{{ FOR u IN user.filter(settings = { api_key = isNotNull }).items }}
  Пользователь {{ u.login }} с настроенным API ключом
{{ END }}
```

```
{{ FOR u IN user.filter(profile = { avatar = isEmpty }).items }}
  Пользователь {{ u.login }} без аватара в профиле
{{ END }}
```

## Практические примеры синтаксиса

### Сравнение различных подходов

**Числовые значения:**
```
# Простая запись (рекомендуется для равенства)
{{ FOR u IN user.filter( user_id = 123 ).items }}

# Явная запись (когда нужна ясность)
{{ FOR u IN user.filter( user_id = eq(123) ).items }}

# Операторы сравнения (обязательно через функции)
{{ FOR u IN user.filter( user_id = gt(1000) ).items }}
```

**Строковые значения:**
```
# Точное совпадение
{{ FOR u IN us.filter( status = STATUS_ACTIVE ).items }}

# LIKE поиск (с символами %)
{{ FOR u IN us.filter( status = "%ACTIVE%" ).items }}

# Явное точное совпадение (рекомендуется)
{{ FOR u IN us.filter( status = eq(STATUS_ACTIVE) ).items }}
```

**Boolean значения:**
```
# Простая запись (рекомендуется)
{{ FOR u IN user.filter( enabled = true ).items }}
{{ FOR u IN user.filter( deleted = false ).items }}

# Числовая запись (также работает)
{{ FOR u IN user.filter( enabled = 1 ).items }}
{{ FOR u IN user.filter( deleted = 0 ).items }}
```

**JSON поля:**
```
# Простые значения в JSON
{{ FOR u IN user.filter( settings = { theme = 'dark' } ).items }}

# Функции сравнения в JSON
{{ FOR u IN user.filter( settings = { max_items = gt(10) } ).items }}

# Специальные функции в JSON
{{ FOR u IN user.filter( settings = { api_key = isNotNull } ).items }}
```

### Рекомендации по выбору синтаксиса

**Используйте простую запись когда:**
- Проверяете равенство: `block = 1`
- Работаете с boolean: `enabled = true`
- Нужно совпадение строки без учета регистра: `role = "admin"`
- Нужно совпадение части строки: `role = "adm%"`

**Используйте функции когда:**
- Нужны операторы сравнения: `age = gt(18)`
- Работаете с NULL: `field = isNull`
- Нужны специальные проверки: `name = isEmpty`
- Нужно точное совпадение строки: `role = eq("admin")`


## Специальные функции для фильтрации

### Проверка на NULL и пустые значения

#### `isNull`
Проверяет, что поле равно NULL:
```
{{ FOR u IN user.filter( avatar = isNull ).items }}
  Пользователь {{ u.login }} без аватара
{{ END }}
```

#### `isNotNull`
Проверяет, что поле не равно NULL:
```
{{ FOR u IN user.filter( avatar = isNotNull ).items }}
  Пользователь {{ u.login }} с аватаром
{{ END }}
```

#### `isEmpty`
Проверяет, что поле пустое (NULL или пустая строка):
```
{{ FOR u IN user.filter( description = isEmpty ).items }}
  Пользователь {{ u.login }} без описания
{{ END }}
```

#### `isNotEmpty`
Проверяет, что поле не пустое:
```
{{ FOR u IN user.filter( description = isNotEmpty ).items }}
  Пользователь {{ u.login }} с описанием
{{ END }}
```

### Числовые сравнения

#### `lt(значение)` (<)
Меньше указанного значения:
```
{{ FOR u IN user.filter( balance = lt(100) ).items }}
  Пользователь {{ u.login }} с балансом меньше 100
{{ END }}
```

#### `gt(значение)` (>)
Больше указанного значения:
```
{{ FOR u IN user.filter( balance = gt(1000) ).items }}
  Пользователь {{ u.login }} с балансом больше 1000
{{ END }}
```

#### `le(значение)` (<=)
Меньше или равно указанному значению:
```
{{ FOR u IN user.filter( balance = le(0) ).items }}
  Пользователь {{ u.login }} с нулевым или отрицательным балансом
{{ END }}
```

#### `ge(значение)` (>=)
Больше или равно указанному значению:
```
{{ FOR u IN user.filter( balance = ge(500) ).items }}
  Пользователь {{ u.login }} с балансом от 500
{{ END }}
```

#### `eq(значение)` (==)
Равно указанному значению:
```
{{ FOR u IN user.filter( status = eq(1) ).items }}
  Активный пользователь {{ u.login }}
{{ END }}
```

#### `ne(значение)` (!=)
Не равно указанному значению:
```
{{ FOR u IN user.filter( status = ne(0) ).items }}
  Пользователь {{ u.login }} (не заблокирован)
{{ END }}
```

#### `between(мин, макс)` ([..])
Значение в диапазоне (включительно):
```
{{ FOR u IN user.filter( balance = between(100, 1000) ).items }}
  Пользователь {{ u.login }} с балансом от 100 до 1000
{{ END }}
```

### Проверка знака числа

#### `isPositive` (>0)
Проверяет, что значение больше нуля (положительное):
```
{{ FOR u IN user.filter( balance = isPositive ).items }}
  Пользователь {{ u.login }} с положительным балансом
{{ END }}
```

#### `isNegative` (<0)
Проверяет, что значение меньше нуля (отрицательное):
```
{{ FOR u IN user.filter( balance = isNegative ).items }}
  Пользователь {{ u.login }} с отрицательным балансом
{{ END }}
```

#### `isNonNegative` (>=0)
Проверяет, что значение больше или равно нулю (неотрицательное):
```
{{ FOR u IN user.filter( balance = isNonNegative ).items }}
  Пользователь {{ u.login }} с неотрицательным балансом
{{ END }}
```

#### `isNonPositive` (<=0)
Проверяет, что значение меньше или равно нулю (неположительное):
```
{{ FOR u IN user.filter( balance = isNonPositive ).items }}
  Пользователь {{ u.login }} с неположительным балансом
{{ END }}
```

## Сложные примеры фильтрации

### Комбинирование разных типов условий

```
{{ FOR u IN user.filter(
    balance = gt(0)
    status = ne(0)
    description = isNotEmpty
    settings = { notifications = true }
    created = between('2024-01-01', '2024-12-31')
).items }}
  Активный пользователь {{ u.login }} с положительным балансом, описанием,
  включенными уведомлениями, созданный в 2024 году
{{ END }}
```

### Фильтрация с использованием JSON и специальных функций

```
{{ FOR s IN service.filter(
    enabled = true
    price = ge(100)
    config = { auto_renewal = false }
    description = isNotEmpty
).items }}
  Активная услуга {{ s.name }} стоимостью от 100 без автопродления
{{ END }}
```

### Комбинирование AND и OR условий

```
{{ FOR u IN user.filter(
    status = eq(1)
    balance = ge(0)
    '-or' = {
        profile = { type = 'premium' }
        created = ge('2024-01-01')
        settings = { notifications = true }
    }
).items }}
  Активный пользователь с неотрицательным балансом И (премиум профиль ИЛИ создан в 2024 ИЛИ включены уведомления)
{{ END }}
```

### Сложная JSON фильтрация

```
{{ FOR u IN user.filter(
    settings = 'api_key'
    settings = '!deprecated_features'
    settings = {
        max_sessions = gt(1)
        auto_logout = true
        'security.two_factor' = ne(false)
    }
    profile = { 'preferences.language' = ['ru', 'en'] }
).items }}
  Пользователь {{ u.login }} с API ключом, без устаревших функций,
  с настройками безопасности и поддержкой русского или английского языка
{{ END }}
```

## Специальные значения

Также доступны специальные константы:

- `null` - значение NULL
- `true` - логическое истина (1)
- `false` - логическое ложь (0)

```
{{ FOR s IN service.filter( enabled = true ).items }}
  Активная услуга {{ s.name }}
{{ END }}
```


