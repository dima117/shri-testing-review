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

&nbsp;

<div class="next" markdown="1">
Проблемы:

- не все важные сценарии покрыты тестами
- интеграционные тесты вместо модульных
</div>

## Алгоритм

**Всё, что возможно проверить модульными тестами.<br/> Остальное — интеграционными.**

&nbsp;

  - {:.next}разбили приложение на смысловые блоки
  - {:.next}смотрим интерфейс, находим сценарии
  - {:.next}если логика сценария находится внутри блока, пишем модульный тест
  - {:.next}если важно взаимодействие блоков или в блоке мало логики, пишем интеграционный тест

## Модульные тесты:<br />что тестировать
{:.section}

## Сказка про колобка

- {:.next}побег от бабушки
- {:.next}встреча с зайцем
- {:.next}встреча с волком
- {:.next}встреча с медведем
- {:.next}встреча с лисой и смерть

## Сказка про колобка

- побег от бабушки
- встречи с неопасными животными
  - встреча_с_животным(заяц)
  - встреча_с_животным(волк)
  - встреча_с_животным(медведь)
- встреча с лисой
- встреча_с_животным
- {:.next} песенка

## Учебный проект

- контроллеры
- хелперы
- обертки над зависимостями

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
// git.js
function gitHistory(git = executeGit) {
    return git().then(/* ... */);
}

// git.test.js
it ('из вывода git парсятся поля hash, timestamp, author, msg', () => {
    const gitStub = () => Promise.resolve('...');

    const result = gitHistory(gitStub);
    // ...
});
```

## переписать на классы
{:.fullscreen}

```js
// git.js
class GitHelpers {
    constructor(gitStub) {
        this.git = gitStub || executeGit;
    }
    gitHistory() {
        return this.git().then(/* ... */);
    }
}
// git.test.js
it ('из вывода git парсятся поля hash, timestamp, author, msg', () => {
    const gitStub = () => Promise.resolve('...');
    // разные экземпляры в каждом тесте
    const result = new GitHelpers(gitStub).gitHistory();
    // ...
});
```

## контроллер express
{:.fullscreen}

```js
function(req, res, next) {
    getHistory()
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
// indexController.js
function createController(historyStub) {
    const getHistoryFn = historyStub || getHistory;

    return function(req, res, next) {
        return getHistoryFn().then(/* ... */);
    };
}
// indexController.test.js
it ('...', () => {
    const historyStub = () => Promise.resolve({ /* ... */});
    const indexController = createController(historyStub);

    indexController(...);
    // ...
});
```

## Код только для тестов - ок.<br /><br />Уродливый код - не ок.
{:.blockquote}

## Модульные тесты:<br />хорошие практики

## Top 3 нарушений

- {:.next}бесполезные проверки
  - например, проверяется формат, но не проверяются данные
- {:.next}файловая структура не соответствует структуре тестов
  - например, всё в одном файле
- {:.next}непонятные названия 
  - очень общие, например *"This repo - second commit"*
  - сформулированы в терминах реализации, например: *если передать 5, то вернет пустой массив*

## Нет описания в README 🙁
{:.shout}

## Интеграционные тесты
{:.section}



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

## Подошел бы любой другой вариант, если он обоснован
{:.shout}
