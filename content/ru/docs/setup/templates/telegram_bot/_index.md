
---
title: "Telegram bot"
weight: 200
toc_hide: true
hide_summary: false
---

## Шаблон для Telegram Bot-а

Это двух-уровневый шаблон. Сначала используются теги `<% ... %>` для нахождения
нужной секции шаблона, соответствующей команды. После нахождения нужной секции
шаблонизатор будет использовать теги вида: `{{ ... }}`.

Для сопоставления команды пользователя/бота используется внутренняя переменная `cmd`.
Так, при наборе команды `/balance` будет найдена секция: ```<% CASE '/balance' %>```.

> Команда `USER_NOT_FOUND` является встроенной в SHM, и вызывается автоматичеки, когда SHM не может найти у себя этого пользователя.

В каждой секции мы можем писать реальные команды Telegram. Например, команда `sendMessage` отправляет сообщение в Telegram.
Вы можете использовать эту команду в соответсвии с [документацией](https://core.telegram.org/bots/api#sendmessage) Telegram.

Для отправки команд в API Telegram используйте метод `tg_api`.

Для того, чтобы лучше понять, как строятся всевозможные [кнопочки](https://handbook.tmat.me/ru/messages/buttons), читайте документацию Telegram. В этом шаблоне всего-лишь описаны вызовы этих методов.

Пример шаблона:

```go
<% SWITCH cmd %>
<% CASE 'USER_NOT_FOUND' %>
{{ tg_api( shmRegister = { callback_data = "/menu" } ) }}
<% CASE ['/start', '/menu'] %>
{{ tg_api( sendMessage = {
        text = "Я Ваш тестовый Telegram Bot"
        reply_markup = {
            inline_keyboard = [
                [
                    {
                        text = "Баланс"
                        callback_data = "/balance"
                    }
                ]
            ]
        }
    }
)
}}
<% CASE '/balance' %>
{{ tg_api( deleteMessage = { message_id = message.message_id } ) }}
{{ tg_api( sendMessage = {
        text = "Баланс: " _ user.balance
        reply_markup = {
            inline_keyboard = [
                [
                    {
                        text = "Назад"
                        callback_data = "/menu"
                    }
                ]
            ]
        }
    }
)
}}
<% CASE %>
{{ tg_api( sendMessage = { text = 'Я не знаю команды: ' _  cmd  } ) }}
<% END %>
```

### Стандартные методы Telegram

В SHM можно использовать любые методы Telegram.

Полный перечень доступных методов и синтаксис их использования
можно посмотреть на официальном сайте документации Telegram https://core.telegram.org/bots/api#available-methods

> `chat_id` - заполняется автоматически, но при необходимости его можно указать

### Встроенные переменные SHM

- `cmd` - специальная переменная SHM в которую записывается значение, полученное из Telegram, в зависимости от типа:
`message.text` для команд введеных пользователем, и `callback_query.data` для `callback_data`. Если команда пользователя начинается
с символа `/`, то такая команда будет усечена до первого слова.
- `args` - массив аргументов команды, разделитель - пробел.
- `start_args` - именованный массив аргументов для команды `start`
- `message` - полное сообщение от Telegram (json объект)

#### Примеры парсинга `cmd` и `args`:
| Источник      | Команда      | cmd        | args    |
|:--------------|--------------|------------|---------|
| Пользователь  | /test 1 2 3  | /test      | [1,2,3] |
| Пользователь  | test 1 2 3   | test 1 2 3 | [1,2,3] |
| callback_data | /test 1 2 3  | /test      | [1,2,3] |
| callback_data | test 1 2 3   | test       | [1,2,3] |

> `args` - это массив. Для получения значений из него используйте синтаксис: `args.N`, где `N` - индекс элемента.
Например, получить первый элемент массива можно так: `args.0`

### Передача дополнительных параметров при старте бота

При старте бота может понадобится передача каких-либо данных, например для отслеживания рекламных акций (`utm` метрики),
или для создания партнерской ссылки.

> ВНИМАНИЕ: суммарная длина параметров не может превышать 64 символа

#### Генерация партнерской ссылки
Для генерации партнерской ссылки можно использовать следующий шаблон:
```go
https://t.me/myshm_bot?start={{ toBase64Url(toQueryString( pid = user.id )) }}
```

где `myshm_bot` - имя бота (замените на свой)

#### Генерация ссылки с произвольными параметрами
```go
https://t.me/myshm_bot?start={{ toBase64Url(toQueryString(
    utm_source = 'google'
    utm_medium = 'telegram'
    foo = 'bar'
))
}}
```
где `myshm_bot` - имя бота (замените на свой)

> Переменные `utm_` автоматически сохраняются в `settings` пользователя

#### Чтение переданных параметров
Вы можете прочитать переданный при старте бота параметр с помощью специальной переменной: `start_args`, пример:

```go
Источник: {{ start_args.utm_source }}
```



### Встроенные методы SHM
#### `shmRegister`
Метод позволяет зарегистрировать нового клиента
  ```go
  "shmRegister": {
      "callback_data": "/menu",
      "error": "ОШИБКА"
  }
  ```

#### `shmServiceOrder`
Метод для регистрации новых услуг. Пример использования:
  ```go
  "shmServiceOrder": {
      "service_id": "{{ args.0 }}",
      "expire": "2014-09-25 10:11:12",
      "parent": 123,
      "check_exists": 1,
      "check_exists_unpaid": 1,
      "check_category": "test-%",
      "callback_data": "/menu",
      "cb_not_enough_money": "/pay",
      "error": "ОШИБКА"
   }
   ```
   где: `service_id` - ID услуги,
    `expire` - установка произвольной даты истечения услуги (опционально),
    `parent` - ID родительской услуги (опционально),
    `check_exists` - проверка существования услуги (опционально),
    `check_exists_unpaid` - проверка существования неоплаченной услуги (опционально),
    `check_category` - проверка существования услуги по категории (опционально),
    `callback_data` - команда для случая успешного заказа услуги,
    `cb_not_enough_money` - команда для случая нехватки средств для активации услуги.

>   Если используется один из флагов: `check_exists`, `check_exists_unpaid`, `check_category` и услуга найдена,
    то регистрация новой услуги не осуществляется, а метод вернет первую найденную услугу (`callback_data` или `cb_not_enough_money`).

#### `shmServiceDelete`
Метод для удаления услуг пользователя. Пример использования:
  ```go
  "shmServiceDelete": {
      "usi": "{{ args.0 }}",
      "callback_data": "/menu",
      "error": "ОШИБКА"
   }
   ```
   где: `usi` - ID услуги пользователя

#### `uploadDocumentFromStorage`
Метод загружает данные из Storage и отправляет их в виде файла
  ```go
  "uploadDocumentFromStorage": {
      "name": "{{ args.0 }}",
      "filename": "{{ args.0 }}.conf"
  }
  ```

#### `uploadPhotoFromStorage`
Метод загружает данные из Storage и отправляет их в виде картинки (QR code)
  ```go
  "uploadPhotoFromStorage": {
      "name": "{{ args.0 }}",
      "format": "qr_code_png"
  }
  ```

#### `shmRedirectCallback`
Метод вызывает указанную команду (`cmd`)
  ```go
  "shmRedirectCallback": {
      "callback_data": "/help"
  }
  ```

## Приём платежей

Для приёма платежей в Telegram удобно использовать дополнительный шаблон ([web_app](https://core.telegram.org/bots/webapps)).

Шаблон используется для возможности ввода произвольной суммы и выбора настроенных платежных систем SHM.

1. [Настройте]({{< ref "/docs/setup/payments" >}}) одну или несколько платежных систем
2. [Скачайте шаблон](https://raw.githubusercontent.com/danuk/shm-templates/main/telegram_bot/tg_payments_webapp.tmpl) и сохраните в SHM под названием `tg_payments_webapp`
3. В Шаблоне своего бота используйте конструкцию вида:
```go
<% CASE '/payment' %>
{{ tg_payment_webapp="{{config.api.url}}/shm/v1/public/tg_payment_webapp?format=html&user_id={{user.id}}&profile={{tpl.id}}" }}
{{ tg_api(
  sendMessage = {
    text = "Оплата покупки",
    reply_markup = {
      inline_keyboard = [
        [{
          text = "Оплатить..."
          web_app = { url = tg_payment_webapp }
        }]
      ]
    }
  }
)
}}
```





