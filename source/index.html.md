---
title: Resto API

search: true

---

# Resto/ZON API

## Доступ к API

Аутентификация делается через http-base-auth, запросы к Resto API - GET. Домен - api.resto.ru.

## Выдача_

Формат - JSON.

### Ленивая выдача_

По умолчанию выдается только первая страница (даже если явно пейджинг не был явно запрошен) и из полей - только id'ы, остальные поля и страницы нужно запрашивать явно. Для указания полей используется опция `include`

Пример:

http://api.resto.ru/places.json?ids=7477,8&include=title,id,lat,lat

Если у заведения отсутствует какое-либо значение (например нет status'а - заведение не относится к "Временно закрытом", "Новым" и т.п.), то она просто не выводится.

### Пейджинация_

По умолчанию выдается только первая страница (даже если явно пейджинг не был явно запрошен).

Управлять пейджинацией можно через не обязательные параметры `per_page` (количество объектов на "странице", по умолчанию - 10) и `page` - номер страницы (по умолчанию - 1).

Пример:

http://api.resto.ru/places.json?per_page=3

http://api.resto.ru/places.json?region=spb&page=2

# Заведения (places)

Все параметры возможные при запросе places:

* особенности:
  * properties
  * title
  * address
  * phone
  * lat
  * long
  * cuisine (кухни)
  * price-range
  * subway-station
  * type (тип заведения: ресторан, кафе и т.п.)
  * zon_rating
  * order_possible
  * description
  * ring
  * status ("Закрытое", "Временно закрытое", "Новое" и т.п.)
* картинки (выдают url или массив url'ов):
  * has_photos
  * photos
  * smallbox (64x64px, выдается одна картинка, логика ее выбора - на стороне API)
  * middlebox (96x96px, выдается одна картинка, логика ее выбора - на стороне API)
  * logo (в приложении не используется)
  * panorama (в приложении не используется)
* фильтры:
  * with (получить список возможных значений для `with` и `with_any` можно через [словари](#vocabularies))
  * with_any 
  * starts_with
  * ids
  * region
* геопозиция (действует как фильтр):
  * lat
  * long
  * radius
* пейджинация:
  * page
  * per_page
* сортировка:
  * sort_by
* поиск по title:
  * search

Выборка заведений по id'ам:

http://api.resto.ru/places.json?ids=7477,4846

http://api.resto.ru/places.json?id=7477,4846

С координатами:

http://api.resto.ru/places.json?include=title,id,lat,lat&long=37.5&lat=55.7&page=2

В радиусе (указываются метры):

http://api.resto.ru/places.json?include=title,id&long=37.5&lat=55.7&radius=300000

С адресом:

http://api.resto.ru/places.json?include=title,address&long=37.5&lat=55.7&page=2

С картинками:

http://api.resto.ru/places.json?id=55&include=title,photos,logo,panorama

С thumbnail'ом smallbox:

http://api.resto.ru/places.json?ids=7477&include=title,smallbox

С указанной особенностью (станция метро в данном случае):

http://api.resto.ru/places.json?include=title,price-range,long,lat&with=dinamo

С рейтингом ZON'а:

http://api.resto.ru/places.json?ids=9786&include=title,zon_rating

Заведения, в которых можно забронировать столик через ZON:

http://api.resto.ru/places.json?ids=300392&include=title,zon_order_possible


Со статусом заведения:

http://api.resto.ru/places.json?with=new&include=title,status


Заведения указанного региона:

http://api.resto.ru/places.json?region=spb

С кольцом (Садовым, Бульварным и т.п.):

http://api.resto.ru/places.json?with=out-of-town&include=title,ring

Заведения измененные с указанной даты:

http://api.resto.ru/places.json?include=title,updated&after=2015-04-01T13:15:58+04:00


### Сортировка_

Если в запросе указана исходная точка, то сортировка делается по удаленности от этой точки. Во всех прочих случаях сортировка делается по `title`. Можно явно указать сортировку через параметр `sort_by`.
При сортировке по геоособенности (метро, точка и т.п.) выдается `distance` - расстояние до этой точки.

С сортировкой по геоособенности:

http://api.resto.ru/places.json?include=title&with=dinamo&sort_by=dinamo

## Поиск_

Для поиска по названию заведения можно использовать `search` или `starts_with`

Простой пример поиска:

http://api.resto.ru/places.json?include=title,id&starts_with=де

Поиск с фильтрами:

http://api.resto.ru/places.json?include=title,id&long=37.84&lat=55.63&radius=300&page=2&per_page=10&starts_with=де

# Словари (vocabularies)

Для построения навигации можно использовать иерархические конструкции (vocabularies), которые запрашиваются у API. Таким образом приложение получает набор опций, которые можно использовать при запросах (через `with`, `with_any`), и управление навигацией перекладывается на API. Т.е. необязательно обновлять приложение при добавлении новой опции и т.п.

Выводятся vocabularies в виде хэша.

http://api.resto.ru/vocabularies.json

## Локации (location)

Выборки по местоположению (aka геоособенность, т.е. особенность имеющая координату). Сейчас это только метро.

http://api.resto.ru/vocabularies/locations.json

Локации для региона:

http://api.resto.ru/vocabularies/locations.json?region=spb

## Особенности (properties)

Набор всевозможных параметров заведений.

http://api.resto.ru/vocabularies/properties.json

# Регионы (regions)

Список регионов:

http://api.resto.ru/regions.json

Регионы с русской локалью:

http://api.resto.ru/regions.json?locale=ru

Информация по указанному региону:

http://api.resto.ru/regions.json?ids=msk

http://api.resto.ru/places.json?region=spb&page=2
