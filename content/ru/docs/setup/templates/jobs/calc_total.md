
---
title: "Подсчет доходов за месяц"
weight: 10
toc_hide: false
hide_summary: false
---

Ниже приведен пример шаблона для подсчета поступления средств за Январь 2024 года:

```go
{{ sum = 0 }}
{{ arr = ref(user.pays.list_for_api( 'admin', 1, 'limit', 0,  'filter', { 'date' => '2024-01-%'} )) }}
{{ FOR item IN arr }}
{{ sum = sum + item.money }}
{{ END }}

Итого за Январь: {{ sum }} руб.
```

