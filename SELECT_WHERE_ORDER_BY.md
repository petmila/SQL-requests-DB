## Теория
### SELECT
1. Пример выборки всего содержимого из таблицы.

        SELECT *
        FROM [Production].[Product]
        
2. Использование псевдонимов.

        SELECT [Name]
        FROM [Production].[Product]
        ___

        SELECT p.[Name]
        FROM [Production].[Product] AS p        
3. Три столбца, одиному из которых присвоен псевдоним.
 
        SELECT [ProductID], [Name], [ListPrice]-[StandardCost] AS [discountsize]
        FROM [Production].[Product]
4. Константное текстовое значение в третьем столбце, в четвертом - результат выполнения функции.

        SELECT [ProductID], [Name], 'empty column', GETDATE()
        FROM [Production].[Product]
5. Выбор данных без повторений.

        SELECT DISTINCT [Size]
        FROM [Production].[Product]
6. Выбор всех возможных пар значений без повторений.

        SELECT DISTINCT [Color], [Size]
        FROM [Production].[Product]
### ORDER BY
