
---
title: "Вывод данных в формате JSON"
weight: 20
toc_hide: false
hide_summary: false
---

Для формирования JSON объекта удобно использовать следующий синтаксис:

#### Пример шаблона (`my_template`) для формирования данных в JSON:
```go
{{ u = user.id( request.params.uid ) }}
{{
  toJson(
    user_id = u.id
    login = u.login
    balance = u.balance
    last_payment = u.pays.last
    forecast = u.pays.forecast.total
  )
}}
```

#### Пример вызова шаблона для `curl`:

`curl -s http://127.0.0.1:8081/shm/v1/public/my_template?format=json&uid=123`

> Необходимо прописать в `settings` шаблона параметр: `allow_public: true`



#### Пример результата:

```go
{
    "user_id": "123",
    "login": "danuk",
    "balance": -21.56,
    "last_payment": {
        "comment": null,
        "date": "2016-01-04 20:33:35",
        "id": 2,
        "money": 455,
        "pay_system_id": "manual",
        "user_id": 123
    },
    "forecast": 1013.45
}
```
