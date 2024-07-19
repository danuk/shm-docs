---
title: "Хранилище"
weight: 80
---

Хранилище в SHM предназначено для хранения произвольных данных в БД SHM.

Данные в хранилище сохраняются под произвольными именами. Имена должны быть уникальными для каждого пользователя.

Типы хранимой информации:
- JSON
- Binary

Максимальный объем записи для одного ключа: 16 Mb.

К данным, сохраненным в формате JSON можно обращаться по ключам (см. примеры ниже).

Работать с данными можно как через HTTP, так и с помощью [Шаблонов]({{< ref "docs/setup/templates/#хранилище" >}}).

## Работа через HTTP

Для работы с хранилищем используйте следующие данные:

### URL
`/shm/v1/storage/manage/NAME`, где `NAME` - имя записи

### Методы
- GET - чтение записи
- PUT - создание новой записи
- POST - редактирование существующией записи
- DELETE - удаление записи

### Заголовок `Content-Type`
- `application/json` для сохранения данных JSON
- `text/html` - для хранения текстовых данных
- `application/octet-stream` - для хранения двоичных данных

## Примеры
### Сохранение данных в хранилище

Сохраним содержимое файла: `my_file.txt` в SHM с ключем `my_file` для пользователя: `user`:
```bash
curl -s -u "user:pass" \
      -X "PUT" \
      -H "Content-Type: text/html; charset=utf-8" \
      https://admin.DOMAIN/shm/v1/storage/manage/my_file \
      --data-binary @my_file.txt
```

Сохраним JSON данные в SHM с ключем `data` для пользователя: `user`:
```bash
curl -s -u "user:pass" \
      -X "PUT" \
      -H "Content-Type: application/json; charset=utf-8" \
      https://admin.DOMAIN/shm/v1/storage/manage/data \
      -d '{"user_key":123}'
```

### Чтение данных

Чтение файла `my_file` из SHM и сохранение его в `my_file.txt`:
```bash
curl -s -u 'user:pass' https://admin.DOMAIN/shm/v1/storage/manage/my_file > my_file.txt
```

Чтение JSON данных, получение значения ключа `user_key`:
```bash
curl -s -u 'user:pass' https://admin.DOMAIN/shm/v1/storage/manage/data | jq .user_key
```


