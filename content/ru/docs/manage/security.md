---
title: "Безопасность"
weight: 200
description: >-
     Настройка безопасности SHM
---

## Безопасность API

SHM имеет встроенные системы безопасности, такие как:
- Общее ограничение количества запросов в API за определенное время (RPS)
- Ограничения на количество запросов в методы аутентификации
- Ограничения на количество запросов в методы регистрации клиентов и их услуг
- Проверка токенов Telegram и многие другие

### Лимиты

При превышении одного из следующих лимитов пользователь получит ошибку HTTP: `429 Too Many Requests`.

Лимиты накладываются на IP-адрес пользователя.

#### Общие ограничения HTTP запросов в API
- 1 запрос в секунду. Допускается кратковременное превышение этого лимита до 5 запросов в секунду.

> Эти лимиты не распространяются на внутренние IP-адреса (127.0.0.0/8, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16)

#### Включение/отключение HTTP rate limiting

HTTP rate limiting на уровне nginx **отключен по умолчанию**. Для его включения необходимо установить переменную окружения `ENABLE_RATE_LIMIT=true`.

**Включение rate limiting:**

```yaml
services:
  api:
    environment:
      ENABLE_RATE_LIMIT: "true"
```

**Настройка через .env файл:**
```bash
ENABLE_RATE_LIMIT=true
```

**Поведение в зависимости от значения:**

| Значение `ENABLE_RATE_LIMIT` | HTTP rate limiting |
|------------------------------|-------------------|
| `true` | ✅ **Включен** |
| `false` | ❌ **Отключен** |
| Не указано | ❌ **Отключен** (по умолчанию) |
| Пустая строка | ❌ **Отключен** |

> **Важно**: Отключение HTTP rate limiting не влияет на внутренние лимиты SHM (аутентификация, регистрация и т.д.). Эти лимиты всегда активы при наличии Redis.

#### Настройка проброса реальных IP-адресов

**Критически важно**: Для корректной работы всех типов лимитов необходимо убедиться, что в логах контейнера API отображаются реальные IP-адреса клиентов, а не IP прокси-сервера или балансировщика нагрузки.

**Проверка текущих IP в логах:**

Перейдите в директорию, где находится ваш файл: `docker-compose.yml` и выполните команду:

```bash
# Просмотр логов API контейнера
docker compose logs api --tail=10

# 'remote_addr' должен содержать реальные IP клиентов, а не 172.x.x.x или другие внутренние адреса
```

Если в логах отображаются внутренние IP-адреса (например, `172.18.0.1`, `10.0.0.1`), необходимо настроить прокси-сервер для передачи заголовков с реальными IP.

### Настройка проброса IP для различных прокси-серверов

#### 1. Nginx (рекомендуемый)

**Базовая конфигурация:**
```nginx
server {
    ...

    # Передача реальных IP адресов
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    ...
}
```

#### 2. Caddy

**Базовая конфигурация (Caddyfile):**
```caddy
your-domain.com {
    reverse_proxy localhost:8080 {
        header_up X-Real-IP {remote_host}
        header_up X-Forwarded-For {remote_host}
    }
}
```

#### 3. HAProxy

**Базовая конфигурация:**
```haproxy

frontend shm_frontend
    ...

    # Добавление заголовков с реальными IP
    option forwardfor
    http-request set-header X-Real-IP %[src]

    ...
```

### Настройка для облачных сервисов

#### Cloudflare
```nginx
# Добавить в nginx конфигурацию
set_real_ip_from 173.245.48.0/20;
set_real_ip_from 103.21.244.0/22;
set_real_ip_from 103.22.200.0/22;
set_real_ip_from 103.31.4.0/22;
set_real_ip_from 141.101.64.0/18;
set_real_ip_from 108.162.192.0/18;
set_real_ip_from 190.93.240.0/20;
set_real_ip_from 188.114.96.0/20;
set_real_ip_from 197.234.240.0/22;
set_real_ip_from 198.41.128.0/17;
set_real_ip_from 162.158.0.0/15;
set_real_ip_from 104.16.0.0/13;
set_real_ip_from 172.64.0.0/13;
set_real_ip_from 131.0.72.0/22;

real_ip_header CF-Connecting-IP;
```

#### AWS Application Load Balancer
В конфигурации ALB убедитесь, что включены атрибуты:
- `routing.http.preserve_host_header.enabled = true`
- `routing.http.x_amzn_trace_id.enabled = true`

### Устранение проблем

#### Проблема: В логах отображаются внутренние IP
**Решение:**
1. Убедитесь, что прокси передает заголовки `X-Forwarded-For` или `X-Real-IP`
2. Проверьте настройки `set_real_ip_from` в nginx.conf контейнера API
3. Убедитесь, что IP прокси-сервера включен в `set_real_ip_from`

#### Проблема: Rate limiting не работает для внешних IP
**Решение:**
1. Проверьте, что в логах отображаются реальные IP клиентов
2. Убедитесь, что `ENABLE_RATE_LIMIT=true`
3. Проверьте, что IP клиента не входит в `TRUSTED_IPS`

#### Исключение доверенных IP-адресов из лимитов

Вы можете настроить список доверенных IP-адресов, которые будут исключены из всех ограничений rate limiting с помощью переменной окружения `TRUSTED_IPS`.

> **Примечание**: Переменная `TRUSTED_IPS` работает независимо от `ENABLE_RATE_LIMIT`. Доверенные IP исключаются из внутренних лимитов SHM даже при отключенном HTTP rate limiting.

**Настройка через docker-compose.yml:**

```yaml
services:
  api:
    environment:
      ENABLE_RATE_LIMIT: "true"  # Включить HTTP rate limiting
      TRUSTED_IPS: "192.168.1.0/24,10.0.0.1,172.20.0.0/16"
```

**Поддерживаемые форматы:**
- Отдельные IP-адреса: `192.168.1.100`
- CIDR диапазоны: `192.168.1.0/24`, `10.0.0.0/8`
- IPv6 адреса и диапазоны: `2001:db8::/32`
- Список через запятую: `192.168.1.100,10.0.0.0/8,172.16.0.1`

**Область действия:**
- **Nginx rate limiting** - исключение на уровне веб-сервера
- **Application rate limiting** - исключение на уровне приложения SHM
- Применяется ко всем типам ограничений (HTTP запросы, аутентификация, регистрация и т.д.)

**Пример настройки:**
```yaml
services:
  api:
    environment:
      # Исключить корпоративную сеть и админские IP
      TRUSTED_IPS: "192.168.0.0/16,10.1.1.100,10.1.1.101"
```

#### Внутренние лимиты SHM

- Неуспешная аутентификация пользователя: 5 за 3 минуты (блокировка на 3 минуты)
- Регистрация пользователя: 5 за час (блокировка на час)
- Регистрация услуги пользователем: 5 за 10 минут (блокировка на 10 минут)
- Неуспешная аутентификация Telegram: 5 за час (блокировка на час)

> Для работы этих ограничений необходимо убедиться, что запущен Redis.

## Блокировка IP-адресов пользователей на сервере

Вы можете настроить автоматическую блокировку IP-адресов на уровне сервера с помощью fail2ban. Это обеспечит дополнительную защиту от злоупотреблений API.

### Настройка логирования

#### 1. Настройка Docker Compose

Раскомментируйте в файле `docker-compose.yml` строки для логирования API контейнера:

```yaml
services:
  api:
    # ... другие настройки ...
    logging:
      driver: syslog
      options:
        syslog-address: "udp://127.0.0.1:514"
        tag: "shm-api"
```

#### 2. Настройка rsyslog

Создайте конфигурацию rsyslog для приема логов от Docker:

```bash
# Создайте файл /etc/rsyslog.d/49-docker.conf
sudo tee /etc/rsyslog.d/49-docker.conf << 'EOF'
# Включить UDP модуль для приема логов от Docker
module(load="imudp")
input(type="imudp" port="514")

# Шаблон для очистки JSON логов от Docker префиксов
$template SHMAPITemplate,"%msg:2:$%\n"

# Направить логи SHM API в отдельный файл
if $programname == 'shm-api' then /var/log/shm-api.log;SHMAPITemplate
& stop
EOF

# Создайте директорию для логов
sudo mkdir -p /var/log
sudo touch /var/log/shm-api.log
sudo chmod 644 /var/log/shm-api.log

# Перезапустите rsyslog
sudo systemctl restart rsyslog
```

### Настройка fail2ban

#### 1. Установка fail2ban

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install fail2ban
```

#### 2. Создание фильтра

Создайте фильтр для обработки JSON логов SHM API:

```bash
sudo tee /etc/fail2ban/filter.d/shm-api-json-429.conf << 'EOF'
# Fail2Ban filter for SHM API JSON format logs
[Definition]

failregex = ^.*"remote_addr"\s*:\s*"<HOST>".*"status"\s*:\s*429.*$
ignoreregex = ^.*"remote_addr"\s*:\s*"<HOST>".*"status"\s*:\s*(?:200|201|202|204|301|302|304).*$
datepattern = "time"\s*:\s*"%%Y-%%m-%%dT%%H:%%M:%%S
EOF
```

#### 3. Создание jail конфигурации

Создайте jail для автоматической блокировки:

```bash
sudo tee /etc/fail2ban/jail.d/shm-api-json-429.conf << 'EOF'
# SHM API protection jail
[shm-api-json-429]
enabled = true
port = http,https
filter = shm-api-json-429
logpath = /var/log/shm-api.log
maxretry = 5
findtime = 300
bantime = 3600
ignoreip = 127.0.0.1/8 ::1 172.16.0.0/12 192.168.0.0/16 10.0.0.0/8
action = iptables-multiport[name=shm-api-429, port="http,https", protocol=tcp]
EOF
```

#### 4. Параметры блокировки

- **maxretry**: 5 - количество попыток до блокировки
- **findtime**: 300 секунд (5 минут) - период анализа попыток
- **bantime**: 3600 секунд (1 час) - время блокировки IP-адреса

### Запуск и проверка

#### 1. Запуск fail2ban

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

#### 2. Проверка статуса

```bash
# Общий статус fail2ban
sudo fail2ban-client status

# Статус конкретного jail
sudo fail2ban-client status shm-api-json-429

# Просмотр заблокированных IP-адресов
sudo fail2ban-client get shm-api-json-429 banip
```

#### 3. Мониторинг

```bash
# Мониторинг логов fail2ban
sudo tail -f /var/log/fail2ban.log | grep shm-api

# Мониторинг логов API
sudo tail -f /var/log/shm-api.log

# Проверка iptables правил
sudo iptables -L f2b-shm-api-json-429 -n
```

### Управление блокировками

#### Разблокировка IP-адресов

```bash
# Разблокировать конкретный IP-адрес
sudo fail2ban-client set shm-api-json-429 unbanip 192.168.1.100

# Разблокировать все IP-адреса в jail
sudo fail2ban-client unban --all
```

### Настройка уведомлений

Для получения уведомлений о блокировках добавьте в jail конфигурацию:

```ini
[shm-api-json-429]
# ... остальные параметры ...

# Email уведомления
action = %(action_mwl)s
destemail = admin@example.com
sendername = Fail2Ban SHM API
sender = fail2ban@example.com
```

### Логротация

Настройте ротацию логов для предотвращения переполнения диска:

```bash
sudo tee /etc/logrotate.d/shm-api << 'EOF'
/var/log/shm-api.log {
    daily
    rotate 30
    compress
    delaycompress
    missingok
    notifempty
    create 644 syslog syslog
    postrotate
        /bin/systemctl reload rsyslog > /dev/null 2>&1 || true
    endscript
}
EOF
```

### Рекомендации по безопасности

1. **Мониторинг**: Регулярно проверяйте логи fail2ban и заблокированные IP-адреса
2. **Настройка**: Корректируйте параметры `maxretry`, `findtime` и `bantime` в зависимости от нагрузки
3. **Whitelist**: Добавляйте доверенные IP-адреса в `ignoreip` для избежания случайных блокировок
4. **Интеграция с TRUSTED_IPS**: Синхронизируйте списки доверенных IP между `TRUSTED_IPS` и `ignoreip` в fail2ban
5. **Резервное копирование**: Регулярно создавайте резервные копии конфигураций fail2ban
6. **Обновления**: Поддерживайте fail2ban в актуальном состоянии

> **Важно**: IP-адреса, указанные в переменной `TRUSTED_IPS`, автоматически исключаются из rate limiting на уровне приложения, но могут по-прежнему блокироваться fail2ban. Рекомендуется также добавлять их в параметр `ignoreip` конфигурации fail2ban.

### Устранение неполадок

#### Проблема: Jail не запускается

```bash
# Проверка конфигурации
sudo fail2ban-client --test

# Проверка логов
sudo journalctl -u fail2ban -n 50
```

#### Проблема: IP-адрес не блокируется

```bash
# Тестирование фильтра
sudo fail2ban-regex /var/log/shm-api.log /etc/fail2ban/filter.d/shm-api-json-429.conf

# Проверка формата логов
sudo tail -5 /var/log/shm-api.log
```


