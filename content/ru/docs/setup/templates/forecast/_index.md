
---
title: "Прогноз оплаты"
weight: 20
---

Метод `user.pays.forecast` возвращает JSON вида:

```go
{
  "items": [
    {
      "name": "Тариф хостинга",
      "expired": "2017-01-31 23:59:50",
      "total": 123.45,
    },
    {
      "name": "Регистрация домена в зоне .RU: domain.ru",
      "expired": "2017-07-29 12:39:46",
      "total": 590,
    }
  ],
  "dept": 21.56,
  "total": 713.45
}
```

Мы можем построить шаблон письма о прогнозе опаты услуг следующим образом:
```go
Уважаемый {{ user.full_name }}

Уведомляем Вас о сроках действия услуг:

{{ FOR item IN user.pays.forecast.items }}
- Услуга: {{ item.name }}
  Стоимость: {{ item.total }}
  Истекает: {{ item.expired }}
{{ END }}

{{ IF user.pays.forecast.dept }}
Погашение задолженности: {{ user.pays.forecast.dept }}
{{ END }}

Итого к оплате: {{ user.pays.forecast.total }} руб.
```

Подробнее о создании и настройке шаблонов можно прочитать [здесь]({{< ref "/docs/api/templates" >}})

