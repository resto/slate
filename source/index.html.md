---
title: Resto API

search: false

---

<h1 id="Resto-ZON-API">Resto API</h1>

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

Данные выдаются только по явному запросу. Из полей - только id'ы, остальные поля и страницы нужно запрашивать явно. По умолчанию выдается только первая страница (даже если явно пейджинация не была явно запрошена). Для указания полей используется опция `include`.

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
