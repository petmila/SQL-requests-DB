### Примеры запросов к базе данных:

1. Получить все названия товаров из таблицы Product.
    
        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
    
2. Получить все названия товаров в системе, цена которых (listprice) выше 200.

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [ListPrice] > 200
    
3. Получить все названия товаров в системе цена которых (listprice) выше 200 и у которых первая буква в названии “S”

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [ListPrice] > 200 AND [Name] LIKE 'S%'
    
4. Получить все названия товаров в системе, цена которых (listprice) выше 200 и у которых в названии есть сочетание символов “are”.

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [ListPrice] > 200 AND [Name] LIKE '%are%'
    
5. Получить все названия товаров в системе, в названии которых третий символ – либо буква “s”, либо буква “r”. Решить задачу как минимум двумя способами.

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Name] LIKE '__s%' OR [Name] LIKE '__r%'
        
        WHERE [Name] LIKE '__[sr]%'
        
6. Получить все названия товаров в системе, в названии которых ровно 5 символов.

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Name] LIKE '_____'
        
        WHERE len([Name])=5
        
7. Найти, без повторений, номера товаров, которые были куплены хотя бы один раз.
        
        SELECT DISTINCT [ProductID]
        FROM [AdventureWorks2019].[Sales].[SalesOrderDetail]

8. Найти список всех возможных стилей (style) продукта, без повторений

        SELECT DISTINCT [Style]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [Style] IS NOT NULL

9. Написать запрос, который возвращает названия товаров, которые были произведены между мартом 2011 года и мартом 2012 года включительно
(необходимо учитывать формат даты)

        SELECT [Name]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [SellStartDate] BETWEEN '2011-01-03' AND '2012-31-03'
        
10. Найти максимальную стоимость товара (отпускная цена ListPrice) из тех, которые были произведены, начиная с марта 2011 года.

        SELECT max([ListPrice]) AS [MaxPrice]
        FROM [AdventureWorks2019].[Production].[Product]
        WHERE [SellStartDate] >= '2011-01-03'
11. Вывести название и цвет продукта, отсортированные по названию продукта по возрастанию.

        SELECT [Name], [Color]
        FROM [AdventureWorks2019].[Production].[Product]
        ORDER BY 1 ASC
        
12. Вывести названия продукта, цвет и отпускную цену для таких товаров, у
которых цвет определен и цена отлична от нуля, и отсортировать полученный
список по возрастанию цвета товара и убыванию отпускной цены.

14.
