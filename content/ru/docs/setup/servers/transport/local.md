
---
title: "LOCAL"
weight: 100
---

LOCAL - локальный транспорт: код выполняется на сервере SHM.

Этот транспорт удобно использовать когда требуется выполнить шаблон средствами самого SHM.
Примеры можно найти [здесь]({{< ref "docs/setup/templates/jobs" >}}).

## Управление результатом выполнения

При использовании LOCAL транспорта Вы можете явно указать результат выполнения задачи с помощью метода [task.answer()]({{< ref "docs/setup/templates/task#taskanswer" >}}):

```go
{{ user.add_bonus( 100, 'Акция' ) }}
{{ task.answer(
    status = TASK_SUCCESS
    msg = 'Бонусы начислены успешно'
) }}
```

## Создание задач из шаблонов

С помощью [spool.add()]({{< ref "docs/setup/templates/task#spooladd" >}}) Вы можете создавать новые задачи для выполнения собственных шаблонов:

```go
{{ result = spool.add(
    event = {
        name = "UPDATE"
        title = "Обновление настроек"
        settings = { transport = 'local' }
    }
    settings = {
        template_id = 'my_custom_template'
        user_service_id = us.id
    }
) }}
```

Для создания задач для всех пользователей используйте метод `job_users` с типом `Jobs`:

Указанный шаблон `reminder` будет выполнен для всех пользователей.

```go
{{ spool.add(
    event = {
      kind = "Jobs"
      method = "job_users"
      name = "Reminder"
      title = "connect"
      settings = { transport = 'local' }
    }
    settings = {
        template_id = "reminder"
    }
    prio = 1000
  )
}}
```

Для создания событий в контексте услуги пользователя используйте [us.make_custom_event()]({{< ref "docs/setup/templates/task#usmake_custom_event" >}}):

```go
{{ result = us.make_custom_event(
    name = 'CUSTOM_ACTION'
    title = 'Выполнение пользовательского действия'
    transport = 'local'
    template_id = 'my_template'
) }}
```

Подробнее о работе с задачами читайте в разделе [Работа с задачами]({{< ref "docs/setup/templates/task" >}}).

## Очереди

Очереди позволяют группировать задачи и управлять скоростью их выполнения с помощью параметра `rate_limit` (максимальное число задач в секунду).

При создании задач через `job_users` очередь создаётся автоматически. Чтобы создать очередь явно и передать её `id` в задачи, используйте `spool_queue.add()`:

```go
{{ queue_id = spool_queue.add(
    name = "Массовая рассылка"
    rate_limit = 10
) }}

{{ spool.add(
    event = {
      kind = "Jobs"
      method = "job_users"
      name = "Reminder"
      title = "connect"
      period = 3600
      server_gid = 1
    }
    settings = {
        template_id = "reminder"
        queue_id = queue_id
    }
    prio = 1000
    status = "PAUSED"
  )
}}
```

### Параметры очереди

| Параметр | Описание |
|---|---|
| `name` | Название очереди |
| `rate_limit` | Максимальное количество задач в секунду (`NULL` — без ограничений) |
| `status` | Статус: `active` или `paused` |

### Счётчики выполнения

Очередь автоматически отслеживает статистику выполнения задач:

| Поле | Описание |
|---|---|
| `total_added` | Всего добавлено задач |
| `cnt_success` | Выполнено успешно |
| `cnt_fail` | Завершено с ошибкой |
| `cnt_skipped` | Пропущено |
| `cnt_stuck` | Зависло |
| `cnt_pending` | Ожидают выполнения |
| `finished_at` | Время завершения очереди (заполняется автоматически, когда все задачи обработаны) |


