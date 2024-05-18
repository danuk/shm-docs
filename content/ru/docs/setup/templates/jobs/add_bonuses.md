
---
title: "Массовое начисление бонусов"
weight: 40
toc_hide: false
hide_summary: false
---

Бывают случаи, когда необходимо начислить бонусы всем клиентам. Здесь приведены примеры, как это сделать.

## Массовое начисление бонусов клиентам с указанной активной услугой:

Следующий код начислит всем клиентам с активной услугой 6 по 100 бонусов:

```go
{{ arr = ref(user.services.list_for_api( 'admin',1, 'limit',0, 'filter',{ 'service_id' => 5, 'status' => 'ACTIVE' } )) }}
{{ FOR item IN arr }}
{{ user = user.switch( item.user_id ) }}
{{ user.add_bonus( 100, 'Акция' ) }}
{{ END }}
```

