
---
title: CryptoCloud.plus
weight: 100
---

Сервис позволяет принимать крипто-валюту от ваших клиентов.

> SHM не умеет конвертировать валюты, поэтому в биллинге следует установить все цены для услуг в долларах, и принимать только крипто-валюту: USDT.

## Настройка платежной системы CryptoCloud.plus

* Зарегистрируйтесь в сервисе CryptoCloud.plus (https://cryptocloud.plus/)
* Сохраните в [конфиг]({{< ref "/docs/api/config" >}}) SHM следующие данные:
```yaml
{
  "pay_systems": {
    "cryptocloud": {
      "name": "CryptoCloud"
      "api_key":"API_KEY"
      "shop_id":"Идентификтор магазина"
      "template_id": "crypto-cloud-plus"
      "show_for_client": true
    }
  }
}
```
* Настройте URL для уведомлений:

  `https://ВАШ_ДОМЕН/shm/pay_systems/cryptocloud.cgi`


### Пример ссылки для создания платежа:

`https://ВАШ_ДОМЕН/shm/pay_systems/cryptocloud.cgi?action=create&amount=123`

