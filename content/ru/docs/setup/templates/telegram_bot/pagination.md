
---
title: "Пагинация"
weight: 200
toc_hide: true
hide_summary: false
---

## Пример шаблона для постраничного вывода списка услуг


```go
{{
  limit = 5
  data = []
}}
<% SWITCH cmd %>
<% CASE '/list' %>
{{
  offset = args.0 || 0
  items = ref( user.services.list_for_api('limit',limit, 'offset',offset) )
}}
{{ FOR item IN items }}
{{ data.push(
    [{
      "text" = 'Услуга: ' _ item.name _ ' ID: ' _  item.user_service_id
      "callback_data" = '/service ' _ item.user_service_id
    }]
  )
}}
{{ END }}

{{ data.push(
    [{
      "text" = 'Еще...'
      "callback_data" = '/list ' _ (limit + offset)
    }]
  ) IF items.size == limit
}}

{
  "sendMessage": {
    "text": "Список услуг",
    "reply_markup" : {
      "inline_keyboard": {{ toJson( data ) }}
    }
  }
}
<% END %>
```
