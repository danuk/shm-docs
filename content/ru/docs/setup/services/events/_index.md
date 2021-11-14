
---
title: "События"
---

События генерируются SHM, для возможности привязки команд.

Список cобытий:
| Event             | Описание                                                          |
|:------------------|:------------------------------------------------------------------|
| create            | Услуга создана и оплачена (впервые)                               |
| not_enought_money | Не хватает денег для создания услуги                              |
| prolongate        | Услуга продлена (хватило денег для продления активной услуги)     |
| block             | Услуга заблокирована (нехватка денег для продления, либо вручную) |
| activate          | Услуга активирована (возобновлена после блокировки)               |
| remove            | Услуга удалена                                                    |


