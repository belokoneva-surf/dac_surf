# О товаре
## Общее
Данный инструмент предназначен быстрого доступа к информации о товаре.\

Например для быстрого перехода из любого раздела через разводной экран к информации о товаре, если у покупателя появился вопрос.

## Точки входа
По тапу на раздел “О товаре“ на разводном экране.

# Экран “О товаре“
Empty state экрана содержит:\

1. Кнопка “Бургер“ - по тапу открываем разводной экран.
2. Полноэкранная иконка “Статус сканера“ с текстовым пояснением.
    - Заголовок “Сканер готов” и подзаголовок "Отсканируйте код маркировки или штрихкод, чтобы получить информацию о товаре".
    - Заголовок "Сканер в режиме ожидания" и подзаголовок “Чтобы запустить сканер, нажмите кнопку сканирования на корпусе устройства“.

## Информация о товаре после сканирования
1. Кнопка “Бургер“ - по тапу открываем разводной экран.
2. Иконка “Статус сканера“ - по тапу отображем снек с пояснением состояния сканера.
3. Информация о товаре - после сканирования ШК или КМ товара, сравниваем полученное значение с кэшем:
    - Если есть совпадение - отправляем запрос на получение остатков товара /dwh/rests/v1/goods/
        - Пока ждём - шиммер на остатке без кнопки “Повторить“.
        - Успех - отображаем остаток и товар в резерве.
        - В случае получения ошибки - не отображаем “остаток“( вместо него отображаем шиммер) и отображаем снек с соответствующей ошибкой, по тапу на кнопку “Обновить“ рядом с остатком - повторно отправляем запрос.
        - ! Запрос необходимо кэшировать и брать данные из кэша, при этом необходимо повторно отправлять запрос если с момента последнего запроса прошло 5 минут.
    - Если нет - отображаем шторку "Код не распознан" (не блокируем сканнер, при сканировании нового ШК повторно сравниваем значение с кэшем и далее логика аналогичная описанной выше):
        - В поле под заголовком отображаем полученное от сканнера значение ШК, в случае есть сканировали КМ - отображаем серийный номер КМ.
        - Если серийный номер КМ не помещается в поле (более 13 символов) - обрезаем троеточием по тапу на которое разворачиваем поле (отображаем КМ полностью) и меняем троеточие на “Свернуть“ при этом при развороте КМ отображаем скролл и не меняем размер шторки о окна.

### Компоновка информации о товаре:
1. Название товара - offers.name из кэша получения товаров или получения дельты.
    - Отображаем полное название, никак не сокращаем.
    - Если отсутствует - не отображаем поле.
2. Блок “Маркировка“:
    - Если marking=true - “Подлежит маркировке с markingDate(в формате ДД месяц ГГГГ)" из кэша получения товаров или получения дельты.
    - Если marking=false - “Не подлежит маркировке“.
    - Если markingDate отсутствует - “Нет данных о маркировке“.
3. Поле “Цена“ - newPrice из /so/prices/v1/list, отображаем “Текущую цену“ по логике аналогичной описанной в п.5 описания экрана “Переоценка“.
    - Если newPrice отсутствует в кэше переоценки для этого товара - отображаем “прочерк“.
    - Если текущая цена по логике переоценки отсутствует - отображаем “прочерк“.
4. Поле “Старая цена“ - lastwhitePrice из /so/prices/v1/list отображаем поле только в случае если crossout=true.
    - Если lastwhitePrice отсутствует в кэше переоценки для этого товара - отображаем “прочерк“.
5. Поле “Остаток” - quantity (reserved) из /dwh/rests/v2/goods.
    - В случае получения ошибки - не отображаем “остаток“( вместо него отображаем шиммер) и отображаем снек с соответствующей ошибкой, по тапу на кнопку “Обновить“ рядом с остатком - повторно отправляем запрос.
6. Поле “ШК“ - отсканированный ШК или полученный из КМ.
7. Поле “Артикул“ - article из кэша получения товаров или получения дельты.
    - Если article отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
8. Поле “Цвет“/”Размер”/”Рост” - соответственно color ,size ,growth из кэша товаров или получения дельты.(получение товаров= синхронизация при выборе города, получение дельты=обновление этих данных на следующий день).
    - Если color,size ,growth отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
9. Поле “Состав“ - consist из кэша получения товаров или получения дельты.
    - Если consist отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
10. Поле “Уход“ - productCare из кэша получения товаров или получения дельты.
    - Если productCare отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
11. Поле “Производитель“ - manufacturer из кэша получения товаров или получения дельты.
    - Если manufacturer отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
12. Поле “Импортёр“ - importer из кэша получения товаров или получения дельты.
    - Если importer отсутствует в кэше товаров для этого товара - отображаем “Нет данных“.
13. Кнопка “Посмотреть остатки“ - по тапу переход в раздел “Остатки“.

> Для получения информации о следующем товаре необходимо отсканировать товар на текущем экране.

# Методы
Подробное описание параметров см. [swagger](https://github.com/surfstudio/melon-fashion-group-swagger)./

> При отправке каждого запроса необходимо передавать в хедерах [device-uuid, serial-number, user-agent](https://wiki.surfstudio.ru/x/1kZD).

POST:…/dwh/rests/v1/goods/- получение остатков конкретного товара в конкретном магазине/

#### Пример запроса

```{
    "store": [
        "000000856"
    ],
    "barcode": [
        "4603375484060"
    ]
}
```
#### Пример ответа

```{
    "items": [
        {
            "brand": "befree",
            "store": "000000997",
            "barcode": "4603374355552",
            "quantity": 5,
            "reserved": 0,
            "rn": 2699262510,
            "upd": "2022-03-21T00:14:26.4400000"
        }
    ],
    "count": 3442
}
```