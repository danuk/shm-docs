
---
title: ЮMoney
weight: 100
---

ЮMoney - Российская платежная система. ЮMoney предоставляет платежную форму для приёма платежей от ваших клиентов.
Для того, чтобы автоматически заполнять поле "Назначение платежа", мы будем использовать шаблон SHM.

Создайте платежную форму на сайте [ЮMoney](https://yoomoney.ru/quickpay/form), а в назначении платежа
укажите `user.id`

Скопируйте код вашей платежной формы, и замените в нём `user.id` на `{{ user.id }}`.

У Вас должен получится код похожий на этот:

```html
<iframe
    src="https://yoomoney.ru/quickpay/shop-widget?writer=seller&targets={{ user.id }}&targets-hint=&default-sum=&button-text=11&payment-type-choice=on&mobile-payment-type-choice=on&hint=&successURL=&quickpay=shop&account=XXXXX" width="422" height="224" frameborder="0" allowtransparency="true" scrolling="no">
</iframe>
```

Вы можете использовать код из примера, заменив `account=XXXXX` на Ваш.

Подробнее о создании и настройке шаблонов можно прочитать [здесь]({{< ref "/docs/api/templates" >}})

Информация о том, как принимать платежи, используя этот шаблон, читайте [здесь]({{< ref "/docs/setup/pay/payments#юmoney" >}})

