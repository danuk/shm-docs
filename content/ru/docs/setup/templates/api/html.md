
---
title: "Вывод данных в формате HTML"
weight: 20
toc_hide: false
hide_summary: false
---

#### Пример шаблона (`my_template`) для формирования данных в HTML:
```go
<table border=1>
{{ FOR u IN ref( user.list_for_api('admin',1,'limit',0)) }}
<tr>
    <td>{{ u.user_id }}</td>
    <td>{{ u.login }}</td>
    <td>{{ u.balance }}</td>
</tr>
{{ END }}
</table>
```

#### Пример вызова шаблона для `curl`:

`curl -s http://127.0.0.1:8081/shm/v1/public/my_template?format=html`

> Необходимо прописать в `settings` шаблона параметр: `allow_public: true`

#### Результат работы HTML шаблона лучше всего смотреть в браузере:

`http://127.0.0.1:8081/shm/v1/public/my_template?format=html`


