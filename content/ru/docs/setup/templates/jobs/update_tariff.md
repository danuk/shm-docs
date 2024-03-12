
---
title: "Массовое обновление тарифов"
weight: 40
toc_hide: false
hide_summary: false
---

Бывают случаи, когда нужно обновить тарифы всем клиентам. Здесь приведены примеры, как это сделать.


## Обновление стоимости текущей услуги (тарифа)

Просто изменить стоимость услуги в Каталоге не достаточно.
Необходимо для таких услуг пользователя установить "следующую" услугу в "текущую":

```go
{{ arr = ref(user.services.list_for_api( 'admin',1, 'limit',0, 'filter',{} )) }}
{{ FOR item IN arr }}
{{ us.id( item.user_service_id ).set('next', item.service_id) }}
{{ END }}
```

## Массовая смена услуг (тарифов)

Например, мы хотим сменить (со следующего учетного периода) услугу 5 на 6, делается это так:

```go
{{ arr = ref(user.services.list_for_api( 'admin',1, 'limit',0, 'filter',{ 'service_id' => 5 } )) }}
{{ FOR item IN arr }}
{{ us.id( item.user_service_id ).set('next', 6) }}
{{ END }}
```

