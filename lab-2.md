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

        SELECT TOP 3 [ProductSubcategoryID]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [ProductSubcategoryID] IS NOT NULL
        GROUP BY [ProductSubcategoryID]
        ORDER BY COUNT([Name]) DESC
2. Разбить продукты по количеству символов в названии, для каждой группы определить количество продуктов.

        SELECT LEN([Name]) AS NameLength, COUNT([Name]) AS ProductsAmount
        FROM [AdventureWorks2019].[Production].[Product]
        GROUP BY LEN([Name])
3. Проверить, есть ли продукты с одинаковым названием, если есть, то вывести эти названия.

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        GROUP BY [ProductID], [Name]
        HAVING COUNT([Name]) > 1
#### Самостоятельные решения:
1. Найти и вывести на экран количество товаров каждого цвета, исключив из поиска товары, цена которых меньше 30.

        SELECT [Color], COUNT([Color]) AS Amount
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [ListPrice] > 30 AND Color IS NOT NULL
        GROUP BY [Color]
2. Найти и вывести на экран список, состоящий из цветов товаров, таких, что минимальная цена товара данного цвета более 100.

        SELECT [Color], MIN([ListPrice]) as MinPrice
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE ListPrice > 130 AND Color IS NOT NULL
        GROUP BY Color
3. Найти и вывести на экран номера подкатегорий товаров и количество товаров в каждой категории.


4. Найти и вывести на экран номера товаров и количество фактов продаж данного
товара (используется таблица SalesORDERDetail).

5. Найти и вывести на экран номера товаров, которые были куплены более пяти
раз.

6. Найти и вывести на экран номера покупателей, CustomerID, у которых
существует более одного чека, SalesORDERID, с одинаковой датой

7. Найти и вывести на экран все номера чеков, на которые приходится более трех
продуктов.
8. Найти и вывести на экран все номера продуктов, которые были куплены более
трех раз.
9. Найти и вывести на экран все номера продуктов, которые были куплены или
три или пять раз.
10. Найти и вывести на экран все номера подкатегорий, в которым относится
более десяти товаров.
11. Найти и вывести на экран номера товаров, которые всегда покупались в
одном экземпляре за одну покупку.
12 Найти и вывести на экран номер чека, SalesORDERID, на который приходится
с наибольшим разнообразием товаров купленных на этот чек.
13 Найти и вывести на экран номер чека, SalesORDERID с наибольшей суммой
покупки, исходя из того, что цена товара – это UnitPrice, а количество
конкретного товара в чеке – это ORDERQty.
14 Определить количество товаров в каждой подкатегории, исключая товары,
для которых подкатегория не определена, и товары, у которых не определен цвет.
15 Получить список цветов товаров в порядке убывания количества товаров
данного цвета
16 Вывести на экран ProductID тех товаров, что всегда покупались в количестве
более 1 единицы на один чек, при этом таких покупок было более двух.
