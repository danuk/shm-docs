
---
title: Cryptomus
weight: 100
toc_hide: true
hide_summary: true
---

Сервис позволяет принимать крипто-валюту от ваших клиентов.

> SHM не умеет конвертировать валюты, поэтому в биллинге следует установить все цены для услуг в той же валюте что указано в `currency`.

## Настройка платежной системы Cryptomus

* Зарегистрируйтесь в сервисе Cryptomus (https://cryptomus.com/ru)
* Сохраните в [конфиг]({{< ref "/docs/api/config" >}}) SHM следующие данные:
```yaml
{
  "pay_systems": {
    "cryptomus": {
      "name": "CryptoCloud"
      "api_key": "Платежный_api_ключ_из_настроек_мерчанта"
      "merchant": "ID_мерсанта_из_настроек_мерчанта"
      "currency": "RUB_или_USD"
      "url_success": "укажите_url_бота_для_возврата_после_удачного_платежа"
      "url_callback": "https://ВАШ_ДОМЕН/shm/pay_systems/cryptomus.cgi"
      "show_for_client": true
    }
  }
}
```
#### Дополнительные параметры:

`lifetime` в секундах, по умолчанию (3600) 1 час 
* min: 300
* max: 43200



#### Настройте URL для уведомлений:

в параметр `url_callback` добавьте ссылку

* https://ВАШ_ДОМЕН/shm/pay_systems/cryptomus.cgi"

### Пример ссылки для создания платежа:

`https://ВАШ_ДОМЕН/shm/pay_systems/cryptomus.cgi?action=create&amount=123&user_id=123`

