
---
title: "Telegram bot"
weight: 200
---

## Введение

Этот шаблон используется для Telegram Bot.

Это двух-уровневый шаблон. Сначала используются теги `<% ... %>` для нахождения
нужной секции шаблона, соответствующей команды. После нахождения нужной секции
шаблонизатор будет использовать теги вида: `{{ ... }}`.

Для сопоставления команды пользователя/бота используется внутренняя переменная `cmd`.
Так, при наборе команды `/balance` будет найдена секция: ```<% CASE '/balance' %>```.

> Команда `USER_NOT_FOUND` является встроенной в SHM, и вызывается автоматичеки, когда SHM не может найти у себя этого пользователя.

В каждой секции мы можем писать реальные команды Telegram. Например, команда `sendMessage` отправляет сообщение в Telegram.
Вы можете использовать эту команду в соответсвии с [документацией](https://core.telegram.org/bots/api#sendmessage) Telegram.

В каждой секции можно писать множество команд, через запятую (см. пример в секции `/balance`).

Для того, чтобы лучше понять, как строятся всевозможные кнопочки, читайте документацию Telegram. В этом шаблоне всего-лишь описаны вызовы этих методов.

Пример шаблона:

```go
<% SWITCH cmd %>
<% CASE 'USER_NOT_FOUND' %>
{
    "sendMessage": {
        "text": "Для работы с Telegram ботом укажите Telegram логин в профиле личного кабинета"
    }
}
<% CASE ['/start', '/menu'] %>
{
    "sendMessage": {
        "text": "Я Ваш тестовый Telegram Bot",
        "reply_markup": {
            "inline_keyboard": [
                [
                    {
                        "text": "Баланс",
                        "callback_data": "/balance"
                    }
                ]
            ]
        }
    }
}
<% CASE '/balance' %>
{
    "deleteMessage": { "message_id": {{ message.message_id }} }
},
{
    "sendMessage": {
        "text": "Баланс: {{ user.balance }}",
        "reply_markup": {
            "inline_keyboard": [
                [
                    {
                        "text": "Назад",
                        "callback_data": "/menu"
                    }
                ]
            ]
        }
    }
}
```

## Стандартные методы

- sendMessage - https://core.telegram.org/bots/api#sendmessage
- sendPhoto - https://core.telegram.org/bots/api#sendphoto
- sendAudio - https://core.telegram.org/bots/api#sendaudio
- sendDocument - https://core.telegram.org/bots/api#senddocument
- sendVideo - https://core.telegram.org/bots/api#sendvideo
- sendAnimation - https://core.telegram.org/bots/api#sendanimation
- sendVoice - https://core.telegram.org/bots/api#sendvoice
- sendVideoNote - https://core.telegram.org/bots/api#sendvideonote
- sendMediaGroup - https://core.telegram.org/bots/api#sendmediagroup
- sendLocation - https://core.telegram.org/bots/api#sendlocation
- sendVenue - https://core.telegram.org/bots/api#sendvenue
- sendContact - https://core.telegram.org/bots/api#sendcontact
- sendPoll - https://core.telegram.org/bots/api#sendpoll
- sendDice - https://core.telegram.org/bots/api#senddice
- sendChatAction - https://core.telegram.org/bots/api#sendchataction
- deleteMessage - https://core.telegram.org/bots/api#deletemessage

`chat_id` - заполняется автоматически

## Встроенные переменные SHM
- `cmd` - Команда от бота или пользователя. В обычном случае это первое слово до пробела. Если команда начинается со знака `|`, то команда будет сохранена целиком.
- `message` - сообщение от Telegram
- `args` - массив аргументов, переданный в `callback_data`. Разделитель - пробел.

## Встроенные методы SHM

- `shmRegister` - метод позволяет зарегистрировать нового клиента
  ```go
  "shmRegister": {
      "callback_data": "/menu",
      "error": "ОШИБКА: Логин {{ message.chat.username }} или chat_id {{ message.chat.id }} уже существует"
  }
  ```

- `shmServiceOrder` - метод для регистрации новых услуг. Пример использования:
  ```go
  "shmServiceOrder": {
      "service_id": "{{ args.0 }}",
      "callback_data": "/menu",
      "cb_not_enough_money": "/pay",
      "error": "ОШИБКА"
   }
   ```
   где: `service_id` - ID услуги, `callback_data` - команда для случая успешного заказа услуги, `cb_not_enough_money` - команда для случая нехватки средств для активации услуги.

- `shmServiceDelete` - метод для удаления услуг пользователя. Пример использования:
  ```go
  "shmServiceDelete": {
      "usi": "{{ args.0 }}",
      "callback_data": "/menu",
      "error": "ОШИБКА"
   }
   ```
   где: `usi` - ID услуги пользователя

- `uploadDocumentFromStorage` - метод загружает данные из Storage и отправляет их в виде файла
  ```go
  "uploadDocumentFromStorage": {
      "name": "vpn{{ args.0 }}",
      "filename": "vpn{{ args.0 }}.conf"
  }
  ```

- `uploadPhotoFromStorage` - метод загружает данные из Storage и отправляет их в виде картинки (QR code)
  ```go
  "uploadPhotoFromStorage": {
      "name": "vpn{{ args.0 }}",
      "format": "qr_code_png"
  }
  ```

## Отправка системных сообщений

В случае, если Вы хотите отправлять системные сообщения себе в телеграм, то для этого Вы можете создать отдельный
шаблон, и в его settings прописать:
```go
{
    "telegram": {
        "chat_id": ID_вашего_чата
    }
}
```

Пример шаблона:
```go
Событие:  {{ event_name }}

Пользователь: {{ user.login }} ({{ user.id }})

{{ IF us.id }}
Услуга: [{{ us.id }}]  {{ us.name }}
{{ END }}

{{ IF event_name == "PAYMENT" }}
Платеж на сумму: {{ user.pays.last.money }} зачислен для пользователя: {{ user.login }}
{{ END }}
```






