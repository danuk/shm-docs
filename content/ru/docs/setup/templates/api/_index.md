
---
title: "HTTP (API)"
linkTitle: "Через HTTP"
weight: 20
toc_hide: true
hide_summary: false
---

<style>
r { color: Red }
g { color: Green }
</style>

SHM позволяет использовать шаблоны для внешнего использования

Поддерживаемые методы:
- GET
- POST

## HTTP адрес

Доступ к шаблону с именем <g>`my_template`</g> можно получить следующим способами:

### Для пользователей

Доступ с авторизацией:

/shm/v1/**<r>template</r>/<g>my_template</g>**?format=html&foo=1&bar=hello

#### Пример для `curl`
```go
curl -s -u 'admin:admin' http://127.0.0.1:8081/shm/v1/template/my_template
```

> Более детально о вариантах аутентификации можно почитать [здесь]({{< ref "docs/api#аутентификация" >}})


### Для публичного использования

/shm/v1/**<r>public</r>/<g>my_template</g>**?format=html&foo=1&bar=hello

Для того, чтобы шаблон работал без авторизации необходимо прописать в его `settings` параметр:

`allow_public`: <g>`true`</g>

В публичных шаблонах переменная `user` не устанавливается автоматически, поэтому
если есть необходимость обратиться к нужному пользователю, то это можно сделать так:
- Указать идентификатор вручную:

  в шаблоне: `user.id( 123 )....`

- Указать идентификатор через HTTP запрос:

  `/shm/v1/public/my_template?uid=123`

  в шаблоне: `user.id( request.params.uid )....`

#### Пример для `curl`
```go
curl -s http://127.0.0.1:8081/shm/v1/public/my_template?uid=123
```

#### Пример шаблона для проверки существования пользователя:

```go
{{ IF user.id( request.params.uid ).id }}
...
{{ END }}
```

### Аргументы

В строку запроса можно добавить любые аргументы. Внутри самого шаблона к ним можно обратиться через переменную `request.params`

```
{{ foo = request.params.foo }}
```

Ниже приведен список специальных аргументов:

#### `format` - формат отдаваемого контента (MIME types)
- `plain` (text/plain)
- `html` (text/html)
- `json` (application/json)
- `other` (application/octet-stream)
- `qrcode`

#### `user_id` - зарезервирован

В случае работы с шаблоном из под прав администратора можно явно указать идентификатор пользователя

## HTTP заголовки

В шаблонах можно читать заголовки. Их удобно использовать для различных проверок

```
{{ headers = request.headers }}
```

#### Пример шаблона для проверки заголовка `x-webhook-secret`:

```go
{{ IF request.headers.x-webhook-secret == 'something-very-very-secret' }}
...
{{ END }}
```

## Примеры

