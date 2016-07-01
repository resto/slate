---
title: Resto API

search: false

---

<h1 id="Resto-API">Resto API</h1>

<h2 id="Доступ-к-API">Доступ к API</h2>

Версия 1.0.

Аутентификация делается через http-base-auth. Запросы к Resto API выполняются http-методом GET в домене api.resto.ru.

<h2 id="Выдача">Выдача</h2>

API возвращает данные в формате JSON.

<h3 id="Выдача">"Ленивая" выдача</h3>

> Пример запроса:

```shell
http://api.resto.ru/places.json?ids=7477,8&include=title,id,lat,lat
```

Данные выдаются только по явному запросу. Из полей - только ID'ы, остальные поля и страницы нужно запрашивать явно. По умолчанию выдается только первая страница (даже если явно пейджинация не была явно запрошена). Для указания полей используется опция `include`.

Если у заведения отсутствует какое-либо значение, то она просто не выводится. Например, если нет status'а (т.е. заведение не относится к "Закрытым", "Новым" и т.п.)

<h3 id="Пейджинация">Пейджинация</h3>

> Примеры пейджинации:

```shell
http://api.resto.ru/places.json?per_page=3
http://api.resto.ru/places.json?region=spb&page=2
```

По умолчанию выдается только первая страница (даже если явно пейджинг не был явно запрошен).

Управлять пейджинацией можно через не обязательные параметры `per_page` (количество объектов на "странице", по умолчанию - 10) и `page` - номер страницы (по умолчанию - 1).

<h1 id="Заведения">Заведения</h1>

> Выборка заведений по id'ам:

```shell
http://api.resto.ru/places.json?ids=7477,4846
http://api.resto.ru/places.json?id=7477,4846
```

> С координатами:

```shell
http://api.resto.ru/places.json?include=title,id,lat,lat&long=37.5&lat=55.7&page=2
```

> В радиусе (указываются метры):

```shell
http://api.resto.ru/places.json?include=title,id&long=37.5&lat=55.7&radius=300000
```

> С адресом:

```shell
http://api.resto.ru/places.json?include=title,address&long=37.5&lat=55.7&page=2
```

> С картинками:

```shell
http://api.resto.ru/places.json?id=55&include=title,photos,logo,panorama
```

> С thumbnail'ом smallbox:

```shell
http://api.resto.ru/places.json?ids=7477&include=title,smallbox
```

> С указанной особенностью (станция метро в данном случае):

```shell
http://api.resto.ru/places.json?include=title,price-range,long,lat&with=dinamo
```

> Рестораны в которых можно сделать заказ столика:

```shell
http://api.resto.ru/places.json?include=title,order_partner&with=order_partner
```

> С рейтингом ZON'а:

```shell
http://api.resto.ru/places.json?ids=9786&include=title,zon_rating
```

> Заведения, в которых можно забронировать столик через ZON (возвращается zon_id):

```shell
http://api.resto.ru/places.json?ids=300392&include=title,zon_id
```

> Со статусом заведения:

```shell
http://api.resto.ru/places.json?with=new&include=title,status
```

> Заведения указанного региона:

```shell
http://api.resto.ru/places.json?region=spb
```

> С кольцом (Садовым, Бульварным и т.п.):

```shell
http://api.resto.ru/places.json?with=out-of-town&include=title,ring
```

> Заведения, измененные с указанной даты:

```shell
http://api.resto.ru/places.json?include=title,updated&after=2015-04-01T13:15:58+04:00
```

Все параметры возможные при запросе places:

* особенности (т.е. опции, которые можно использовать для `include`):
  * properties (все особенности заведения)
  * title (название)
  * address (адрес)
  * phone (телефоны)
  * lat (широта геокоординаты)
  * long (долгота геокоординаты)
  * cuisine (кухни)
  * price-range (средний счет)
  * subway-station (станции метро)
  * type (тип заведения: ресторан, кафе и т.п.)
  * zon_rating
  * zon_id
  * order_partner (в этом ресторане можно забронировать столик)
  * description
  * ring (кольца: Бульварное, Садовое и т.п.)
  * status (особый статус: закрытое, новое и т.п.)
* картинки (выдают url или массив url'ов):
  * has_photos
  * photos (фотографии заведения)
  * smallbox (64x64px, выдается одна картинка, логика ее выбора - на стороне API)
  * middlebox (96x96px, выдается одна картинка, логика ее выбора - на стороне API)
  * logo (логотип)
  * panorama (круговая панорама)
* фильтры:
  * with (получить список возможных значений для `with` и `with_any` можно через [словари](#Словари))
  * with_any
  * starts_with (символы, с которых начинается название)
  * ids
  * id
  * region (фильтр по городам)
* геопозиция (действует как фильтр):
  * lat
  * long
  * radius (расстояние в метрах от указанной геокоординаты)
* пейджинация:
  * page (номер страницы)
  * per_page (количество заведений на странице)
* сортировка:
  * sort_by ([подробно о сортировке](#Сортировка))
* поиск по title:
  * search ([подробно о поиске](#Поиск))


<h2 id="Сортировка">Сортировка</h2>

>Сортировка по геоособенности:

```shell
http://api.resto.ru/places.json?include=title&with=dinamo&sort_by=dinamo
```

Если в запросе указана исходная точка, то сортировка делается по расстоянию от этой точки. Во всех прочих случаях сортировка делается по `title`. Можно явно указать сортировку через параметр `sort_by`.
При сортировке по геоособенности (метро, точка и т.п.) выдается расстояние до этой точки (поле `distance`).

<h2 id="Поиск">Поиск</h2>

> Пример поиска:

```shell
http://api.resto.ru/places.json?include=title,id&starts_with=де
```

> Поиск с фильтрами:

```shell
http://api.resto.ru/places.json?include=title,id&long=37.84&lat=55.63&radius=300&page=2&per_page=10&starts_with=де
```

Для поиска по названию заведения можно использовать `search` (поиск по подстроке) или `starts_with` (поиск по первым буквам названия).

<h1 id="Словари">Словари</h1>

> Полный список словарей:

```shell
http://api.resto.ru/vocabularies.json
```

Для получения списка особенностей или построения иерархической навигации можно запросить у API т.н. словари (vocabularies), которые выводятся в виде хэша.

Полученные опции можно использовать для запросов (через `with`, `with_any`).

<h2 id="Локации">Локации</h2>

> Локации для региона по умолчанию (Москва):

```shell
http://api.resto.ru/vocabularies/locations.json
```

> Локации для указанного региона:

```shell
http://api.resto.ru/vocabularies/locations.json?region=spb
```

Локация (aka геоособенность), это особенность, имеющая координату.

<aside class="notice">Пока локации это только станции метро.</aside>


<h2 id="Особенности">Особенности</h2>

> Запрос особенностей:

```shell
http://api.resto.ru/vocabularies/properties.json
```

Набор всевозможных параметров заведений.

<h1 id="Регионы">Регионы</h1>

> Список регионов:

```shell
http://api.resto.ru/regions.json
```

> Регионы с русской локалью:

```shell
http://api.resto.ru/regions.json?locale=ru
```

> Информация по указанному региону:

```shell
http://api.resto.ru/regions.json?ids=msk
```

> Заведения указанного Санкт-Петербурга, вторая страница:

```shell
http://api.resto.ru/places.json?region=spb&page=2
```

В выдаче можно запрашивать только заведения указанного города (региона). Список регионов и их идентификаторы (которые используются для фильтров) можно получить через список регионов.

<h1 id="ZON-API">ZON API</h1>

Версия 0.1.

Аутентификация выполняется через идентификатор партнера (является частью URL) и ключ (атрибут key в POST-запросе).

Запросы к API выполняются http-методом POST в домене z-o-n.ru.

URL'ы запросов имеют такой вид:

/_api/action_XXX/partner_YYY

где XXX - имя команды, YYY - ID партнёра

<h2 id="placeinforesto">placeinforesto</h2>

> Пример curl-вызова:

```shell
curl -d 'key=***&restoid=6901' http://z-o-n.ru/_api/action_placeinforesto/partner_**
```

> Пример ответа:

```json
{
   "place_id" : "12081",
   "place_graph" : "1",
   "place_tables_photos" : 1,
   "zals" : [
      {
         "zal_name" : "Vinoroom",
         "zal_kol_seat" : 11,
         "zal_pict" : "http://z-o-n.ru/i/zals/zal389.png",
         "zal_smoke" : "0",
         "zal_id" : "389"
      },
      {
         "zal_smoke" : "0",
         "zal_id" : "390",
         "zal_kol_seat" : 67,
         "zal_pict" : "http://z-o-n.ru/i/zals/zal390.png",
         "zal_name" : "Terrassa"
      }
   ]
}
```

Информация о ресторане по ID ресторана в базер Resto. Передается один параметр - `restoid`

**Возвращаемые значения:**

* place_id - ID по версии ЗОН
* place_graph -  наличие визуальной формы заказа
* zals - массив из залов:
  * zal_name - название зала
  * zal_smoke - можно ли курить в зале
  * zal_kol_seat - число посадочных мест в зале

<h2 id="worktime">worktime</h2>

Узнать время работы ресторана. Передается один параметр - `restoid`

Возращает время работы заведения и его ID в базе ZON.

> Пример curl-вызова:

```shell
curl -d 'key=***&restoid=7477' http://z-o-n.ru/_api/action_worktime/partner_**
```

> Пример ответа:

```json
{
   "place_id" : 11,
   "worktime" : {
      "place_mon" : "09:00:00-00:00:00",
      "place_sun" : "10:00:00-00:00:00",
      "place_fri" : "09:00:00-00:00:00",
      "place_tue" : "09:00:00-00:00:00",
      "place_thu" : "09:00:00-00:00:00",
      "place_wed" : "09:00:00-00:00:00",
      "place_sat" : "10:00:00-00:00:00"
   }
}
```

<h2 id="gettablelist">gettablelist</h2>

> Пример curl-вызова:

```shell
curl -d 'key=***&id=12081&&year=2016&month=02&day=07&hour=15&minutes=55&persons=1' http://z-o-n.ru/_api/action_gettablelist/partner_**
```

> Пример ответа:

```json
{
   "total" : 2,
   "tables" : [
      {
         "140x102url" : "https://z-o-n.ru/i/tables/140x102/table7837.png",
         "229x238url" : "https://z-o-n.ru/i/tables/229x238/table7837.png",
         "url" : "https://z-o-n.ru/i/tables/table7837.png",
         "table_id" : 7837
      },
      {
         "140x102url" : "https://z-o-n.ru/i/tables/140x102/table7844.png",
         "229x238url" : "https://z-o-n.ru/i/tables/229x238/table7844.png",
         "url" : "https://z-o-n.ru/i/tables/table7844.png",
         "table_id" : 7844
      }
   ]
}
```


Список столиков указанного ресторана, соответствующие запрашиваемым параметрам.

**Параметры запроса:**

* id - ID ресторана
* persons - количество персон (необязательный параметр, если не передан, то выводятся все доступные столики)
* smoke - зал для курящих или не курящих (возможные значения: 1, 0; необязательный параметр, если не передан, то выводятся все доступные столики)
* время и дата брони (необязательный параметр):
  * year
  * month
  * day
  * hour
  * minutes

<aside class="notice">
Если переданы все параметры даты-времени, то выводятся только свободные на это времени столики. Если хотя бы один из параметров даты-времени не передан, то выводятся все столики.
</aside>

**Возвращаемые значения:**

* total - количество найденных столов
* tables - список столов
  * table_id - ID столика
  * url - ссылка на фото столика
  * 140x102url - ссылка на фото столика 140x102
  * 229x238url - ссылка на фото столика 229x238

<aside class="notice">
URL'ы фотографий (url, 140x102url, 229x238url) выдаются только в том случае, если они есть в наличии.
</aside>

<h2 id="gettablecoord">gettablecoord</h2>

> Пример curl-вызова:

```shell
curl -d 'key=***&table_id=86' http://z-o-n.ru/_api/action_gettablecoord/partner_**
```

> Пример ответа:

```json
{
   "zalimagesmallurl" : "https://z-o-n.ru/i/zals/297x242/zal13.png",
   "zalimageurl" : "https://z-o-n.ru/i/zals/zal13.png",
   "table_coords" : "330,233,370,262"
}
```

Получить картинку зала и координаты столика на ней.

На вход принимается один параметр - `table_id`. Узнать ID столика можно с помощью метода [gettablelist](#gettablelist)

На выходе:
* zalimageurl - URL картинки зала
* zalimagesmallurl - URL уменьшенной картинки зала (297x242)
* table_coords - 4 числа разделенные запятыми (координаты левого верхнего угла прямоугольника столика и координаты правого нижнего угла: left1, top1,  left2, top2)

<h2 id="search">search</h2>

> Пример curl-вызова:

```shell
curl -d 'key=***&name=рно' http://z-o-n.ru/_api/action_search/partner_**
```

> Пример ответа:

```json
{
   "arr" : [
      {
         "place_id" : "10056",
         "place_name" : "Ливорно"
      },
      {
         "place_id" : "18910",
         "place_name" : "Бонжорно"
      },
      {
         "place_name" : "Лазурное",
         "place_id" : "23583"
      },
      {
         "place_id" : "97849",
         "place_name" : "Соджорно"
      },
      {
         "place_name" : "Иль Форно",
         "place_id" : "101053"
      },
      {
         "place_id" : "103935",
         "place_name" : "Черновар"
      }
   ],
   "total" : 6
}

```

Поиск ресторана. В параметре `name` передается искомая подстрока.

<h2 id="snear">snear</h2>

> Пример curl-вызова:

```shell
curl -d 'key=***&latitude=55.45&longitude=37.36' http://z-o-n.ru/_api/action_snear/partner_**
```

> Пример ответа:

```json
{
   "arr" : [
      {
         "place_name" : "Якитория",
         "place_id" : "17327"
      },
      {
         "place_id" : "9608",
         "place_name" : "Пикник на обочине"
      },
      {
         "place_id" : "12520",
         "place_name" : "Скрип колеса"
      },
      {
         "place_id" : "96889",
         "place_name" : "Ватутинки"
      },
      {
         "place_name" : "Номер №1",
         "place_id" : "12275"
      },
      {
         "place_name" : "Русский погребок",
         "place_id" : "11086"
      },
      {
         "place_name" : "Атлетико",
         "place_id" : "11438"
      },
      {
         "place_id" : "11437",
         "place_name" : "Дольче Пьяно"
      },
      {
         "place_id" : "12915",
         "place_name" : "Заречье"
      },
      {
         "place_name" : "Банкетный зал &quot;Валуево&quot;",
         "place_id" : "96885"
      }
   ],
   "total" : 10
}
```

Узнать 10 ближайших расторанов, расположенных рядом с указанной точкой. Рестораны упорядочены по удаленности от точки (сначала ближайшие).

Возвращает массив со структурой, идентичной структуре для вызова [search](#search)

<h2 id="order">order</h2>

> Пример запроса:

```json
{
  "placeid": 6,
  "order": {
    "hour": 11,
    "minutes": 45,
    "day": 19,
    "month": 07,
    "year": 2011,
    "name": "some name",
    "phone": "+7 (112) 121-21-21",
    "email": "email@mail.ru",
    "persons": 1,
    "partner_comment": "some comment",
    "comment": "some comment",
    "userpartnerid": 111
  }
}
```

Заказ любого свободного столика в ресторане.

Параметры запроса:

* placeid - ID ресторана
* order:
  * persons - количество персон
  * name - имя гостя
  * phone - телефон гостя
  * email - email гостя
  * comment - комментарий гостя
  * hour - часы из времени заказа
  * minutes - минуты из времени заказа
  * day - день из времени заказа
  * month - месяц из времени заказа
  * year - год из времени заказа
  * partner_comment - комментарий пользователя API
  * userpartnerid - ID гостя в базе пользователя API (**NB!** обязательный параметр, заказы без userpartnerid не попадают в вывод методов изменений состояния заказа - changedorders и changedordersrestoall)

<aside class="notice">
Чтобы сделать заказ столика нужно знать ID заведения. По ID заведения в базе Resto узнать ID в базе ZON, можно через запрос `placeinforesto`:<br/>
<code>curl -d 'key=***&restoid=1021' http://z-o-n.ru/_api/action_placeinforesto/partner_**</code>
<br/>
<br/>

После чего:<br/>
<code>curl -d "key=***&placeid=9066&order[persons]=1&order[year]=2015&order[month]=08&order[day]=04&order[hour]=17&order[minutes]=45&order[phone]=%2B7%20(916)%20878-38-77&order[email]=hmizgir%40gmail.com&order[comment]=test&order[name]=test" http://z-o-n.ru/_api/action_order/partner_**</code>
<br/>
<br/>

Пример ответа:<br/>
<code>{"codeanswer":"TR","data":"30"}</code>
</aside>

Возвращает JSON следующего вида:

<code>{"codeanswer":"TR","data":"30"}</code>

Где `codeanswer` - общий итог работы скрипта, `data` - конкретизация итогов работы (зависит от codeanswer).

Ответы `codeanswer`:

* OK - "заказ успешно добавлен", возможные значения в `data`:
    * 10 - заказ успешно добавлен через zon
    * 20 - заказ успешно добавлен через call-центр
* TR - "проблема с входными данными", возможные значения в `data`:
    * 10 - имя пользователя не прошло цензуру
    * 20 - указан некорректный телефон
    * 30 - ресторан неактивен
    * 40 - Заказ на слишком раннее время
    * 50 - На это время ресторан не принимает заказы, обратите внимание на часы работы или время приёма заказов
    * 60 - Все столики ресторана закрыты на спецобслуживание
    * 70 - Извините, клиент с этими данными заблокирован
    * 80 - Вы уже забронировали столик на эту дату. Если вы хотите забронировать столик в этом ресторане, сначала отмените предыдущий заказ
    * 90 - Вы уже забронировали столик в ресторане на эту дату. Чтобы забронировать столик, отмените предыдущую бронь
    * 100 - Заказчик НЕ может забронировать более 3 столиков в одном ресторане на одно и то же время
    * 110 - Может быть забронирован только столик с депозитом. Требуется согласие клиента на это, в случае согласия передать дополнительно POST['confirmdep']=1
* BUSY - "столики заняты", возможные значения в `data`:
    * 10 - Есть столик в другом зале,
    * 20 - Есть менее вместительные столики. Попробуйте заказать несколько столиков,
    * 30 - Все маленькие столики заняты,
    * 40 - Зал закрыт от бронирования,
    * 50 - Нужный Вам столик есть на 30 минут позже,
    * 60 - Нужный Вам столик есть на 60 минут позже,
    * 70 - Нужный Вам столик есть на 90 минут позже,
    * 80 - Нужный Вам столик есть на 120 минут позже,
    * 10000 - Нет подходящих столиков с такими характеристиками
* ERR - "ошибка во время выполнения"


<h2 id="orderv">orderv</h2>

> Пример запроса:

```json
{
  "placeid": 6,
  "order": {
    "reserved_table_id": 1230,
    "hour": 11,
    "minutes": 45,
    "day": 19,
    "month": 07,
    "year": 2011,
    "name": "some name",
    "phone": "+7 (112) 121-21-21",
    "email": "email@mail.ru",
    "persons": 1,
    "partner_comment": "some comment",
    "comment": "some comment"
  }
}
```

Заказ определенного столика в ресторане.

Orderv аналогичен order, но в параметрах заказа передается ID столика (через параметр `reserved_table_id`)

<aside class="notice">
Выяснить ID столика можно с помощью метода <code>gettablelist</code>.
<br/>
<br/>
Пример gettablelist-запроса в ruby:<br/>
<code>
params = {'id' => 2, 'persons' => 2, 'smoke' => 1, 'key' => '***'}<br/>
url = 'http://z-o-n.ru/_api/action_gettablelist/partner_**'<br/>
tables_info = JSON.parse RestClient.post(url, params, :content_type => :json, :accept => :json).body<br/>
table_id = tables_info['tables'].last['table_id']
</code>
</aside>

<h2 id="changedorders">changedorders</h2>

> Пример запроса:

```json
{ "datetime": "2015-09-05 16:22:06", "id": 1230 }
```

> Возвращает структуру вида:

```json
{
  "200223": {
    "name": "тест",
    "orderdatetime": "2015-08-21 17:40:45",
    "userpartnerid": 11,
    "place": 2,
    "email": "test@test.ru",
    "statuses": [
      {
       "datetime": "2015-08-21 17:40:45",
       "status": 10
      },
      {
        "datetime": "2015-08-21 19:25:26",
        "status": 500
      }
    ]
  },
  "201753": {
    "name": "Марина",
    "orderdatetime": "2015-08-21 17:40:45",
    "userpartnerid": "test",
    "place": 11014,
    "email": "mixxxxto@gmail.ru",
    "statuses": [
      {
        "datetime": "2015-08-26 11:45:21",
        "status": 10
      },
      {
        "datetime": "2015-08-26 12:46:09",
        "status": 500
      }
    ]
  }
}
```

Все измененния, происходящие с бронью (статусы), начиная с времени последней синхронизации.

**Параметры запроса:**

* `datetime` - дата-время в формате "2015-09-05 16:22:06"
* `id` - ID заказа (необязательный параметр, если равен нулю - передается все)


**Возвращаемые значения:**

* name - имя гостя
* orderdatetime - время брони
* userpartnerid - ID гостя в базе пользователя API
* place - заведение, куда сделан заказ
* email - email гостя
* statuses - массив изменений статусов заказа (status - новое состояние,  datetime - время-дата установки статуса), коды статусов:
    * 5 - гость отправлен в колл-центр
    * 10 - новый заказ
    * 20 - гость пришел
    * 30 - гость отменил заказ
    * 40 - гость не пришел
    * 100 - заказ принят
    * 101 - столик забронирован
    * 300 - направлен в ресторан
    * 402 - нет мест
    * 500 - ошибочный заказ

<h2 id="changedordersrestoall">changedordersrestoall</h2>

Все измененные выполненные брони, начиная с времени предыдущей синхронизации.

Входные параметры и возвращаемые данные аналогичны `changedorders`.

