[![Latest Stable Version](https://poser.pugx.org/arrilot/bitrix-migrations/v/stable.svg)](https://packagist.org/packages/arrilot/bitrix-migrations/)
[![Total Downloads](https://img.shields.io/packagist/dt/arrilot/bitrix-migrations.svg?style=flat)](https://packagist.org/packages/Arrilot/bitrix-migrations)
[![Build Status](https://img.shields.io/travis/arrilot/bitrix-migrations/master.svg?style=flat)](https://travis-ci.org/arrilot/bitrix-migrations)
[![Scrutinizer Quality Score](https://scrutinizer-ci.com/g/arrilot/bitrix-migrations/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/arrilot/bitrix-migrations/)


# Bitrix-migrations

*Миграции БД для Битрикса и не только*

## Установка

1) `composer require arrilot/bitrix-migrations`

2) `cp vendor/arrilot/bitrix-migrations/migrator migrator` - копируем исполняемый файл в удобное место.

3) `php migrator install`

Данная команда создаст в БД таблицу для хранения названий выполненных миграций.

По умолчанию:

1) Таблица называется migrations.

2) `composer.json` и `migrator` лежат в корне сайта.

3) Файлы миграций будут создаваться в директории `./migrations` относительно скопированного на этапе 2 файла.

При необходимости всё это можно изменить в скопированном файле `migrator`.

* Крайне рекомендуется сделать `migrator` и `./migrations` недоступными по http через веб-сервер.*

## Использование

### Рабочий процесс

Рабочий процесс происходит через консоль и кратко описывается примерно так:

1) Создаем файл (или файлы) миграции при помощи `php migrator make название_миграции`

Файл миграции представляет из себя класс с двумя методами `up()` и `down()`

2) Реализуем в методе `up()`необходимые изменения в БЗ. При желании в методе `down()` реализуем откат этих измнений

3) Применяем имеющиеся миграции - `php migrator migrate`

4) Вносим файлы миграций в систему контроля версий, чтобы их можно было запустить и на других машинах


### Доступные команды

Список доступных команд можно получить в консоли - `php migrator list`

1) `php migrator install` - создает таблицу для хранения миграций. Запускается один раз.

2) `php migrator migrate` - применяет все доступные для применения миграции. Миграции примененные ранее не применяются.

3) `php migrator rollback` - откатывает последнюю миграцию (метод `down()`). После этого её можно применить повторно.

4) `php migrator make название_миграции` - геренирует файл миграции

Так как изменение структуры БД битрикса через его АПИ  - занятие крайне малоприятное, то для облегчения этого процесса есть механизм шаблонов миграций, работающий следущим образом:
При генерации файла миграции можно указать его шаблон: `php migrator make название_миграции -t add_iblock` где `add_block` - название шаблона.
При этом сгенерируется класс с бойлерплейтом из шаблона и остается лишь указать детали (например название и код инфоблока)
Все шаблоны миграции регистрируются напрямую в файле `migrator`. Там же можно добавить свои, удалить имеющиеся и т д.

5) `php migrator templates` - показывает подробную таблицу со всем существующими шаблонами миграций

По умолчанию в настоящий момент имеются следующие шаблоны:

1) `default` - чистый шаблон по умолчанию

2) `add_iblock`

3) `add_iblock_element_property`

4) `add_uf` - добавление пользовательского свойства (допустим к пользователям или к разделу инфоблока)


### Отмена выполнения миграции

Для отмены миграции в момент её исполнения достаточно выкинуть исключение - ```php throw new MigrationException('Тут текст ошибки');```
Ни сама миграция, ни последующие при этом применены не будут.

### Использование вне Битрикс

Пакет создан для использования совместно с Битриксом, однако его довольно просто можно использовать и в других системах.
Для этого нужно в файле `migrator`:

1) Заменить подключение ядра Битрикса на ядро другой системы.

2) Реализовать свой аналог ```php Arrilot\BitrixMigrations\Repositories\BitrixDatabaseRepository;``` и использовать тут его.

3) По желанию отключить существующие шаблоны миграций, сделав свои.