
---
title: "Услуги"
weight: 10
---

## Услуги

Для того, чтобы оказать/продать услугу, её необходимо создать и настроить.

Создавать и управлять услугами удобно через интерфейс Администратора, либо через API.
Полное описание полей и настроек услуг вы можете посмотреть на странице API.

Описание полей услуг:
<table>
<tr>
    <th>Поле</th>
    <th>Тип</th>
    <th>Обязательное</th>
    <th>default</th>
    <th>Описание</th>
</tr>
<tr>
    <td>name</td>
    <td align="center">Integer</td>
    <td align="center">Да</td>
    <td align="center">-</td>
    <td>
        Название услуги. Например: "Тариф простой"
    </td>
</tr>
<tr>
    <td>category</td>
    <td align="center">String</td>
    <td align="center">Да</td>
    <td align="center">-</td>
    <td>
        Служит для группировки услуг. События для услуг создаются по этому полю
    </td>
</tr>
<tr>
    <td>cost</td>
    <td align="center">Double</td>
    <td align="center">Да</td>
    <td align="center">-</td>
    <td>
        Стоимость услуги за период
    </td>
</tr>
<tr>
    <td>period_cost</td>
    <td align="center">Integer</td>
    <td align="center">Нет</td>
    <td align="center">1</td>
    <td>
        Кол-во месяцев. Например, для продажи доменных имён обычно используется период в 12 месяцев
    </td>
</tr>
<tr>
    <td>next</td>
    <td align="center">Integer</td>
    <td align="center">Нет</td>
    <td align="center">-</td>
    <td>
        Идентификатор следующей услуги. `-1` - удалить услугу после истечения
    </td>
</tr>
<tr>
    <td>children</td>
    <td align="center">Array</td>
    <td align="center">Нет</td>
    <td align="center">-</td>
    <td>
        Дочерние услуги. Массив из идентификаторов дочерних услуг
    </td>
</tr>
<tr>
    <td>allow_to_order</td>
    <td align="center">Integer</td>
    <td align="center">Нет</td>
    <td align="center">-</td>
    <td>
        Установите 1, для разрешения услуги к заказу клиентом
    </td>
</tr>
<tr>
    <td>pay_in_credit</td>
    <td align="center">Integer</td>
    <td align="center">Нет</td>
    <td align="center">-</td>
    <td>
        Установите 1, для разрешения кредита для услуги
    </td>
</tr>
<tr>
    <td>config</td>
    <td align="center">JSON</td>
    <td align="center">Нет</td>
    <td align="center">-</td>
    <td>
        Произвольные данные в формате JSON
    </td>
</tr>

</table>

## Пользовательские услуги

Пользовательская услуга - это такая услуга, которая уже оказана пользователю.

Пользовательская услуга характеризуется статусом и датой истечения, так же стоимость пользовательской
услуги может отличаться от услуги, т.к. могут быть применены различные скидки для конкретного пользователя и его услуги.






