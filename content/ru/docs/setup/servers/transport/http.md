
---
title: "HTTP"
weight: 10
---

HTTP (HTTPS) - самый распространенный протокол в сети Интернет.

В этом разделе описывается способ настройки транспорта HTTP.

## Настройки

Хост (host)
: адрес сервера, например: `https://domain.com`

> Можно использовать шаблоны, например: `https://api.telegram.org/bot{{ config.telegram.token }}/sendMessage`

Метод (method)
: Метод HTTP: GET, POST, PUT, DELETE. По-умолчанию используется POST.

Шаблон (template_id)
: Шаблон для формирования PAYLOAD DATA (context).
Используется для POST и PUT методов. Для остальных можно указать любой.

content_type
: По-умолчанию установлен в значение: `application/json; charset=utf-8`

headers
: Заголовки, например: `{"Authorization":"Basic YWRtaW46YWRtaW4","Cache-Control":"no-cache"}`

timeout
: Таймаут HTTP сервера в секундах. По-умолчанию 10 сек

verify_hostname
: Установите этот параметр в значение 0, если не требуется проверка SSL сертификата на валидность (самоподписный сертификат)

## Примеры

### Отправка сообщения в Telegram (sendMessage)

https://core.telegram.org/bots/api#sendmessage

1. Настройте Telegram уведомления по [инструкции]({{<ref "docs/setup/servers/transport/telegram" >}})
2. Создайте шаблон для формирования уведомления в Telegram через HTTP:
```
{{
  toJson(
    chat_id = user.settings.telegram.chat_id
    text = "test message"
  )
}}
```
3. Создайте группу серверов с транспортом HTTP
4. Создайте сервер в этой группе, укажите Хост: `https://api.telegram.org/bot{{ config.telegram.token }}/sendMessage` и укажите созданый шаблон на 2 шаге.
5. Привяжите необходимое событие к этой группе серверов (созданную на шаге 3)

Теперь, когда событие наступит, SHM выполнит вот такую команду:
```
curl https://api.telegram.org/bot2836119681:AAEyvDasDFC-Y98xmYOhLni8p2bhshjkhio/sendMessage \
    -X POST \
    -H 'Content-Type: application/json; charset=utf-8' \
    -d '{"chat_id":1234567890,"text":"test message"}'
```

### Формирование QueryString для GET запросов

Используйте ф-ию `toQueryString()` для формирования аргументов для GET запросов.

Пример:
```
{{
  toQueryString(
    A = 1
    text = "test message"
    qaz = "Привет Мир "
  )
}}
```

Вернет строку вида: `text=test%20message&A=1&qaz=%D0%9F%D1%80%D0%B8%D0%B2%D0%B5%D1%82%20%D0%9C%D0%B8%D1%80%20`, которая
будет автоматически добавлена к URI.

