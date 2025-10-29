
---
title: "Подсчет доходов за месяц"
weight: 10
toc_hide: false
hide_summary: false
---

Ниже приведен пример шаблона для подсчета поступления средств за Январь 2024 года:

```go
{{ sum = user.pays.filter( date = '2024-01-%' ).sum( all_users = 1 ).money }}

Итого за Январь: {{ sum }} руб.
```

