---
title: "MySQL"
weight: 10
description: >-
     Бэкапы и восстановление
---

## Создание архивной копии (Backup)

```go
docker exec shm_mysql_1 /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysqldump -u root shm' > shm_backup.sql
```

## Восстановление БД из архива (Restore)

```go
docker exec -i shm_mysql_1 /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysql -u root shm' < shm_backup.sql
```

## Периодические бэкапы

mysql_backup.sh
```go
mkdir -p /opt/shm/backups
docker exec shm_mysql_1 /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysqldump -u root shm' | gzip > /opt/shm/backups/shm_$(date +%d%m%Y-%H%M%S).sql.gz
```
