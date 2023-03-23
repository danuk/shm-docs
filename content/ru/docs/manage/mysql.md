---
title: "MySQL"
weight: 10
description: >-
     Бэкапы и восстановление
---

## Создание архивной копии (Backup)

```go
docker-compose exec -T mysql /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysqldump -u root shm' > shm_backup.sql
```

## Восстановление БД из архива (Restore)

```go
docker-compose exec -T mysql /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysql -u root shm' < shm_backup.sql
```

## Периодические бэкапы

mysql_backup.sh
```go
DOCKER_COMPOSE_PATH="/opt/shm"
BACKUP_DIR="/opt/shm/backups"

mkdir -p ${BACKUP_DIR}
cd ${DOCKER_COMPOSE_PATH}
docker-compose exec -T mysql /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysqldump -u root shm' > ${BACKUP_DIR}/shm_$(date +%d%m%Y-%H%M%S).sql
```
