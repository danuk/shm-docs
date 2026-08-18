
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

Подробнее о работе с задачами читайте в разделе [Работа с задачами]({{< ref "docs/setup/templates/task" >}}).


