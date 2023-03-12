### Агрегирующие функции:
- SUM ( [ ALL | DISTINCT ] expressiON ) – сумма значений в столбце

- MAX ( [ ALL | DISTINCT ] expressiON ) – максимальное значение в столбце

Максимальная цена продукта красного цвета:

        SELECT MAX([ListPrice]) FROM [Production].[Product]
        WHERE [Color]='Red'
- MIN ( [ ALL | DISTINCT ] expressiON ) – минимальное значение в столбце

- AVG ( [ ALL | DISTINCT ] expressiON ) – среднее значение в столбце

- COUNT ( { [ [ ALL | DISTINCT ] expressiON ] | * } ) – количество строк в столбце,
с учетом NULL значений

Количество строк, для которых определен цвет:

        SELECT COUNT([Color]) FROM [Production].[Product]
        WHERE [Color] IS NOT NULL
Количество строк с различным цветом:

        SELECT COUNT(DISTINCT [Color]) FROM [Production].[Product]
        WHERE [Color] IS NOT NULL

#### GROUP BY - разделение результата на группы и выполнение операций над каждой из них
Количество продуктов каждого цвета:

        SELECT [Color],  COUNT([Color]) AS 'Amount'
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Color] IS NOT NULL
        GROUP BY [Color]
Количество продуктов, у которых определены и цвет, и стиль по соответствующим группам.
        
        SELECT [Color], [Style], COUNT([Name]) AS 'Amount'
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Color] IS NOT NULL AND [Style] IS NOT NULL
        GROUP BY [Color], [Style]
        
- HAVING - условие поиска группы

Формируются группы только такие, где больше 10 членов и максимальная цена больше 3000:

        SELECT [Color], COUNT([Color]) AS 'Amount'
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Color] IS NOT NULL
        GROUP BY [Color]
        HAVING COUNT([Color])>10 AND MAX([ListPrice])>3000

- GROUP BY ROLLUP ()

Пример сравнения с GROUP BY - разница в том, что результат будет содержать дополнительный набор с группировкой по цвету и стилю, дополнительный
набор с группировкой только по цвету и строку без группировки:

        SELECT [Color], [Style], [Class], COUNT(*)
        FROM [Production].[Product]
        GROUP BY [Color], [Style], [Class]
        _________________________________________
        
        SELECT [Color], [Style], [Class], COUNT(*)
        FROM [Production].[Product]
        GROUP BY ROLLUP ([Color], [Style], [Class])
- GROUP BY CUBE () - добавляет все возможные сочетания параметров группировки (с учетом Null каждого столбца)
- GROUP BY GROUPING SETS ()

Данный запрос вернет данные, сгруппированные по цвету, и еще набор строк,
где данные сгруппированы по размеру:

        SELECT COUNT(*)
        FROM [Production].[Product]
        GROUP BY GROUPING SETS (([Color]),([Size]))

#### Примеры решений задач:
1. Найти номера первых трех подкатегорий (ProductSubcategoryID) с наибольшим количеством наименований товаров


2. 
