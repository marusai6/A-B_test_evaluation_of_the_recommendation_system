# Оценка эффективности системы рекомендаций онлайн-магазина здорового питания

У магазина есть приложение, внутри которого реализована рекомендательная система: она подсказывает пользователю, какие товары ему может быть интересно добавить в корзину. Раньше система была реализована довольно прямолинейно: пользователю предлагалось добавить те товары, которые он часто покупал в прошлом. Эту информацию можно получить из истории покупок пользователя.

Однако коллеги из отдела разработки алгоритмов машинного обучения создали новую рекомендательную систему на основе нейронных сетей, которая с помощью данных о прошлых покупках пользователя и того, что покупают другие люди, предлагает ему релевантные товары. Важно, что система может рекомендовать не только то, что пользователь и так часто покупает, но и абсолютно новые продукты, которые он раньше никогда не пробовал.

Возникает вопрос: действительно ли новый алгоритм работает лучше, чем существующий?

Чтобы ответить на него, провели A/B-эксперимент, в ходе которого контрольная группа пользователей продолжала пользоваться старой версией системы рекомендаций, а экспериментальная группа использовала новый алгоритм. Период проведения эксперимента — с 01.10.2023 по 14.10.2023 включительно.

**Задача**

Проанализировать собранные в ходе эксперимента данные и определить, станет ли лучше от использования нового алгоритма рекомендаций.

## Данные о проведении A/B-эксперимента
Ниже описаны данные, которые доступны по итогу проведения A/B-эксперимента.
Таблицы лежат в папке files в репозитории.

**Таблица «user»**

В таблице содержится общая информация о пользователях онлайн-магазина.

Таблица содержит следующие колонки:

«id» — уникальный идентификатор пользователя.
«gender» — пол пользователя. Значение «М» соответствует мужскому полу, значение «Ж» — женскому.
«age» — возраст пользователя на момент 01.09.2023.
«region» — регион, в котором проживает пользователь.

Схема таблицы:

|Колонка|Тип данных|Пропуски|Первичный ключ|
|-------|---------|---------|--------------|
|id|Целое число|False|True
|gender|Строка|False|False|
|age|Целое число|False|False|
|region|Строка|False|False|

**Таблица «user_ab_group»**

В таблице содержится информация о том, в какую группу в рамках эксперимента входил тот или иной пользователь. Важно, что в эксперименте участвовали не все пользователи. Поэтому некоторых из них нет в данной таблице.

Таблица содержит следующие колонки:

«user_id» — идентификатор пользователя.
«group» — название группы, в которую входил пользователь в рамках эксперимента. Название «control» соответствует контрольной группе, а «treatment» — экспериментальной.

Схема таблицы:

|Колонка|Тип данных|Пропуски|Первичный ключ|
|-------|---------|---------|--------------|
|user_id|Целое число|False|True|
|group|Строка|False|False|

**Таблица «good»**

В таблице содержится общая информация о продуктах, которые можно купить в магазине.

Таблица содержит следующие колонки:

«id» — уникальный идентификатор товара.
«good_name» — название товара.
«price_per_unit» — цена за одну условную единицу товара.

Схема таблицы:

|Колонка|Тип данных|Пропуски|Первичный ключ|
|-------|---------|---------|--------------|
|id|Целое число|False|True|
|good_name|Строка|False|False|
|price_per_unit|Целое число|False|False|

**Таблица «user_purchase»**

В таблице содержится информация о покупках, которые совершали пользователи. Важно, что в таблице присутствуют данные и о том, как эти же самые пользователи совершали покупки вне периода проведения эксперимента.

Таблица содержит следующие колонки:

«id» — уникальный идентификатор покупки.
«user_id» — идентификатор пользователя, который совершил эту покупку.
«date_time» — момент времени, когда была совершена покупка. Формат представления момента времени: %Y-%m-%d %H:%M:%S.

Схема таблицы:

|Колонка|Тип данных|Пропуски|Первичный ключ|
|-------|---------|---------|--------------|
|id|Целое число|False|True|
|user_id|Целое число|False|False|
|date_time|Момент времени|False|False|

**Таблица «purchase_good»**

В таблице содержится информация о товарах, которые входили в список покупок, совершённых пользователями. Каждому товару из конкретной покупки соответствует отдельная строчка в таблице.

Таблица содержит следующие колонки:

«purchase_id» — идентификатор покупки.
«good_id» — идентификатор товара, который входит в эту покупку.
«amount» — количество единиц товара, входящих в покупку. Для простоты будем считать, что это всегда целочисленное значение.
«was_in_recommended_goods» — значение, которое показывает, был ли приобретённый продукт добавлен в корзину по рекомендации: True — если был, False — если нет.

Схема таблицы:

|Колонка|Тип данных|Пропуски|Первичный ключ|
|-------|---------|---------|--------------|
|purchase_id|Целое число|False|True|
|good_id|Целое число|False|True|
|amount|Целое число|False|False|
|was_in_recommended_goods|Логическое значение|False|False|
