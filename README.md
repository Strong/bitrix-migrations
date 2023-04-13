[![Latest Stable Version](https://poser.pugx.org/arrilot/bitrix-migrations/v/stable.svg)](https://packagist.org/packages/arrilot/bitrix-migrations/)
[![Total Downloads](https://img.shields.io/packagist/dt/arrilot/bitrix-migrations.svg?style=flat)](https://packagist.org/packages/Arrilot/bitrix-migrations)
[![Build Status](https://img.shields.io/travis/arrilot/bitrix-migrations/master.svg?style=flat)](https://travis-ci.org/arrilot/bitrix-migrations)
[![Scrutinizer Quality Score](https://scrutinizer-ci.com/g/arrilot/bitrix-migrations/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/arrilot/bitrix-migrations/)

# Данный пакет больше активно не поддерживается

Причина - мы больше не используем Битрикс в своих проектах.
Если вам интересен этот проект и вы хотите заняться его поддержкой - форкните его и создайте Issue в данном репозитории чтобы мы поместили здесь ссылку на форк.

# Bitrix-migrations

*Миграции БД для Битрикса и не только*

## Установка

1) `composer require strong/bitrix-migrations`

2) `cp vendor/strong/bitrix-migrations/migrator migrator` - копируем исполняемый файл в удобное место.

3) заходим внутрь и удостоверяемся что задается правильный $_SERVER['DOCUMENT_ROOT']. Меняем настройки если нужно

4) `php migrator install`

Данная команда создаст в БД таблицу для хранения названий выполненных миграций.

По умолчанию:

1) Таблица называется migrations.

2) `composer.json` и `migrator` лежат в корне сайта.

3) Файлы миграций будут создаваться в директории `./migrations` относительно скопированного на этапе 2 файла.

При необходимости всё это можно изменить в скопированном файле `migrator`.

* Крайне рекомендуется сделать `migrator` и `./migrations` недоступными по http через веб-сервер. *

## Использование

### Рабочий процесс

Рабочий процесс происходит через консоль и кратко описывается примерно так:

1) Создаем файл (или файлы) миграции при помощи `php migrator make название_миграции`

Файл миграции представляет из себя класс с двумя методами `up()` и `down()`

2) Реализуем в методе `up()`необходимые изменения в БД. При желании в методе `down()` реализуем откат этих измнений

3) Применяем имеющиеся миграции - `php migrator migrate`

4) Вносим файлы миграций в систему контроля версий, чтобы их можно было запустить и на других машинах


### Доступные команды

Список доступных команд можно получить в консоли - `php migrator list`

<table>
<tr><th>Название</th><th>Описание</th></tr>
<tr>
    <td>`php migrator install`</td>
    <td>Создает таблицу для хранения миграций. Запускается один раз.</td>
</tr>
<tr>
    <td>`php migrator make название_миграции`</td>
    <td>
        Создает файл миграции
        Опции:<br>
        `-d foo/bar` - указать поддиректорию, в которой будет создана миграция<br>
    </td>
</tr>
<tr>
    <td>`php migrator migrate`</td>
    <td>Применяет все доступные для применения миграции. Миграции примененные ранее не применяются.</td>
</tr>
<tr>
    <td>`php migrator rollback`</td>
    <td>
        Откатывает последнюю миграцию (метод `down()`). После этого её можно применить повторно.<br>
        Опции:<br>
        `--hard` - выполнить жесткий откат без вызова метода `down()`<br>
        `--delete` - удалить файл с миграцией после отката.<br>
    </td>
</tr>
<tr>
    <td>`php migrator templates`</td>
    <td>Показывает подробную таблицу со всем существующими шаблонами миграций</td>
</tr>
<tr>
    <td>`php migrator status`</td>
    <td>Показывает доступные для выполнения миграции, а также последние выполненные.</td>
</tr>
<tr>
    <td>`php migrator archive`</td>
    <td>
        Переносит все миграции в архив. По умолчанию это директория archive, но можно переопределить в конфиге, указав "dir_archive"<br>
        Опции:<br>
        `-w 10` - не переносить в архив последние N миграций<br>
    </td>
</tr>
</table>

### Шаблоны миграций

Так как изменение структуры БД битрикса через его АПИ  - занятие крайне малоприятное, то для облегчения этого процесса есть механизм шаблонов миграций, работающий следущим образом:
При генерации файла миграции можно указать его шаблон: `php migrator make название_миграции -t add_iblock` где `add_block` - название шаблона.
При этом сгенерируется класс с бойлерплейтом из шаблона и остается лишь указать детали (например название и код инфоблока)
Свои шаблоны миграций можно добавить напрямую в файле `migrator` при помощи `TemplateCollection::registerTemplate()`

Имеющиеся шаблоны:

<table>
<tr><th>Название</th><th>Описание</th><th>Алиасы</th></tr>
<tr>
    <td>`default`</td>
    <td>Чистый шаблон по умолчанию</td>
    <td></td>
</tr>
<tr>
    <td>`add_iblock_type`</td>
    <td>Добавление типа инфоблока</td>
    <td></td>
</tr>
<tr>
    <td>`add_iblock`</td>
    <td>Добавление инфоблока</td>
    <td></td>
</tr>
<tr>
    <td>`add_iblock_element_property`</td>
    <td>Добавление свойства в инфоблок</td>
    <td>`add_iblock_prop`, `add_iblock_element_prop`, `add_element_prop`, `add_element_property`</td>
</tr>
<tr>
    <td>`add_uf`</td>
    <td>Добавление UF свойства</td>
    <td></td>
</tr>
<tr>
    <td>`query`</td>
    <td>Произвольный запрос в БД через АПИ d7</td>
    <td></td>
</tr>
<tr>
    <td>`add_table`</td>
    <td>Создание таблицы через АПИ d7</td>
    <td>`create_table`</td>
</tr>
<tr>
    <td>`delete_table`</td>
    <td>Удаление таблицы через АПИ d7</td>
    <td>`drop_table`</td>
</tr>
</table>

6) `php migrator status` - показывает доступные для выполнения миграции, а также последние выполненные.


### Автоматическое создание миграций

Еще одна киллер-фича - режим автоматического создания миграций.
Для его включения необходимо добавить примерно следующее в `init.php`

```php
Arrilot\BitrixMigrations\Autocreate\Manager::init($_SERVER["DOCUMENT_ROOT"].'/migrations');
```

В метод `Manager::init()` передается путь до директории аналогичной конфигу в файле `migrator`.

После этого при выполнении ряда действий в админке будет происходить следующее

1) Срабатывает битриксовый обработчик события

2) Создается файл миграции как при `php migrator make`

3) Миграция помечается примененной

4) Показывается нотификация о предыдущих пунктах

Включение данного режима позволяет избавиться от ручного написания миграций для многих случаев.
Ничего в создаваемых автоматически миграциях править не требуется.

Список обрабатываемых событий:

<table>
<tr><th>Событие</th><th>Комментарии</th></tr>
<tr>
    <td>Добавление инфоблока</td>
    <td></td>
</tr>
<tr>
    <td>Обновление базовых полей инфоблока</td>
    <td>Из-за специфики работы админки битрикса эта миграция зачастую создается когда не нужно, допустим при добавлении кастомного свойства в инфоблок. Ничего смертельного, но надо смириться.</td>
</tr>
<tr>
    <td>Удаление инфоблока</td>
    <td></td>
</tr>
<tr>
    <td>Добавление кастомного свойства в инфоблок</td>
    <td></td>
</tr>
<tr>
    <td>Обновление кастомного свойства инфоблока</td>
    <td>Миграция создается только если какой-либо из атрибутов свойства был изменён</td>
</tr>
<tr>
    <td>Удаление кастомного свойства инфоблока</td>
    <td></td>
</tr>
<tr>
    <td>Добавление UF свойства куда-либо (раздел ИБ, пользователь, хайлоадблок)</td>
    <td>К сожалению Битрикс не даёт возможности отслеживать изменение такого свойства - только добавление и удаление</td>
</tr>
<tr>
    <td>Удаление UF свойства</td>
    <td></td>
</tr>
<tr>
    <td>Добавление хайлоадблока</td>
    <td></td>
</tr>
<tr>
    <td>Изменение хайлоадблока</td>
    <td>Миграция создается только если какой-либо из атрибутов хайлоадблока был изменён</td>
</tr>
<tr>
    <td>Удаление хайлоадблока</td>
    <td></td>
</tr>
<tr>
    <td>Добавление группы пользователей</td>
    <td></td>
</tr>
<tr>
    <td>Изменение группы пользователей</td>
    <td></td>
</tr>
<tr>
    <td>Удаление группы пользователей</td>
    <td></td>
</tr>
</table>

* Миграции используют события `OnBefore...`. Если при вашем изменении произошла ошибка (допустим не указана привязка к сайту при добавлении инфоблока) 
и было показано уведомление о том что миграция создана, необходимо вручную откатить такую миграцию при помощи `php migrator rollback --hard --delete` *

### Обработка ошибок миграций

Для отмены миграции в момент её выполнения достаточно выкинуть исключение - ```php throw new MigrationException('Тут текст ошибки');```
Ни сама миграция, ни последующие при этом применены не будут.

## Использование вне Битрикс

Пакет создан для использования совместно с Битриксом, однако его довольно просто можно использовать и в других системах.
Для этого нужно в файле `migrator`:

1) Заменить подключение ядра Битрикса на ядро другой системы.

2) Реализовать свой аналог ` Arrilot\BitrixMigrations\Repositories\BitrixDatabaseRepository;` и использовать его.

3) По желанию отключить существующие шаблоны миграций, сделав свои.

