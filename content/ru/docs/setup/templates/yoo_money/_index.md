
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

Пример шаблона платежной формы:
```html
<iframe
src="https://yoomoney.ru/quickpay/shop-widget?writer=seller&targets=%D0%9E%D0%BF%D0%BB%D0%B0%D1%82%D0%B0%20%D0%BF%D0%BE%20%D0%B4%D0%BE%D0%B3%D0%BE%D0%B2%D0%BE%D1%80%D1%83%20{{ user.id }}&targets-hint=&default-sum=100&label={{ user.id }}&button-text=12&payment-type-choice=on&hint=&successURL=&quickpay=shop&account={{ config.pay_systems.yoomoney.account }}" width="100%" height="198" frameborder="0" allowtransparency="true" scrolling="no">
</iframe>
```

Подробнее о создании и настройке шаблонов можно прочитать [здесь]({{< ref "/docs/api/templates" >}})

Информация о том, как принимать платежи, используя этот шаблон, читайте [здесь]({{< ref "/docs/setup/payments/yoomoney" >}})

