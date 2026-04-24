
---
title: "Telegram"
weight: 40
---

Telegram - популярный мессенджер (https://telegram.org/)

В этом разделе описывается настройка транспорта Telegram и способы его использования.

Транспорт Telegram позволяет:
- Отправлять уведомления/сообщения клиентам с помощью Telegram
- Работать в качестве полноценного Бота (оказывать услуги, принимать платежи и т.п.)

Для написания ботов используются API методы Telegram. Ознакомится с полным списком методов вы можете в [официальной документации
Telegram](https://core.telegram.org/bots/api). Ниже приведены примеры для метода [`sendMessage`](https://core.telegram.org/bots/api#sendmessage).

## Профили Telegram в SHM

Для работы SHM с Telegram ему необходимо знать `token` бота. SHM поддерживает работу сразу с несколькими ботами.
Для удобного управлениями конфигурациями ботов введено понятие "Профиль". Обычно, профиль бота совпадает с именем шаблона.

Для шаблона бота с названием `telegram_bot` создайте одноименный профиль (`telegram_bot`), и пропишите в этот профиль `token` бота и `secret`
который будет работать с этим шаблоном. Если у вас есть и другие боты, настройте их по аналогии.

Если профиль используется для отправки Telegram уведомлений в конкретный чат, то дополнительно укажите в профиле и `chat_id`.

Профили ботов настраиваются в Админке SHM. В разделе "Конфигурация" выберите пункт `telegram` и кликните по нему дважды.
В открывшемся окне кликните на "шестеренку" для открытия редактора JSON.

Создайте/настройте конфигурацию ваших ботов, пример:
![QR-code](/telegram_profiles_2.jpg)



## Telegram уведомления

```mermaid
flowchart LR
    A([SHM]) --> B(Событие) -->С(Шаблон) --> D(Telegram API) --> E(Telegram client)
```

### Настройка
Для того, чтобы SHM мог отправлять сообщения Вашим пользователям, необходимо:
1. Создать Telegram Bot-а, с помощью бота [@BotFather](https://t.me/BotFather) (https://handbook.tmat.me/ru/dev/botfather)
2. Создайте "Профиль" в SHM и укажите в нем `token` созданного бота
3. Создайте шаблон сообщения в админке, которое вы хотите отправлять своим пользователям ("Настройки" -> "Шаблоны")
4. Создайте нужное событие. Привяжите Ваш шаблон к нужному событию. В качестве группы серверов необходимо указать "Telegram уведомления", или любую другую группу, транспорт которой "telegram"
5. Дайте Вашему пользователю ссылку на вашего бота, чтобы он мог его себе добавить. После добавления бота Ваш клиент сможет получать от него уведомления

> В случаях, когда Ваш клиент регистрировался в SHM НЕ через Telegram bot-а, необходимо указать его логин telegram в его профиле (кабинете)

### Отправка уведомлений

Отправить сообщения в Telegram своим клиентам можно следующими способами:
1. С помощью транспорта Telegram
2. С помощью специального метода Шаблонизатора (`telegram.send()`)
3. С помощью специального метода Шаблонизатора (`telegram.bot()`)

#### `Отправка текста с помощью транспорта Telegram`
- Создайте шаблон с нужным текстом для отправки клиентам
- Привяжите шаблон к нужному событию, указав при этом транспорт Telegram

Пример шаблона отправки текста в Telegram:
```go
Тестовое сообщение для пользователя: {{ user.full_name }}
```

#### `Использование API Telegram с помощью транспорта Telegram`
Если вы хотите отправить не просто текст:
- Создайте шаблон с `JSON` данными для отправки в API Telegram
- Привяжите шаблон к нужному событию, указав при этом транспорт Telegram
- Пропишите в `settings` шаблона: `{"telegram":{"raw":true}}`

Пример использования [`sendMessage`](https://core.telegram.org/bots/api#sendmessage) в API Telegram:
```go
{{ toJson( sendMessage = {
  text = "Тестовое сообщение для пользователя: " _ user.full_name
  reply_markup = {
    inline_keyboard = [[{
      text = "Посетите наш сайт"
      url = "https://domain.com"
    }]]
  }
  })
}}
```

Пример шаблона отправки нескольких сообщений в API Telegram:
```go
{{ data = [] }}
{{ data.push( sendMessage = { text = 'Сообщение 1' } ) }}
{{ data.push( sendMessage = { text = 'Сообщение 2' } ) }}
{{ toJson( data ) }}
```

>> Если клиент использует сразу несколько ботов, то получит сообщение в каждый из них. Если нужно указать конкретный Профиль, то это можно сделать путем указания его в `settings` шаблона: `{"telegram":{"profile":ИМЯ_ПРОФИЛЯ}}`

### Отправка уведомлений себе

В случае, если Вы хотите отправлять системные сообщения себе в Telegram, то для этого:
- Создайте шаблон с нужным содержимым
- Создайте отдельный Профиль Telegram, укажите в нём `token` бота и `chat_id`, куда отправлять сообщения
- В `settings` шаблона укажите Профиль: `{"telegram":{"profile":"ИМЯ_ПРОФИЛЯ"}}`
- Привяжите ваш шаблон к нужным событиям


## Telegram bot

- Telegram bot реализован с помощью шаблона SHM (`telegram_bot` по-умолчанию)
- Telegram client отправляет [x-telegram-bot-api-secret-token](https://core.telegram.org/bots/api#setwebhook) в заголовке запроса. SHM сравнивает его с вашим `secret` который прописан рядом с `token`

```mermaid
flowchart LR
    classDef green fill:green,color:#fff
    classDef red fill:red,color:#fff
    classDef gray fill:gray,color:#fff

    A(Telegram client) <--> B(Telegram API) <--> C(SHM\nВерификация)
    C <-- Успех --> CS(telegram_bot):::green
    C -- Ошибка --> CE(Ошибка):::red -- Webhook verification failed --> B
```
Для работы полноценного бота нужно:
- Выполнить шаги 1 и 2 из раздела: "Telegram уведомления" (если еще не выполнены).
- Настроить Telegram API, сообщить ему адрес, куда отправлять запросы от клиента (от бота). Для этого скачайте bash скрипт [setWebhook.sh](https://raw.githubusercontent.com/danuk/shm/master/scripts/telegram/setWebhook.sh). Перед запуском скрипта необходимо его отредактировать, укажите:
  - `token` бота
  - `secret` 1–256 символов. Разрешены только символы A–Z, a–z, 0–9, _ и -. Вы можете сгенерировать `secret` с помощью следующей команды: `openssl rand -hex 32`
  - адрес SHM (например: domain.com)
  - имя шаблона (`telegram_bot` по-умолчанию)
- Выполните скрипт `setWebhook.sh` на любом Linux/Unix устройстве, так же подойдет и MacOS.
- Проверьте наличие шаблона для бота (`telegram_bot` по-умолчанию). Внесите в него изменения по своему усмотрению.
- Пропишите `secret` в [конфигурацию](https://docs.myshm.ru/docs/setup/servers/transport/telegram/#%D0%BF%D1%80%D0%BE%D1%84%D0%B8%D0%BB%D0%B8-telegram-%D0%B2-shm) SHM, обратите внимание на название шаблона, по умолчанию `telegram_bot`
- Зайдите в своего бота в клиенте Telegram и выполните команду: `/start`. Если всё настроено верно, вы увидите приветствие.

>> По-умолчанию SHM определяет профиль для бота по имени шаблона. Если нужно использовать другой профиль, то это можно указать в скрипте `setWebhook.sh`, добавив после имени шаблона параметр: `tg_profile`, например: `telegram_bot?tg_profile=profile1`

Более подробно о шаблонах Telegram читайте здесь: [Шаблон Telegram bot]({{<ref "docs/setup/templates/telegram_bot" >}})

### Аутентификация

При обращении к боту SHM сопоставляет входящий запрос с пользователем системы по полям `login` и `login2`.

**Поле `login`**

Основное поле. Должно быть заполнено в формате `@ID`, где `ID` — числовой идентификатор пользователя в Telegram (например: `@298002190`).
Заполняется автоматически при первом входе через бота.

>> `@` - префикс по-умолчанию. Его можно переопределить с помощью переменной `login_prefix` в Профиле телеграм бота.

**Поле `login2`**

Дополнительное поле для ручного указания Telegram-идентификатора. Используется, когда пользователь зарегистрировался в SHM не через бота.

Допустимые форматы:
- `@123456789` — Telegram ID (число с `@` в начале).
- `username` — Telegram username

Заполняется вручную администратором в профиле пользователя.

## Telegram Login для Web (OIDC)

SHM поддерживает авторизацию через Telegram Login API в методе:

- `POST /shm/v1/telegram/web/auth`

Метод поддерживает 3 режима:

1. OIDC Code Flow (`code`, `redirect_uri`, `code_verifier`)
2. OIDC ID Token (`id_token`)
3. Legacy Telegram Widget (`id`, `auth_date`, `hash`)

Для OIDC рекомендуется начинать с инициализации:

- `GET /shm/v1/telegram/web/auth/init`

Этот метод генерирует `state`, `nonce`, PKCE (`code_challenge`) и возвращает готовый `auth_url`.

### Настройка профиля

В конфигурации `telegram` для нужного профиля (обычно `telegram_bot`) укажите:

- `token` бота (как и раньше)
- `client_id` (из BotFather → Web Login)
- `client_secret` (из BotFather → Web Login)

Пример:

```json
{
  "telegram_bot": {
    "token": "123456789:AA...",
    "secret": "webhook_secret",
    "client_id": "123456789",
    "client_secret": "telegram_oidc_secret"
  }
}
```

### Что проверяет SHM

Для OIDC (`id_token`):

- подпись JWT по JWKS: `https://oauth.telegram.org/.well-known/jwks.json`
- `iss == https://oauth.telegram.org`
- `aud == client_id` профиля
- срок жизни токена (`exp`)
- `nonce` (если вы его передали)

Для Code Flow:

- обмен `code` на `id_token` на `https://oauth.telegram.org/token`
- затем выполняются все проверки `id_token` выше
- если переданы `state` и `expected_state`, SHM проверяет их совпадение

Для legacy widget:

- проверка `hash` по `token` бота
- проверка свежести `auth_date`

### Пример 1. OIDC Code Flow (рекомендуется)

Шаг 1. Получите URL авторизации у SHM:

```bash
curl 'https://example.com/shm/v1/telegram/web/auth/init?profile=telegram_bot&register_if_not_exists=1'
```

Ответ:

```json
{
  "auth_url": "https://oauth.telegram.org/auth?...",
  "state": "...",
  "nonce": "...",
  "code_challenge": "...",
  "code_challenge_method": "S256",
  "redirect_uri": "https://example.com/shm/v1/telegram/web/callback",
  "expires_in": 600
}
```

Шаг 2. Откройте `auth_url` из ответа (редиректните пользователя).

Сначала отправьте пользователя на Telegram authorize endpoint:

```text
https://oauth.telegram.org/auth?client_id=123456789&redirect_uri=https%3A%2F%2Fexample.com%2Fshm%2Fv1%2Ftelegram%2Fweb%2Fcallback&response_type=code&scope=openid%20profile&state=RANDOM_STATE&code_challenge=PKCE_CHALLENGE&code_challenge_method=S256
```

Вы можете указывать `redirect_uri` сразу на метод SHM:

- `GET /shm/v1/telegram/web/callback`

Этот метод в `v1` принимает `code` от Telegram и выполняет обмен на `id_token` внутри SHM.

Шаг 3. После редиректа Telegram на callback SHM автоматически использует сохраненный `code_verifier` по `state`.

Дополнительный backend-вызов не обязателен, но остается доступным:

```bash
curl -X POST 'https://example.com/shm/v1/telegram/web/auth' \
  -H 'Content-Type: application/json' \
  -d '{
    "profile": "telegram_bot",
    "code": "AUTH_CODE",
    "redirect_uri": "https://example.com/auth/telegram/callback",
    "code_verifier": "PKCE_CODE_VERIFIER",
    "state": "RANDOM_STATE",
    "expected_state": "RANDOM_STATE",
    "register_if_not_exists": 1
  }'
```

Либо можно не делать дополнительный backend-вызов, если используете callback endpoint напрямую:

`https://example.com/shm/v1/telegram/web/callback?profile=telegram_bot&register_if_not_exists=1&expected_state=RANDOM_STATE&code_verifier=PKCE_CODE_VERIFIER`

Если вы используете `auth/init`, параметры `expected_state` и `code_verifier` можно не передавать: SHM возьмет их из кэша по `state`.

Ответ при успехе:

```json
{
  "session_id": "..."
}
```

### Пример 2. OIDC через id_token

Если ваш frontend уже получил `id_token`, передайте его напрямую:

```bash
curl -X POST 'https://example.com/shm/v1/telegram/web/auth' \
  -H 'Content-Type: application/json' \
  -d '{
    "profile": "telegram_bot",
    "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6I...",
    "nonce": "SERVER_NONCE",
    "register_if_not_exists": 1
  }'
```

### Пример 3. Legacy Telegram Widget

Старый формат также поддерживается:

```bash
curl -X POST 'https://example.com/shm/v1/telegram/web/auth' \
  -H 'Content-Type: application/json' \
  -d '{
    "profile": "telegram_bot",
    "id": "298002190",
    "first_name": "John",
    "last_name": "Smith",
    "username": "johnsmith",
    "photo_url": "https://t.me/i/userpic/...jpg",
    "auth_date": "1713780000",
    "hash": "<telegram_hash>",
    "register_if_not_exists": 1
  }'
```

### Привязка Telegram к уже существующему пользователю

Чтобы привязать Telegram к текущему пользователю SHM, передайте:

- `uid` — user_id в SHM
- `bind_to_profile` = `1`

Пример:

```bash
curl -X POST 'https://example.com/shm/v1/telegram/web/auth' \
  -H 'Content-Type: application/json' \
  -d '{
    "profile": "telegram_bot",
    "uid": 123,
    "bind_to_profile": 1,
    "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6I..."
  }'
```

### Рекомендации по безопасности

1. Для OIDC Code Flow обязательно используйте PKCE (`code_challenge_method=S256`).
2. Всегда проверяйте `state` на backend (`state` == `expected_state`).
3. Используйте `nonce` и передавайте его в `/telegram/web/auth` при работе с `id_token`.
4. Храните `client_secret` только на backend.
5. Убедитесь, что ваш `redirect_uri` добавлен в Allowed URLs у BotFather.

