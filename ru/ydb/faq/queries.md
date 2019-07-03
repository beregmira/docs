### Запросы, синтаксис, UDF

#### Как сделать запрос по логам за последний день/неделю/месяц или какой-либо другой относительный период времени?
Если подставлять даты в текст запроса перед запуском не удобно, то можно сделать следующим образом:

1. Пишем UDF на [Python](../udf/python.md), [JavaScript](../udf/javascript.md) или [C++](../udf/cpp.md) c сигнатурой `(String)->Bool`. По имени таблицы она должна вернуть true только для попадающих в нужный диапазон, ориентируясь на текущее время или дату, полученные средствами выбранного языка программирования.
2. Передаём эту UDF в функцию [FILTER](../syntax/extensions.md#filter) вместе с префиксом до директории с логами.
3. Используем результат работы FILTER в любом месте, где ожидается путь к входным данным ([FROM](../syntax/basic.md#from), [PROCESS](../syntax/extensions.md#process), [REDUCE](../syntax/extensions.md#reduce))

[Пример](https://yql.yandex-team.ru/Operations/WXcgQY0UzHDE91SFjG2rUqDJc118T4EeE-33gDGw1ZE=).

Так как форматы даты и времени в названиях таблиц могут быть произвольными, а также логика выбора подходящих таблиц может быть неопределённо сложной, более специализированного решения для этой задачи не предоставляется.

#### Как сделать фильтрацию по большому списку значений?
Можно, конечно, сгенерировать гигантское условие в `WHERE`, но лучше:

* Сгенерировать текстовый файл со списком значений по одному на строку, загрузить его куда-либо в доступное по http место (например, через [ya upload](https://wiki.yandex-team.ru/yatool/upload)) и воспользоваться [IN](../syntax/basic.md#in) в сочетании с функцией [ParseFile](../builtins/basic.md#parsefile).
* Сформировать таблицу из значений, по которым нужно сделать фильтрацию, и выполнить `LEFT SEMI JOIN` с этой таблицей.
* Для строк можно сгенерировать регулярное выражение, проверяющее их через «или» и воспользоваться ключевым словом [REGEXP](../syntax/basic.md#regexp), либо одной из предустановленных функций для работы с регулярными выражениями ([HyperScan](../udf/list/hyperscan.md), [Pire](../udf/list/pire.md) или [Re2](../udf/list/re2.md)).

#### Чем отличаются MIN, MIN\_BY и MIN\_OF? (или MAX, MAX\_BY и MAX\_OF)

* `MIN(column)` — агрегатная функция из SQL-стандарта, возвращающая минимальное значение, найденное в указанной колонке.
* `MIN_BY(result, by)` — нестандартная агрегатная функция, которая, в отличие от предыдущей, возвращает не само минимальное значение, а какое-то другое значение из строк(-и), где нашелся минимум.
* `MIN_OF(a, b, c, d)` — встроенная функция, которая возвращает среди N значений-аргументов минимальное.

#### Как обратиться к колонке, в имени которой есть знак минус или другой спецсимвол?
Обернуть имя колонки в квадратные скобки или backticks по аналогии с именами таблиц:
``` yql
SELECT [field-with-minus] FROM [table];
SELECT `field-with-minus` FROM `table`;
```

#### Что такое колонка \_other?
Это колонка, имеющая тип `Dict<String,String>`, в которой будут доступны столбцы таблицы, явно не заданные в нестрогой схеме (с атрибутом `<strict=%false>`).
Есть несколько распространенных способов получить такую таблицу. Например, если изначально таблица была создана без схемы ([в терминах YT](https://wiki.yandex-team.ru/yt/userdoc/staticschema/)), а потом отсортирована, система (YT) выставляет ей схему, в которой явно указаны только имена столбцов, по которым производилась сортировка.

Например, такая схема часто присутствует у сортированных таблиц, полученных через YaMR-обертку. В этом случае доступ к `value` можно получить следующим образом:

``` yql
SELECT WeakField(t.value,"String") FROM
[path/to/my_table] as t;
```
или

``` yql
SELECT t._other{"value"} FROM
[path/to/my_table] as t;
```
Первый способ позволяет работать единообразно с таблицей, если вдруг поле value попадет в строгую схему. Также WeakField позволяет сразу получить значение в нужном типе, а при работе с _other полученные значения пришлось бы обрабатывать как Yson. Для случая строгой схемы проверяется, что тип переданный в WeakField совпадает с типом в схеме.

[Подробнее](../misc/schema.md)