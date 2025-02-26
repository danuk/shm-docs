
---
title: "Прогноз оплаты"
weight: 20
---

## Описание

SHM имеет встроенный модуль `forecast`, позволяющий получать информацию о прогнозе оплаты услуг.

`forecast` находит все услуги (кроме уже заблокированных), дата истечения которых менее чем 3 дня (`days`), и ещё неоплаченные услуги, и вычисляет стоимость их продления.


#### Получить прогноз оплаты можно следующими способами:
- Через Web для текущего авторизованного пользователя: `/shm/v1/user/pay/forecast`
- Через Web для пользователя с ID 123: `/shm/v1/user/pay/forecast?user_id=123` (нужно быть авторизованным под администратором)
- Из шаблонов: `user.pays.forecast`

#### Настройки

| Параметр | Описание |
|:---------|----------|
| days     | Кол-во дней для прогноза (`3` дня по-умолчанию)
| blocked  | Учитывать заблокированные услуги (`0` - по-умолчанию НЕТ)

#### Пример команды в шаблонах:

`{{ forecast = user.pays.forecast('days', 10, 'blocked', 1) }}`


## Пример шаблона
Метод `user.pays.forecast` возвращает JSON вида:

```go
{
  "items": [
        {
            "name": "Регистрация домена в зоне .RU",
            "service_id": 11,
            "user_service_id": 2949,
            "usi": 2949,
            "cost": 590,
            "discount": 0,
            "months": 12,
            "qnt": 1,
            "total": 590,
            "expire": "2017-07-29 12:39:46",
            "status": "ACTIVE",
            "next": {
                "name": "Продление домена в зоне .RU",
                "service_id": 12,
                "cost": 890,
                "discount": 0,
                "months": 12,
                "qnt": 1,
                "total": 890
            }
        }
    ],
    "balance": -21.56,
    "bonuses": 100,
    "dept": 21.56,
    "total": 768.44
}
```

Мы можем построить шаблон письма о прогнозе опаты услуг следующим образом:
```go
Уважаемый {{ user.full_name }}
{{ forecast = user.pays.forecast }}
{{ IF user.make_autopayment( forecast.total ) }}

Выполнен автоплатеж с вашей карты в размере: {{ forecast.total }}.
Ваши услуги будут продлены автоматически.
{{ ELSE }}

Уведомляем Вас о сроках действия услуг:

{{ FOR item IN ref(forecast.items) }}
- Услуга: {{ item.name }}
  {{ IF item.expire }}
  Истекает: {{ item.expire }}
  {{ IF item.service_id != item.next.service_id }}
  Следующая услуга: {{ item.next.name }}
  Стоимость: {{ item.next.total }}
  {{ ELSE }}
  Стоимость продления: {{ item.next.total }}
  {{ END }}
  {{ ELSE }}
  Стоимость: {{ item.total }}
  {{ END }}

{{ END }}

{{ IF forecast.dept }}
Погашение задолженности: {{ forecast.dept }}
{{ END }}

Итого к оплате: {{ forecast.total }} руб.
{{ END }}
```

Подробнее о создании и настройке шаблонов можно прочитать [здесь]({{< ref "/docs/setup/templates" >}})

