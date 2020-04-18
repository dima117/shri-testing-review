---

layout: yandex2

style: |
    /* собственные стили можно писать здесь!! */


---

# ![](themes/yandex2/images/logo-{{ site.presentation.lang }}.svg){:.logo}

## {{ site.presentation.title }}
{:.title}

### ![](themes/yandex2/images/title-logo-{{ site.presentation.lang }}.svg){{ site.presentation.service }}

{% if site.presentation.nda %}
![](themes/yandex2/images/title-nda.svg)
{:.nda}
{% endif %}

<div class="authors">
{% if site.author %}
<p>{{ site.author.name }}{% if site.author.position %}, {{ site.author.position }}{% endif %}</p>
{% endif %}

{% if site.author2 %}
<p>{{ site.author2.name }}{% if site.author2.position %}, {{ site.author2.position }}{% endif %}</p>
{% endif %}

</div>

## Содержание тестов важнее инструментов
{:.shout}

## Это сложно!

- {:.next}не заинтересованы
- {:.next}приложение не готово

## Как проверяли задание?

Добавляли в код ошибки, запускали тесты, смотрели, сколько ошибок они нашли.

- {:.next}не происходят ожидаемые действия в интерфейсе
- {:.next}неправильные данные в интерфейсе
- {:.next}не изменяется состояние
- {:.next}ошибки на сервере
- {:.next}сломалась вёрстка
  - {:.next}отдельно смотрели на мобильных
  
## Проблемы  
  
- не все важные сценарии покрыты тестами
- проверяется не тот результат<sup>*</sup>
- проверяется только ошибочный сценарий

## Тесты скриншотами

- просто писать
- {:.next}очень общие
- {:.next}хрупкие

&nbsp;

<div class="next" markdown="1">
Как уменьшить хрупкость:

- делать скриншоты отдельных блоков
- закрашивать часть элементов
</div>

## Все написали интеграционные тесты
{:.shout}

## Проблемы интеграционных тестов
{:.section}

## Первая причина:<br/>изменения верстки

- {:.next}вынести селекторы в константы
- {:.next}паттерн page object<sup>*</sup>
- {:.next}селекторы могут быть хрупкими
- {:.next}использовать тестовые css классы<sup>*</sup>

## Вторая причина:<br/>нестабильная инфраструктура

- отказывает selenium грид
- отказывает тестовй стенд

**Помогут ретраи, улучшение инфрраструктуры, изоляция зависимостей**

## Третья причина:<br/>сложные ситуации

 - {:.next}отправка данных меняет общее состояние
 - {:.next}сложно застабать БД и сторонний бэкенд
 - {:.next}недоступность элементов: виртуальный скролл, перекрытие другими элементами, disabled 
 - {:.next}асинхронные действия — нужно ждать элементы
 - {:.next}ошибки в тестах, например, забыли await

## Алгоритм

**Всё, что возможно проверить модульными тестами.<br/> Остальное — интеграционными.**

&nbsp;

  - {:.next}разбили приложение на смысловые блоки
  - {:.next}смотрим интерфейс, находим сценарии
  - {:.next}если логика сценария находится внутри блока, пишем модульный тест
  - {:.next}если важно взаимодействие блоков или в блоке мало логики, пишем интеграционный тест

## Модульные тесты:<br />внешние зависимости
{:.section}

## Проблемы

1. зависимости не изолированы
2. общие экземпляры заглушки

## Схема прототипы и экземпляры
{:.fullscreen}

![](pictures/instances.png)

## Рефакторинг!
{:.shout}

## опциональные параметры
{:.fullscreen}

```js
// api.js                                                опциональные параметры
function getData(send = fetsh) {
    return send('/api/handler').then(/* ... */);
}

// api.test.js
it ('из ответа сервера парсятся поля hash, timestamp, author, msg', () => {
    const fetchStub = () => Promise.resolve('...');

    const result = getData(fetchStub);
    // ...
});
```

## переписать на классы
{:.fullscreen}

```js
// api.js                                                  переписать на классы 
class ApiClient {
    constructor(fetchStub) {
        this.fetch = fetchStub || fetch;
    }
    getData() {
        return this.fetch('/api/handler').then(/* ... */);
    }
}
// git.test.js
it ('из вывода git парсятся поля hash, timestamp, author, msg', () => {
    const fetchStub = () => Promise.resolve('...');
    // разные экземпляры в каждом тесте
    const result = new ApiClient(fetchStub).getData();
    // ...
});
```

## контроллер express
{:.fullscreen}

```js
// контроллер express
function(req, res, next) {
    getData()
        .then(data => {
            res.render({ 
                data,
                // ... 
            });
        },
        err => next(err));
}
```

## фабрика
{:.fullscreen}

```js
// indexController.js                                                   фабрика
function createController(getDataStub) {
    const getDatafn = getDataStub || getData;

    return function(req, res, next) {
        return getDatafn().then(/* ... */);
    };
}
// indexController.test.js
it ('...', () => {
    const getDataStub = () => Promise.resolve({ /* ... */});
    const indexController = createController(getDataStub);

    indexController(...);
    // ...
});
```

## Код, нужный<br />только в тестах - ОК.<br /><br />Уродливый код - НЕ ОК!
{:.blockquote}

## Модульные тесты:<br />хорошие практики
{:.section}

## Top 3 нарушений

- {:.next}нечеткая структура теста
  - в одной куче подготовка данных, проверяемые действия и проверки
- {:.next}непонятные названия 
  - очень общие, например *"This repo - second commit"*
  - сформулированы в терминах реализации, например: *если передать 5, то вернет пустой массив*
- {:.next}файловая структура не соответствует структуре тестов
  - например, всё в одном файле

## Нет описания 🙁
{:.shout}

## Итоги

- {:.next}задали себе правильные вопросы
- {:.next}попробовали инструменты

## Все молодцы! 🙂
{:.shout}

## Контакты 
{:.contacts}

{% if site.author %}

<figure markdown="1">

### {{ site.author.name }}

{% if site.author.position %}
{{ site.author.position }}
{% endif %}

</figure>

{% endif %}

{% if site.author2 %}

<figure markdown="1">

### {{ site.author2.name }}

{% if site.author2.position %}
{{ site.author2.position }}
{% endif %}

</figure>

{% endif %}

<!-- разделитель контактов -->
-------

<!-- left -->
- {:.mail}dima117a@yandex-team.ru
- {:.github}dima117

<!-- right -->
- {:.skype}dima117a
- {:.telegram}dima117a

<!-- 

- {:.mail}author@yandex-team.ru
- {:.phone}+7-999-888-7766
- {:.github}author
- {:.bitbucket}author
- {:.twitter}@author
- {:.telegram}author
- {:.skype}author
- {:.instagram}author
- {:.facebook}author
- {:.vk}@author
- {:.ok}@author

-->

