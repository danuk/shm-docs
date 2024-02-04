---
title: "Функции"
weight: 10
toc_hide: true
hide_summary: true
---

## `toJson()`

Преобразует объект в JSON.

Данную ф-ию удобно использовать для отладки запросов, когда не очевидно, что вернет тот или иной метод, для просмотра полей и т.п.

#### Пример 1:

Смотрим, что вернет метод `user.list_for_api`:

```go
{{ toJson( user.list_for_api ) }}
```

#### Результат:
```go
{
  "balance": 123.45,
  "block": 0,
  "bonus": 0,
  "can_overdraft": 0,
  "created": "2024-01-08 15:18:16",
  "credit": 0,
  "discount": 0,
  "full_name": "Admin",
  "last_login": "2024-01-22 20:52:53",
  "login": "admin",
  "user_id": 1
}
```

#### Пример 2:

Строим JSON объект. Удобно для использования в Telegram bot-е, в HTTP запросах и т.п.:

```go
{{
  toJson(
    a = user.id
    b = 2
    c = [3,4,5]
  )
}}
```

#### Результат:
```go
{"a":1,"b":2,"c":[3,4,5]}
```

## `toQueryString()`

Преобразование объекта в Query String:

#### Пример:

```go
{{
  toQueryString(
    a = user.id
    b = 2
    c = "hello world"
  )
}}
```

#### Результат:
```
a=1&b=2&c=hello%20world
```


## `list_for_api()`

Метод для получения списка данных объекта.

Без аргументов выдаст первые 25 строк данных.

#### Аргументы:
| Параметр | Описание |
|:---------|----------|
| admin    | Установка этого параметра в 1 позволяет получить данные всех клиентов
| limit    | Кол-во отдаваемых данных. По-умолчанию: 25. (0 - без лимитов)
| offset   | Индекс начала смещения списка. По-умолчанию: 0
| filter   | Используется для поиска данных по определенным полям

#### Пример 1:

Выведем всех пользователей:
```go
{{ arr = ref(user.list_for_api('admin', 1)) }}
{{ FOR item IN arr }}
User id: {{ item.user_id }}, Login: {{ item.login }}, Balance: {{ item.balance }}
{{ END }}
```

#### Результат:
```
User id: 1, Login: admin, Balance: 0
User id: 22, Login: danuk, Balance: 224.44
User id: 34, Login: Dima, Balance: 0
User id: 117, Login: xims, Balance: 200
```


#### Пример 2:

Выведем список всех услуг с категорией начинающиеся на `web`, и период которых равен 1 месяцу:
```go
{{ arr = ref(service.list_for_api('filter', { 'category' => 'web%', period => 1 } )) }}
{{ FOR item IN arr }}
Service id: {{ item.service_id }}, Name: {{ item.name }}, Cost: {{ item.cost }}
{{ END }}
```

#### Результат:
```
Service id: 111, Name: Web хостинг, Cost: 0
Service id: 110, Name: Тариф X-MAX, Cost: 300
Service id: 5, Name: Web хостинг LITE, Cost: 0
```

### Сортировка результатов

Для сортировки используете функции: `sort` (для алфавитной сортировки), `nsort` (для числовой сортировки) и `reverse` (обратный порядок данных).

#### Пример сортировки по полю `category`:
```go
{{ arr = ref(service.list_for_api().sort('cateogry')) }}
```

#### Пример сортировки по полю `cost`, в убывающем порядке:
```go
{{ arr = ref(service.list_for_api().nsort('cost').reverse) }}
```

