### Использование подзапросов

    SELECT [Name], [ListPrice]
    FROM [Production].[Product]
    WHERE [Color]='Red'AND [ListPrice]= (SELECT MAX([ListPrice]) FROM [Production].[Product] WHERE [Color]='Red')
    
- оператор ALL - получить список товаров, цена которых больше цены любого красного товара

      SELECT [Name]
      FROM [Production].[Product]
      WHERE [ListPrice] >ALL (SELECT [ListPrice] FROM [Production].[Product] WHERE [Color]='Red')
- оператор IN - список товаров с цветом, совпадающих с одним из цветов тех товаров, у которых цена > 3000

      SELECT [Name]
      FROM [Production].[Product]
      WHERE [Color] IN (SELECT [Color] FROM [Production].[Product] WHERE [ListPrice]>3000)
- рекурсия подзапросов - найти название категории с самым дорогим товаром

      SELECT [Name]
      FROM [Production].[ProductCategory]
      WHERE [ProductCategoryID] IN (SELECT [ProductCategoryID] FROM [Production].[ProductSubcategory]
      WHERE [ProductSubcategoryID] IN (SELECT [ProductSubcategoryID] FROM [Production].[Product]
      WHERE [ListPrice] = (SELECT MAX([ListPrice]) FROM [Production].[Product])))
- несколько подзапросов на одном уровне вложенности - получить список товаров, у которых цвет совпадает с цветом самого дорогого товара, и
стиль совпадает со стилем самого дорого товара

      SELECT [Name]
      FROM [Production].[Product]
      WHERE [Color] IN (SELECT [Color] FROM [Production].[Product]
        WHERE [ListPrice] = (SELECT MAX([ListPrice]) FROM [Production].[Product]))
      AND [Style] IN (SELECT [Style] FROM [Production].[Product]
        WHERE [ListPrice] = (SELECT MAX([ListPrice]) FROM [Production].[Product]))
- формирование выборки - найти номер подкатегории товаров с наибольшим количеством товаров

      SELECT [ProductSubcategoryID]
      FROM [Production].[Product]
      GROUP BY [ProductSubcategoryID] HAVING COUNT(*)=
        (SELECT TOP 1 COUNT(*) FROM [Production].[Product]
        WHERE [ProductSubcategoryID] IS NOT NULL
        GROUP BY [ProductSubcategoryID] ORDER BY 1 DESC)
- связанные подзапросы - получить список самых дорогих товаров в каждой из подкатегорий

      SELECT [Name]
      FROM [Production].[Product] AS P1
      WHERE [ListPrice]= (SELECT MAX([ListPrice]) FROM [Production].[Product] AS P2
        WHERE P1.ProductSubcategoryID=P2.ProductSubcategoryID)
- название продукта и название подкатегории, к которой он относится
      
      SELECT [Name],
	      (SELECT [Name]
	      FROM [Production].[ProductSubcategory] AS PS
	      WHERE P1.ProductSubcategoryID=PS.ProductSubcategoryID)
      FROM [Production].[Product] AS P1
#### Примеры запросов с решениями
- Найти название подкатегории с наибольшим количеством продуктов, без учета
продуктов, для которых подкатегория не определена.

      SELECT [Name]
      FROM Production.ProductSubcategory as ps
      WHERE ps.ProductSubcategoryID IN
        (SELECT p.ProductSubcategoryID FROM Production.Product as p
        WHERE p.ProductSubcategoryID IS NOT NULL
        GROUP BY p.ProductSubcategoryID HAVING COUNT(*)= 
          (SELECT TOP 1 COUNT(*)
          FROM [Production].[Product]
          WHERE [ProductSubcategoryID] IS NOT NULL
          GROUP BY [ProductSubcategoryID]
          ORDER BY 1 DESC
        )
      )      
- 

-
