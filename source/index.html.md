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

> С рейтингом ZON'а:

```shell
http://api.resto.ru/places.json?ids=9786&include=title,zon_rating
```

> Заведения, в которых можно забронировать столик через ZON:

```shell
http://api.resto.ru/places.json?ids=300392&include=title,zon_order_possible
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
  * order_possible
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
    "comment": "some comment"
  }
}
```

Заказ любого столика в ресторане.

<aside class="notice">
Чтобы сделать заказ столика нужно знать ID заведения. По ID заведения в базе Resto узнать ID в базе ZON, можно через запрос `placeinforesto`:<br/>
<code>curl -sd 'key=***&restoid=1021' http://z-o-n.ru/_api/action_placeinforesto/partner_**</code>
<br/>
<br/>

После чего:<br/>
<code>curl -d "key=***&placeid=9066&order[persons]=1&order[year]=2012&order[month]=08&order[day]=04&order[hour]=17&order[minutes]=45&order[phone]=%2B7%20(916)%20878-38-77&order[email]=hmizgir%40gmail.com&order[comment]=test&order[name]=test" http://z-o-n.ru/_api/action_order/partner_**</code>
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
{ "datetime": "2013-09-05 16:22:06", "id": 1230 }
```

> Возвращает структуру вида:

```json
{
  "200223": {
    "name": "тест",
    "orderdatetime": "2013-08-21 17:40:45",
    "userpartnerid": 11,
    "place": 2,
    "email": "test@test.ru",
    "statuses": {
       "0": {
            "datetime": "2013-08-21 17:40:45",
            "status": 10
       },
       "1": {
         "datetime": "2013-08-21 19:25:26",
         "status": 500
       }
    }
  },
  "201753": {
    "name": "Марина",
    "orderdatetime": "2013-08-21 17:40:45",
    "userpartnerid": "test",
    "place": 11014,
    "email": "mixxxxto@gmail.ru",
    "statuses": {
      "0": {
        "datetime": "2013-08-26 11:45:21",
        "status": 10
      },
      "1": {
        "datetime": "2013-08-26 12:46:09",
        "status": 500
      }
    }
  }
}
```

Все измененные бронь, начиная с времени предыдущей синхронизаци (???)

**Параметры запроса:**

* `datetime` - дата-время в формате "2013-09-05 16:22:06"
* `id` - ID заказа (необязательный параметр, если равен нулю - передается все)


**Возвращаемые значения:**

* name - имя, указанное в заказе
* orderdatetime - время брони
* userpartnerid - ID заказчика у партнера
* place - заведение, куда сделан заказ
* email - почта, указанная в заказе
* datetime - время изменения статуса
* status - установленный статус


<h2 id="changedordersrestoall">changedordersrestoall</h2>

Все измененные выполненные брони, начиная с времени предыдущей синхронизации.

Входные параметры и возвращаемые данные аналогичны `changedorders`.

<h2 id="search">search</h2>

> Пример запроса:

```json
{ "name": "рно" }
```

> Пример ответа:

```json
{
  "total": 4,
  "arr": {
    "0": {
      "place_id": 8,
      "place_name": "Иль Форно / Il Forno"
    },
    "1": {
      "place_id": 10056,
      "place_name": "Ливорно"
    },
    "2": {
      "place_id": 12203,
      "place_name": "Примерное поведение"
    },
    "3": {
      "place_id": 10761,
      "place_name": "Черноморская Ривьера"
    }
  }
}
```

Поиск ресторана. В параметре `name` передается искомая подстрока.

<h2 id="gettablelist">gettablelist</h2>

> Пример запроса

```json
{
  "id": 8,
  "persons": 2,
  "smoke": 0,
  "year": 2012,
  "month": 06,
  "day": 22,
  "hour": 15,
  "minutes": 55
}
```

> Пример ответа:

```json
{
  "total": 2,
  "tables": {
    "0": {
      "table_id": 86,
      "url": "https://z-o-n.ru/i/tables/table86.png",
      "140x102url": "https://z-o-n.ru/i/tables/140x102/table86.png",
      "229x238url": "https://z-o-n.ru/i/tables/229x238/table86.png"
    },
    "1": {
      "table_id": 89,
      "url": "https://z-o-n.ru/i/tables/table89.png",
      "url": "https://z-o-n.ru/i/tables/table86.png",
      "140x102url": "https://z-o-n.ru/i/tables/140x102/table89.png",
      "229x238url": "https://z-o-n.ru/i/tables/229x238/table89.png"
    }
  }
}
```

> Пример curl-вызова:

```shell
curl -d 'key=**&id=12081&&year=2012&month=08&day=04&hour=15&minutes=55&persons=1' http://z-o-n.ru/_api/action_gettablelist/partner_**<code>
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

<h2 id="placeinforesto">placeinforesto</h2>

> Пример запроса:

```json
{"restoid": 7569}
```

> Пример ответа:

```json
{
  "place_id": 6,
  "place_graph": 0,
  "zals": {
      "0": {
         "zal_id": 10,
         "zal_pict": "http://z-o-n.ru/i/zals/zal10.png",
         "zal_name": "Зал №1",
         "zal_smoke": 1,
         "zal_kol_seat": 42
      },
      "1": {
         "zal_id": 11,
         "zal_name": "Зал №2",
         "zal_smoke": 1,
         "zal_kol_seat": 56
      },
      "2": {
         "zal_id": 12,
         "zal_name": "Нижний зал",
         "zal_smoke": 0,
         "zal_kol_seat": 50
      }
    }
}
```

Информация о ресторане по ID ресторана в базер Resto.

**Возвращаемые значения:**

* place_id - ID по версии ЗОН
* place_graph -  наличие визуальной формы заказа
* zals - массив из залов:
  * zal_name - название зала
  * zal_smoke - можно ли курить в зале
  * zal_kol_seat - число посадочных мест в зале

<h2 id="findemailbyphone">findemailbyphone</h2>

		findemailbyphone: в массиве POST должен содержаться номер телефона клиента
           POST => Array
        	(
				[phonenumb] => +7 (000) 545-00-45 (ровно 18 символов)
			)
        (возвращает строку с емейлом клиента, если есть;
		или пустую строку в обратом случае)

<h2 id="worktime">worktime</h2>


		worktime: в массиве POST должен содержаться Id ресторана по версии РЕСТО
           POST => Array
        	(
				[restoid] => 1
			)
        (возвращает время приема заказов заведением)

<h2 id="gettablecoord">gettablecoord</h2>


		gettablecoord: отдаёт картинку зала и координаты столика на ней
			принимает: ID столика (table_id ). Пример:
           POST => Array
        	(
				[table_id] => 86
			)
        возвращаемая структура, пример
			Array
			(
				[zalimageurl] => https://z-o-n.ru/i/zals/zal13.png
				[zalimagesmallurl] => https://z-o-n.ru/i/zals/297x242/zal13.png
				[table_coords] => 330,233,370,262
			)
			здесь table_coords - 4 числа разделенные запятыми, соответственно координаты левого верхнего угла прямоугольника столика и координаты правого нижнего угла
				(left1, top1,  left2, top2)
								

<h2 id="snear">snear</h2>

		snear: в массиве POST должны содержаться координаты места
		POST => Array
        	(
            	[latitude] => 55.45 (широта)
            	[longitude] => 37.36 (долгота)
        	)
        (возвращает массив со структурой идентичной структуре для случая action=search, см. выше
			Всегда 10 ресторанов. Упорядочены по удаленности - сначала ближайшие)

