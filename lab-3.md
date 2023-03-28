### Типы соединений
- INNER JOIN - не учитывает NULL в обеих таблицах

       SELECT p.Name, s.Name
       FROM [Production].[Product] p INNER JOIN [Production].[ProductSubcategory] s
       ON p.ProductSubcategoryID=s.ProductSubcategoryID
    
    Получаем список товаров и их подкатегорий, если они определены
- LEFT JOIN - учитывает NULL в левой таблице, т.е. у неопределенной подкатегории будет название NULL
- RIGHT JOIN - учитывает NULL в правой таблице, т.е. теоретически может быть NULL товар
- FULL OUTER JOIN / FULL JOIN - учитывает NULL в обеих таблицах
- CROSS JOIN - полное декартово произведение двух таблиц (все возможные сочетания значений)

#### Примеры запросов:
1. Найти название продуктов и название подкатегорий этих продуктов, у которых
отпускная цена больше 100, не включая случаи, когда продукт не относится ни к
какой подкатегории

        SELECT p.Name as Product, s.Name as Category
        FROM Production.Product as p JOIN Production.ProductSubcategory as s ON p.ProductSubcategoryID=s.ProductSubcategoryID
        WHERE ListPrice > 100
2. Найти название продуктов и название подкатегорий этих продуктов, у которых
отпускная цена больше 100, включая случаи, когда продукт не относится ни к
какой категории

        SELECT p.Name as Product, s.Name as Category
        FROM Production.Product as p LEFT JOIN Production.ProductSubcategory as s ON p.ProductSubcategoryID=s.ProductSubcategoryID
        WHERE ListPrice > 100

3. Найти название продуктов и название категорий из таблицы ProductCategory, с
которой связан этот продукт, не включая случаи, когда у продукта нет
подкатегории

        SELECT P.Name, PC.Name
        FROM [Production].[Product] AS P INNER JOIN [Production].[ProductSubcategory] AS PSC
        ON P.ProductSubcategoryID=PSC.ProductSubcategoryID  INNER JOIN [Production].[ProductCategory] AS PC
        ON PSC.ProductCategoryID=PC.ProductCategoryID

4. Найти название продукта, отпускную цену продукта, а также последнюю
отпускную цену этого продукта (LAStReceiptCost), которую можно узнать из
таблицы ProductVendor

        SELECT p.Name, p.ListPrice, pv.LastReceiptCost
        FROM [Production].[Product] AS p JOIN [Purchasing].[ProductVendor] AS pv ON p.ProductID=pv.ProductID

5. Найти название продукта, отпускную цену продукта, а также последнюю
отпускную цену этого продукта (LAStReceiptCost), которую можно узнать из
таблицы ProductVendor, для таких продуктов, у которых отпускная цена
оказалась ниже последней отпускной цены у поставщика, исключив те товары,
для которых отпускная цена равна нулю

        SELECT p.Name, p.ListPrice, pv.LastReceiptCost
        FROM [Production].[Product] AS p JOIN [Purchasing].[ProductVendor] AS pv ON p.ProductID=pv.ProductID
        WHERE p.listPrice != 0 AND p.ListPrice < pv.LastReceiptCost
        
6. Найти количество товаров, которые поставляют поставщики с самым низким
кредитным рейтингом (CreditRatINg принимает целые значение от
минимального, равного 1, до максимального, равного 5)

7. 
