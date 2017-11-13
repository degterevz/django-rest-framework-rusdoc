# Requests

>Используя REST framework воздержитесь от request.POST.

*— Мальком Трединник, Django developers group*


Классы `Request` в REST framework расширяют стандартные `HttpRequest`, добавляя поддержку гибкого парсинга запросов и аутентификацию запросов для REST framework.

# Парсинг запросов

Объекты Request в REST framework обеспечивают гибкий парсинг запросов, который позволяет обрабатываеть запросы с данными JSON или другим типом данных таким же образом, как это обычно происходит при обработке данных форм.

## .data
`request.data` возращает спарсенный контент тела запросов. Он похож на атрибуты `request.POST` и `request.FILES` за исключением того, что

* Он включает весь спарсенный контет, включая все виды данных (файлы и не файлы).
* Он поддерживает парсинг контента с помощью HTTP методов вместо метода POST. Это значит, что доступ к контенту можно получить используя методы PUT и PATCH.
* Помимо возможности обработки данных форм, он поддерживает гибкий парсинг запросов из REST framework. К примеру вы можете принимать данные JSON тем же образом, как принимаете данные форм.

Для дополнительной информации см документацию по парсерам.

## .query_params
`request.query_params` это более точный синоним для `request.GET`.

Для прозрачности кода мы рекомендуем использовать `request.query_params` вместо стандартного `request.GET`. Это положительно скажется на читаемости кода - любой HTTP метод может включать в себя парамерты запроса, а не только лишь `GET` запросы.

## .parsers
Класс `APIView` или декоратор `@api_view` отвечают за то, что это свойство автоматическии включается в список экземпляров `Parser`, основанный на наборе классов представления `parser_classes` или на настройке `DEFAULT_PARSER_CLASSES`.
Как правило, нет нужды получать доступ к этому свойству.

**Заметка**: Если клиент передал информацию не по правилам формы, то при обращении к `request.data` может возникнуть ошибка `ParseError`. По умолчанию класс `APIView` или декоратор `@api_view` вернут сообщение `400 Bad Request`.
Если клиент пришлет запрос с типом контента, который невозможно спарсить, это вызовет исключение `UnsupportedMediaType`, которое по умолчанию возвращает сообщение `415 Unsupported Media Type`.

# Согласование содержимого
Запрос предоставляет некоторые свойства, которые позволяют вам определить результат согласования контента. Это позволяет реализовать выбирать различные схемы сериализации для разных типов данных.


## .accepted_renderer

Экземпляр рендера, который был выбран на этапе согласования контента.


## .accepted_media_type

Строка, представляющая тип медиафайла, который был принят на этапе согласования контента.


# Аутентификация

REST framework предоставляет гибкую аутентификацию перед совершением запроса, что дает следующие возможности:

* Использование разных типов проверок аутентификации для различных частей вашего API.
* Поддержка использования нескольких типов проверки аутентификации.
* Поддержка использования нескольких типов проверки аутентификации.
* Предоставлять информацию пользователя и токена, связанную с входящим запросом.

## .user
`request.user` как правило возвращает экземпляр `django.contrib.auth.models.User`, хотя поведение зависит от используемого типа аутентификации.
Если запрос не валидный, то значение `request.user` по умолчанию будет принимать экземпляр `django.contrib.auth.models.AnonymousUser`.

## .auth
`request.auth` возвращает любой дополнительный контекст аутентификации. Точное поведение `request.auth` зависит от типа аутентификации, но оно, как правило, зависит от экземпляра токена, который был задействован при аутентификации запроса.
Если запрос не валиден, или если отсутствует дополнтельный контекст, то значение `request.auth`по умолчанию становится `None`.

Для дополнительной информации см документацию по аутентификации.

## .authenticators

Класс `APIView` или декоратор `@api_view` отвечают за то, что это свойство автоматическии включается в список экземпляров `Authentication`, основанных на наборе классов представления `authentication_classes` или на настройке `DEFAULT_AUTHENTICATORS`.

Как правило нет нужды получать доступ к этому свойству.

# Расширение возможностей браузера
REST framework поддерживает некоторые инструменты для расширения возможномтей браузера, такие как браузерные формы `PUT`, `PATCH` и `DELETE`.

## .method

`request.method` возвращает капитализированное строчное представление метода HTTP запроса.
Поддерживаются браузерные формы `PUT`, `PATCH` и `DELETE`.

Для дополнительной информации см документацию по расширенным возможностям.


##.content_type
`request.content_type` возвращает строковый объект, который представляет тип информации тела HTTP запроса, или пустую строку, если не указан тип информации.

Как правило нет нужды напрямую запрашивать тип запроса, так как в этом можно положиться на стандартный механизм парсинга REST framework.

Если вы хотите получить доступ к типу данных запроса, то вам следует использовать свойство `.content_type` отдавая предпочтение `request.META.get('HTTP_CONTENT_TYPE')`, так как это предоставляет прозрачную поддержку браузерного контента, не связанного с формами.

## .stream
`request.stream` возвращает поток, который представляет содежание запрашиваемого тела запросов.

Как правило нет нужды напрямую запрашивать тип запроса, так как в этом можно положиться на стандартный механизм парсинга REST framework.


# Standard HttpRequest attributes
Несмотря на то, что `Request` в REST framework расширяет возможности стандартного `HttpRequest` Django, все остальные стандартные методы и атрибуты также доступны. Например словари `request.META` и `request.session` доступны в стандартном виде.
Обратите внимание, что в силу особенностей реализации, класс `Request` не наследуется от класса `HttpRequest`, но вместо этого расширяет класс, используя композицию.