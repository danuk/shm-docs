
---
title: Приём платежей
---

### Пример зачисления платежа клиенту

> Платеж возможно зачислить только из под прав администратора

```bash
curl -u "login:password" \
     -H "Content-Type: application/json" \
     -X PUT http://$SHM_URL/shm/v1/admin/user/payment \
     -d '{"user_id": 1, "money": 123.45, "pay_system_id": "manual"}'
```

Где:
 - user_id - идентификатор пользователя для зачисления
 - money - сумма для зачисления
 - pay_system_id - идентификатор платежной системы. Произвольное слово, длиной не более 16 символов
