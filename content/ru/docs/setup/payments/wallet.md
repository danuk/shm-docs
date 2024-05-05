
---
title: Wallet
weight: 1
---

Настройка платежной системы Telegram Wallet

> SHM не умеет конвертировать валюты, поэтому в биллинге следует установить все цены для услуг в той валюте какую указываете в `currencyCode`

* Сохраните в [конфиг]({{< ref "/docs/api/config" >}}) SHM следующие данные:
```yaml
{
  "pay_systems": {
    "wallet": {
      "api_key":"ВАШ_Секретный_ключ",
      "autoConversionCurrency":"Перечисление_на_ваш_счёт_TON_BTC_USDT",
      "currencyCode":"валюта_RUB_USD_EUR_USDT_BTC_TON",
      "description":"укажите_наименования_товара_для_чека",
      "failReturnUrl":"укажите_url_для_возврата_после_неудачного_платежа",
      "name":"Wallet",
      "returnUrl":"укажите_url_для_возврата_после_платежа",
      "show_for_client":"true"
    }
  }
}
```

Ваш секретный ключ можно посмотреть в [Настройках](https://pay.wallet.tg/).
 
* В настройках вашего магазина включите тумблер `Включить Webhooks` в появившемся поле укажите:

  `https://admin.ВАШ_ДОМЕН/shm/pay_systems/wallet.cgi`


### Пример ссылки для создания платежа:

`https://admin.ВАШ_ДОМЕН/shm/pay_systems/wallet.cgi?action=create&amount=123`

