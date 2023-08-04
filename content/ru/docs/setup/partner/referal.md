
---
title: "Реферальная система"
linkTitle: "Рефералка"
weight: 10
---

Получение бонусов за приведенных клиентов по ссылке.

В SHM реализована двух-степенчатая реферальная программа.

В конфигурации биллинга в секции `billing->partner->income_percent` можно указать значение партнерского начисления в процентах.

Клиенты могут получать пожизненные бонусы за приведенных клиентов (рефералов) по следующей схеме:

- Для Web, клиент распространяет ссылку вида:

`https://bill.DOMAIN/#!/?partner_id=USER_ID`

где USER_ID это идентификатор клиента
> партнерскую ссылку клиент может получить в своём личном кабинете в профиле
- Для Telegram, клиент распространяет ссылку вида:

`https://t.me/Name_bot?start=USER_ID`

где USER_ID это идентификатор клиента

- Со всех платежей рефералов, которые зарегистрировались в SHM по партнерской ссылке, клиенту будут начилсяться бонусы в размере установленного процента `income_percent`.
- Так же будут начисляться 50% от бонусов реферала (если реферал тоже привлек новых клиентов).

