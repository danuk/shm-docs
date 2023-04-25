
---
title: ЮKassa
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
      "template_id": "yookassa_template"
      "show_for_client": true
    }
  }
}
```
* В разделе "Интеграция" -> "HTTP-уведомления" (https://yookassa.ru/my/merchant/integration/http-notifications) укажите URL для уведомлений вида: https://admin.ВАШ-ДОМЕН/shm/pay_systems/yookassa.cgi
* Создайте шаблон с именем `yookassa_template` вида:
```
<form action="/shm/pay_systems/yookassa.cgi" method="POST" target="_blank" name="userForm">
    <input type="hidden" name="action" value="create" />
    <div class="modal-header">
        <h4 class="modal-title">Оплата с помощью сервиса ЮKassa</h4>
    </div>
    <div class="modal-body" style="min-height: 0vh;">
        <div class="form-group">
            <label class="col-sm-4 control-label">Сумма в рублях:</label>
            <div class="col-sm-8">
                <input type="number" name="amount" class="form-control" value=10 required=1>
            </div>
        </div>
    </div>

    <div class="modal-footer">
        <input class="btn btn-primary" ng-disabled="userForm.$invalid" type="submit" value="Далее" />
    </div>
</form>
```

* Если используете Telegram bot, то можете использовать ссылку для оплаты вида:
`https://{{ config.api.url }}/shm/pay_systems/yookassa.cgi?user_id={{ user.id }}&action=create&amount={{ user.pays.forecast.total }}&message_id={{ message.message_id + 1 }}`

