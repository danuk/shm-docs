---
title: "Разное"
weight: 100
description: >-
     Разные полезные штуки
---

## Просмотр логов

Если что-то пошло не так и есть необходимость посмотреть логи, то:

1. Перейдите в директорию с Вашим файлом: `docker-compose.yml`
2. Выполните команду: `docker compose logs -f`

## Сброс пароля пользователя admin

Если Вы забыли пароль администратора, то можно его сбросить следующим образом:

1. Перейдите в директорию с Вашим файлом: `docker-compose.yml`
2. Выполните команду: `docker compose exec core scripts/reset_admin_pass.cgi`

## Разрешить вход по паролю

Если вы потеряли доступ к Passkey, то можно включить вход по паролю следующим образом:

1. Перейдите в директорию с Вашим файлом: `docker-compose.yml`
2. Выполните команду: `docker compose exec core scripts/enable_password_auth.cgi`

## Редактирование стилей (CSS) клиентского кабинета

1. Перейдите в директорию с Вашим файлом: `docker-compose.yml`
2. Скачайте текущий файл стилей: `wget https://raw.githubusercontent.com/danuk/shm-client/master/app/assets/css/styles-alternative.css`
3. Раскоментируйте строки в файле `docker-compose.yml`:
```
#    volumes:
#      - ./styles-alternative.css:/app/assets/css/styles-alternative.css
```
4. Перезапустите SHM:
```
docker compose down
docker compose up -d
```

Теперь Вы можете править файл стилей `styles-alternative.css` и наблюдать результат в клиентском интерфейсе пользователя (обновляя страницу браузера).

## Перенос SHM на другой сервер

Если возникла необходимость в переносе SHM на другой сервер, то выполните следующие шаги:

1. [Сделайте]({{< ref "docs/manage/mysql" >}}) архивную копию БД на текущем сервере 
2. [Установите](https://docs.myshm.ru/docs/install/) и запустите SHM на новом сервере
3. [Восстановите]({{< ref "docs/manage/mysql" >}}) БД нового SHM из архивной копии (с первого шага)

