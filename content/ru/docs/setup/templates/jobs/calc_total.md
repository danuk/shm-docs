
---
title: "Подсчет доходов за месяц"
weight: 10
toc_hide: false
hide_summary: false
---

Ниже приведен пример шаблона для подсчета поступления средств за Январь 2024 года:

```go
{{ sum = 0 }}
{{ arr = user.pays.filter( date = '2024-01-%' ).items }}
{{ FOR item IN arr }}
{{ sum = sum + item.money }}
{{ END }}

Итого за Январь: {{ sum }} руб.
```

