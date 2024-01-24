
---
title: ЮMoney
weight: 1
---

Настройка платежной системы ЮMoney

* Сохраните в [конфиг]({{< ref "/docs/api/config" >}}) SHM следующие данные:
```yaml
{
  "pay_systems": {
    "yoomoney": {
      "account":"АККАУНТ_ЮMONEY",
      "secret":"СЕКРЕТ_ЮMONEY"
    }
  }
}
```

Аккаунт можно посмотреть в [платежной форме](https://yoomoney.ru/quickpay/form). Если форма еще не создана, введите любое значение в поле "Назначение платежа", и платежная форма появится. Найдите в ней значение вашего аккаунта (`account=`).
Секрет можно посмотреть [здесь](https://yoomoney.ru/transfer/myservices/http-notification).

* Для HTTP-уведомлений на странице: https://yoomoney.ru/transfer/myservices/http-notification настройте URL:

  `https://admin.ВАШ_ДОМЕН/shm/pay_systems/yoomoney.cgi`


### Пример ссылки для создания платежа:

`https://admin.ВАШ_ДОМЕН/shm/pay_systems/yoomoney.cgi?action=create&amount=123`

