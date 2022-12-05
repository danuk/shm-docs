
---
title: Шаблоны
---

### Шаблоны


## Пример обновления шаблона `wg_manager`

```bash
curl -u "admin:admin" \
     -X "POST" \
     -H "Content-Type: text/html; charset=utf-8" \
     http://$SHM_URL/shm/v1/admin/template/wg_manager \
     -d @shm_actions_script.sh
```

