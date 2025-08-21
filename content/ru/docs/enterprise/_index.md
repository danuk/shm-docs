---
linkTitle: "SHM Enterprise"
title: "Платная версия SHM (Enterprise Edition)"
weight: 300
---

Платная версия SHM: `Enterprise Edition (EE)` создана для подписчиков <a href="https://t.me/myshm_support_bot" target="_blank">платной</a> группы SHM.

Все новые функции сначала появляются в платной версии (EE), а спустя некоторое время появляются и в бесплатной версии.

> Некоторые функции доступны только в `EE` версии.

## Установка платной версии SHM

Установка платной версии подразумевает, что у Вас уже установлена бесплатная версия SHM.

При переключении на платную версию все настройки и данные сохраняются. Обратное переключение на бесплатную версию возможно, но некоторые функции платной версии перестанут работать.

- <a href="https://t.me/myshm_support_bot" target="_blank">Оплатите</a> подписку в группу платной поддержки биллинга SHM
- Вы получите файл `key.json`. Скачайте его и сохраните на свой сервер с SHM
- Выполните следующую команду на вашем сервере SHM:
  ```go
  cat key.json | docker login \
    --username json_key \
    --password-stdin \
    cr.yandex
  ```
- Замените Ваши файлы `docker-compose.yml` и `.env` Перейдите в директорию с текущим файлом и выполните команду:
  ```go
  curl -sO https://raw.githubusercontent.com/danuk/shm/master/enterprise/docker-compose.yml
  curl -sO https://raw.githubusercontent.com/danuk/shm/master/enterprise/.env
  ```
- Обновите Ваш SHM и перезапустите:
  ```go
   docker compose pull

   docker compose down
   docker compose up -d
   ```

