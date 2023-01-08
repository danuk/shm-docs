
---
title: CryptoCloud.plus
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
* Настройте URL для уведомлений: `https://ВАШ_ДОМЕН/shm/pay_systems/cryptocloud.cgi`
* Создайте шаблон с именем `crypto-cloud-plus` вида:
```
<form action="/shm/pay_systems/cryptocloud.cgi" method="POST" target="_blank" name="userForm">
    <input type="hidden" name="action" value="create" />
    <div class="modal-header">
        <h4 class="modal-title">Оплата с помощью сервиса CryptoCloud.plus</h4>
    </div>
    <div class="modal-body" style="min-height: 0vh;">
        <div class="form-group">
            <label class="col-sm-4 control-label">Сумма в USDT:</label>
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


