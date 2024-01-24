
---
title: ЮKassa
weight: 2
---

Сервис позволяет принимать оплату самозанятым, ИП и Юридическим лицам.

## Настройка платежной системы ЮKassa

* Зарегистрируйтесь в сервисе ЮKassa  (https://yookassa.ru/)
* Создайте секретный ключ на странице "Интеграция" -> "Ключи API" (https://yookassa.ru/my/merchant/integration/api-keys)
* Сохраните в [конфиг]({{< ref "/docs/api/config" >}}) SHM следующие данные:
```yaml
{
  "pay_systems": {
    "yookassa": {
      "name": "ЮKassa"
      "account_id": ВАШ_shopId
      "api_key": ВАШ_Секретный_ключ
      "customer_email": укажите_email_для_получения_чеков
      "description": укажите_наименования_товара_для_чека
      "return_url": укажите_url_для_возврата_после_платежа
      "show_for_client": true
    }
  }
}
```
* В разделе "Интеграция" -> "HTTP-уведомления" (https://yookassa.ru/my/merchant/integration/http-notifications) укажите URL для уведомлений вида:

  `https://admin.ВАШ-ДОМЕН/shm/pay_systems/yookassa.cgi`


### Пример ссылки для создания платежа:

`https://ВАШ_ДОМЕН/shm/pay_systems/yookassa.cgi?action=create&amount=123`

