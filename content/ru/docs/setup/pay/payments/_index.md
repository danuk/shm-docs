
---
title: Приём платежей
---

Приём платежей пользователей осуществляется путем заполнения одного из шаблонов платежей.

Шаблон платежа содержит HTML форму платежной системы. Шаблон позволяет заполнить ряд полей этой формы автоматически. Например для
того, чтобы понять какой пользователь совершил платеж, мы можем в поле "Назначение платежа” указать идентификатор пользователя.

В настройках платежной системы нужно указать URL, на который платежная система будет присылать информацию о поступлении платежа.

Вам необходимо взять готовый или написать свой скрипт, который будет принимать информацию от платежной системы о платеже и
зачислять этот платеж вашему клиенту в SHM по средствам [API]({{< ref "/docs/api/payments" >}}).


## ЮMoney

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

* Настройте URL: `https://admin.ВАШ_ДОМЕН/shm/pay_systems/yoomoney.cgi` для HTTP-уведомлений на странице: https://yoomoney.ru/transfer/myservices/http-notification

* Создайте [шаблон]({{< ref "/docs/setup/templates/yoo_money" >}}) для платежной системы


