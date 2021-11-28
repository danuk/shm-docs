
---
title: "MySQL"
---

## Создание архивной копии (Backup)

```go
docker exec shm_mysql_1 /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysqldump -u root shm' > shm_backup.sql
```


## Восстановление БД из архива (Restore)

```go
docker exec -i shm_mysql_1 /bin/bash -c 'MYSQL_PWD=${MYSQL_ROOT_PASSWORD} mysql -u root shm' < shm_backup.sql
```


