
---
title: "Kubernetes"
---

## Описание

SHM разработан и адаптирован для работы в Kubernetes.

### Настройка и запуск SHM:

- Добавьте репозиторий:

`helm repo add danuk https://danuk.github.io/shm`

- Установите SHM:

`helm install my-shm danuk/shm --set 'global.shm.domain=$DOMAIN'`


> Возможен автоматический выпуск SSL сертификатов для доменов, если в кластере Kubernetes установлен и настроен cert-manager

Основные параметры:
- `global.shm.domain` - доменное имя
- `global.shm.version` -  версия SHM. По-умолчанию установлен `latest`
- `global.shm.db_user` -  Пользователь БД
- `global.shm.db_pass` -  Пароль БД
- `global.shm.db_root_pass` -  Пароль пользователя root БД
- `global.shm.db_name` -  Имя БД
- `mysql.primary.persistence.size` - Размер диска для БД (по-умолчанию 1Gi)
- `helm-common.ingress.admin.acme` - укажите 1 или true для выпуска SSL сертификата для админки
- `helm-common.ingress.client.acme` - укажите 1 или true для выпуска SSL сертификата для клиентского кабинета

Полные настройки доступны в файле helm чарта: `values.yaml`

## Настройка SHM

В Административном интерфейсе необходимо прописать актуальные настройки.

1. Зайдите в интерфейс администратора: `https://admin.$DOMAIN` (admin:admin)
2. Перейдите в раздел: "Настройки" -> "Конфигурация" и выполните соответствующие настройки, например:
 - В настройке `api` укажите реальный адрес, например: `https://admin.$DOMAIN`
 - В настройке `cli` укажите реальный адрес, например: `https://bill.$DOMAIN`
 - В настройке `mail` укажите ваш реальный обратный адрес `from`
 - В настройке `company` укажите реальное название компании в поле `name`


