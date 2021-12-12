
---
title: "Docker"
---

> Прежде чем начать, убедитесь, что на вашем сервере запущен демон синхронизации времени (NTP)

## Установка Docker

Docker - Самый простой способ запустить SHM на вашем сервере.

### Установите docker:

https://docs.docker.com/install/

### Установите docker-compose:

```bash
apt install docker-compose
```

Активируйте автозапуск:
```bash
systemctl enable docker
```

### Настройка и запуск SHM:

Скачайте файлы `docker-compose.yml` и `.env`

```bash
wget https://raw.githubusercontent.com/danuk/shm/master/docker-compose.yml
wget https://raw.githubusercontent.com/danuk/shm/master/.env
```

Сгенерируйте пароли для БД и измените их в файле `.env`

Так же Вы можете указать в файле `.env` временную зону в которой будет работать SHM. В дальнейшем эту зону менять нельзя!

Запустите SHM:
```bash
docker-compose up -d
```

### SHM будет доступен по адресу:

http://127.0.0.1:8080

Логин: admin

Пароль: admin


## Web сервер и доменные имена

Чтобы открыть SHM внешнему миру установите Nginx на ваш сервер, и настройте как написано ниже.

Настройте DNS вашего домена:
```bash
admin IN A ПУБЛИЧНЫЙ_IP_ВАШЕГО_СЕРВЕРА
bill  IN A ПУБЛИЧНЫЙ_IP_ВАШЕГО_СЕРВЕРА
```

### Установка nginx

```bash
apt install nginx
```

### Настройка nginx

Создайте файл `/etc/nginx/sites-available/admin.conf` на сервере:

(Замените `$DOMAIN` на ваш реальный домен)

```go
server {
  listen 80;
  server_name admin.$DOMAIN;

  location / {
    proxy_pass         http://127.0.0.1:8081;
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Proto $scheme;
  }
}
```

Создайте файл `/etc/nginx/sites-available/bill.conf` на сервере:

(Замените `$DOMAIN` на ваш реальный домен)

```go
server {
  listen 80;
  server_name bill.$DOMAIN;

  location / {
    proxy_pass         http://127.0.0.1:8082;
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
    proxy_set_header   X-Forwarded-Proto $scheme;
  }
}
```

Включите ваши сайты:
```bash
ln -s /etc/nginx/sites-available/admin.conf /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/bill.conf /etc/nginx/sites-enabled/
service nginx reload
```

### Добавление SSL сертификата (https)

Установите `certbot`:
```bash
apt install certbot python3-certbot-nginx
```

Создайте сертификаты для ваших доменов:
```go
certbot --nginx -d admin.$DOMAIN
certbot --nginx -d bill.$DOMAIN
```

Проверьте, что всё работает:

```https://admin.$DOMAIN```

Логин: admin

Пароль: admin


