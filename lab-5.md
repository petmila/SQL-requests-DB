### Представления.
1. Создается запрос, ему присваивается псевдоним, а затем его можно использовать как таблицу с данными.

Пусть нужно найти для каждой подкатегории количество товаров, у которых цена выше средней цены в
подкатегории, и количество товаров, у которых цена ниже средней цены в подкатегории.
Оформить вывод в виде трех столбцов: номер подкатегории, первый показатель, второй показатель.

    SELECT T1.PS, T1.c, T2.c FROM
    (
      SELECT COUNT(*) AS c, [ProductSubcategoryID] AS PS
      FROM [Production].[Product] AS P
      WHERE [ListPrice]<
      (
        SELECT avg([ListPrice])
        FROM [Production].[Product] AS PT
        WHERE pt.ProductSubcategoryID=p.ProductSubcategoryID
      )
      GROUP BY [ProductSubcategoryID]) AS T1 
    INNER JOIN
    (
      SELECT count(*) AS c, [ProductSubcategoryID] AS PS
      FROM [Production].[Product] AS P
      WHERE [ListPrice]>=
      (
        SELECT avg([ListPrice])
        FROM [Production].[Product] AS PT
        WHERE pt.ProductSubcategoryID=p.ProductSubcategoryID
      )
      GROUP BY [ProductSubcategoryID]) AS T2 
    ON T1.PS=T2.PS
2. Конструкция WITH

Найти количество чеков, приходящихся на одного покупателя на каждый год.

    WITH Sales_CTE (SalesPersonID, SalesORDERID, SalesYear) AS
    (
      SELECT SalesPersonID, SalesORDERID, YEAR(ORDERDate) AS SalesYear
      FROM Sales.SalesORDERHeader
      WHERE SalesPersonID IS NOT NULL
    )
    SELECT SalesPersonID, COUNT(SalesORDERID) AS TotalSales, SalesYear
    FROM Sales_CTE
    GROUP BY SalesYear, SalesPersonID
    ORDER BY SalesPersonID, SalesYear

* Примеры с решениями

3. Вывести на экран следующую информацию: название товара, название
категории к которой он относится и общее количество товаров в этой
категории 

        WITH CategoryData (CategoryId, Amount) as
        (
	        Select s.ProductCategoryID, count(p.ProductId)
	        From Production.Product as p join Production.ProductSubcategory as s on p.ProductSubcategoryID = s.ProductSubcategoryID
	        Group by s.ProductCategoryID
        )

        Select p.Name, c.Name, Amount
        From Production.Product as p join Production.ProductSubcategory as cp on
        p.ProductSubcategoryID = cp.ProductSubcategoryID join Production.ProductCategory as c on 
        cp.ProductCategoryID = c.ProductCategoryID join CategoryData on
        c.ProductCategoryID = CategoryId

4. Вывести на экран номера покупателей, количество купленных ими
товаров, и количество чеков, которые у них были 

        WITH CustomerProductData (CustomerId, amountProducts) as
        (
            SELECT CustomerId, count(d.ProductId)
            From Sales.SalesOrderHeader as h join Sales.SalesOrderDetail as d on
            h.SalesOrderID = d.SalesOrderID
            Group by CustomerID
        ),
        CustomerOrderData(CustomerId, amountOrders) as
        (
            Select CustomerId, count(SalesOrderId)
            From Sales.SalesOrderHeader
            Group by CustomerID
        )

        Select p.CustomerId, amountProducts, amountOrders
        From CustomerProductData as p join CustomerOrderData as o on
        p.CustomerId = o.CustomerId
5. Найти номера покупателей, у которых все купленные ими товары были
куплены как минимум дважды, т.е на два разных чека

        WITH Data (CustomerId, ProductId, OrderAmount) as
        (
	        Select h.CustomerId, o.ProductID, count(h.SalesOrderID)
	        From Sales.SalesOrderHeader as h join Sales.SalesOrderDetail as o on
	        h.SalesOrderId = o.SalesOrderId
	        Group by CustomerID, ProductID
        )
        Select CustomerId
        From Data
        Group by CustomerId
        Having min(OrderAmount) > 1

6. Найти номера покупателей, которые покупали товары из более чем
половины подкатегорий товаров, и для них вывести информацию: номер
покупателя, количество чеков, средняя сумма на один чек.

        WITH CustomerData (CustomerId, TotalDueAvg, amountOrders) as 
        (
	        Select CustomerId, avg(TotalDue), count(SalesOrderId)
	        From Sales.SalesOrderHeader
	        Group BY CustomerId
        ),
        SubCategoryData (CustomerId, Sub) as 
        (
	        Select CustomerId, count(Distinct p.ProductSubcategoryID)
	        From Sales.SalesOrderHeader as h join Sales.SalesOrderDetail as o on
	        h.SalesOrderId = o.SalesOrderId join Production.Product as p on
	        p.ProductId = o.ProductId
	        Group by CustomerID
	        Having count(Distinct p.ProductSubcategoryID) >
	        (
		        Select count(ps.ProductSubcategoryID)/2
		        From Production.ProductSubcategory as ps
	        )
        )
        Select d.CustomerId, TotalDueAvg, amountOrders, Sub
        From CustomerData as d join SubCategoryData as s on 
        d.CustomerId = s.CustomerId
        Where d.CustomerId in 
        (	
	        Select CustomerId 
	        From SubCategoryData
        )

7. Найти для каждого чека количество категорий и подкатегорий его товаров


	WITH OrderData(OrderId, CountCategory, CountSubcategory) as
	(
		SELECT s.SalesOrderID, count(distinct ps.ProductSubcategoryID), count(distinct ps.ProductCategoryID)
		From Sales.SalesOrderDetail as s join Production.Product as p on
		s.ProductId = p.ProductId join Production.ProductSubcategory as ps on
		ps.ProductSubcategoryID = p.ProductSubcategoryID
		Group by s.SalesOrderID
	)
	Select OrderId, CountCategory, CountSubcategory
	From OrderData
	Order by OrderId
 
