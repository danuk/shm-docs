---
title: "Обновление SHM"
linkTitle: "upgrade"
weight: 100
description: >-
     Как обновить версию SHM
---

## Обновление версии SHM

> Рекомендуем делать резервную копию БД SHM перед обновлением

1. Перейдите в директорию с Вашим файлом: `docker-compose.yml`
2. Скачайте новые имаджы: `docker-compose pull`
3. Перезапустите контейнеры: `docker-compose down && docker-compose up -d`


