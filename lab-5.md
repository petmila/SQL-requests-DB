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
  
