---
title: "Report"
weight: 11
toc_hide: true
hide_summary: true
---

## `report`

Объект `report` позволяет управлять HTTP-ответом из шаблона:
- задавать HTTP-статус;
- добавлять HTTP-заголовки;
- формировать ошибку для JSON-ответа API.

> Используйте `report` в HTTP-контексте (например, в шаблонах API). В задачах/уведомлениях HTTP-заголовки не применяются.
>
> Важно: без вызова `report.add_error(...)` заголовки и статус из `report` не применяются. Вызов `add_error` обязателен (сообщение можно не передавать).

## `report.status(CODE)`

Устанавливает HTTP-статус ответа.

**Синтаксис:**
```go
{{ report.status(403) }}
```

**Пример:**
```go
{{ IF !user.id }}
{{ report.status(401) }}
{{ report.add_error("UNAUTHORIZED") }}
{{ END }}
```

## `report.headers(HASH)`

Устанавливает HTTP-заголовки ответа.

> Заголовки будут отправлены только если в `report` добавлена ошибка через `report.add_error(...)`.

**Синтаксис:**
```go
{{ report.headers( "X-Request-Id" = "abc-123" ) }}
```

**Пример:**
```go
{{
report.headers(
  "Cache-Control" = "no-store"
  "X-User-Id" = user.id
)
}}
```

## `report.add_error([MESSAGE])`

Добавляет сообщение об ошибке в ответ API.

`MESSAGE` не обязателен. Метод можно вызвать без аргументов, чтобы просто перевести ответ в error-режим.

Именно этот вызов активирует ветку error-ответа, где применяются:
- HTTP-статус из `report.status(...)`;
- HTTP-заголовки из `report.headers(...)`.

Если есть ошибки, API возвращает JSON вида:
```json
{
  "status": 400,
  "error": "MESSAGE"
}
```

Если до этого указан `report.status(...)`, будет использован этот код.

## Типовой шаблон ошибки API

```go
{{ IF !user.id }}
{{ report.status(401) }}
{{ report.headers( "WWW-Authenticate" = "Bearer" ) }}
{{ report.add_error("AUTH_REQUIRED") }}
{{ END }}
```

Результат:
- HTTP статус: `401`
- HTTP заголовок: `WWW-Authenticate: Bearer`
- JSON тело: `{ "status": 401, "error": "AUTH_REQUIRED" }`

## Пример без текста ошибки

```go
{{ report.status(429) }}
{{ report.headers( "Retry-After" = "60", status = 429 ) }}
{{ report.add_error() }}
```

Результат:
- HTTP статус: `429`
- HTTP заголовок: `Retry-After: 60`
- JSON тело содержит поле `status` и пустое поле `error`.

## Пример HTTP редиректа

```go
{{ report.headers( status = 302, location = "https://example.com/success" ) }}
{{ report.add_error() }}
```

Результат:
- HTTP статус: `302`
- HTTP заголовок: `Location: https://example.com/success`
- Ответ будет обработан клиентом как редирект (при поддержке редиректов клиентом).
