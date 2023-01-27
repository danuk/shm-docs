
---
title: "API"
weight: 50
---

Все настройки SHM и всё управление системой осуществляется с помощью вызовов API.

Web интерфейс администратора и кабинет клиента работают на основе API.

## HTTP Методы
SHM API использует стандартные методы протокола HTTP:
| Метод  | Назначение        |
|:------:|:------------------|
| GET    | Чтение данных     |
| POST   | Изменение данных  |
| PUT    | Добавление данных |
| DELETE | Удаление данных   |

SHM API v1 отвечает в следующем формате:
```go
{
    data: [
        {
            item: 1
        },
        {
            item: 2
        }
    ],
    items: 2
}
```

## HTTP коды ответов
При работе с SHM API рекомендуется проверять коды ответов HTTP.

`200` - УСПЕХ для GET, POST, PUT запросов

`201` - УСПЕХ для DELETE запросов (эти запросы не возвращают ответа в теле)

`400` - ошибка запроса, неверные или недостающие аргументы

`403` - не авторизован, требуется авторизация

`404` - объект не найден

`5xx` - ошибки сервера

## Аутентификация
### Аутентификация через Cookies (`session_id`)

Получаем `session_id`:
```go
curl -H "Content-Type: application/json" \
     -X POST http://$SERVER/shm/user/auth.cgi \
     -d '{"login": "admin", "password": "admin"}'
```
пример ответа:
```go
{ "session_id": "3f928c835ff78f836c4066c112b292c5" }
```

Запрос с использованием cookies (`session_id`):
```go
curl --cookie "session_id=3f928c835ff78f836c4066c112b292c5" http://$SERVER/shm/v1/user
```


### Basic-аутентификация
```go
curl -u "admin:admin" http://$SERVER/shm/v1/user
```

### Аутентификация через header-ы
```go
curl -H "session-id: 3f928c835ff78f836c4066c112b292c5" http://$SERVER/shm/v1/user
```

```go
curl -H "login: admin" -H "password: admin" http://$SERVER/shm/v1/user
```

### Аутентификация через Query string
```go
curl http://$SERVER/shm/v1/user?session_id=3f928c835ff78f836c4066c112b292c5
```


## Список ресурсов
| Ресурс                           | URL                             |
|:---------------------------------|:--------------------------------|
| Услуги                           | /v1/admin/service               |
| Услуги дочерние                  | /v1/admin/service/children      |
| События услуг                    | /v1/admin/service/event         |
| Пользователи                     | /v1/admin/user                  |
| Смена пароля пользователя        | /v1/admin/user/passwd           |
| Платежи пользователя             | /v1/admin/user/pay              |
| Зачисление платежей              | /v1/admin/user/payment          |
| Профиль пользователя             | /v1/admin/user/profile          |
| Услуги пользователя              | /v1/admin/user/service          |
| Услуги пользователя - списания   | /v1/admin/user/service/withdraw |
| Блокировка услуги пользователя   | /v1/admin/user/service/stop     |
| Список текущих задач для услуги  | /v1/admin/user/service/spool    |




