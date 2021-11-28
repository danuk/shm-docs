
---
title: ЮMoney
weight: 100
---

ЮMoney - Российская платежная система. ЮMoney предоставляет платежную форму для приёма платежей от ваших клиентов.
Для того, чтобы автоматически заполнять поле "Назначение платежа", мы будем использовать шаблон SHM.

> Скорее всего, этот шаблон уже создан в вашей системе и этот шаг можно пропустить.

Создайте платежную форму на сайте [ЮMoney](https://yoomoney.ru/quickpay/form), а в назначении платежа
укажите любое значение, ниже мы его изменим вручную.

Вы можете использовать код из примера, если его настройки вам подходят, либо
скопируйте код вашей платежной формы, и измените в нём значение полей:
* `target={{ user.id }}`
* `account={{ config.pay_systems.yoomoney.account }}`

Пример платежной формы:
```html
<iframe
    src="https://yoomoney.ru/quickpay/shop-widget?writer=seller&targets={{ user.id }}&targets-hint=&default-sum=&button-text=11&payment-type-choice=on&mobile-payment-type-choice=on&hint=&successURL=&quickpay=shop&account={{ config.pay_systems.yoomoney.account }}" width="422" height="224" frameborder="0" allowtransparency="true" scrolling="no">
</iframe>
```

Подробнее о создании и настройке шаблонов можно прочитать [здесь]({{< ref "/docs/api/templates" >}})

Информация о том, как принимать платежи, используя этот шаблон, читайте [здесь]({{< ref "/docs/setup/pay/payments#юmoney" >}})

