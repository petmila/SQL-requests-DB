### Оконные функции и OVER

Over определяет секционирование и упорядочивание набора сток до применения оконной функции

    OVER (
    [ <PARTITION BY столбец> ]
    [ <ORDER BY столбец> ]
    [ <ROW или RANGE столбец> ]
    )

* Partition by - секционирование результирующего набора
* Order by - упорядочивание каждой секции
* ROW or RANGE - ограничение сток в пределах секции

#### Список функций:
* First_value - первое из упорядоченного набора значений
* Last_value - последнее из упорядоченного набора
* Dense_rank() - нумирует значения по порядку без пропусков (одинаковые значения - одинаковый номер)

### Примеры с собственными решениями:
1. Для каждого чека найти его номер и количество категорий и подкатегорий.

        with numered_data(salesId, subcategories, categories) as
        (
	        Select s.SalesOrderID, dense_rank() over
	        (
		        partition by s.SalesOrderID 
		        order by p.ProductSubcategoryID
	        ) as subcategories, dense_rank() over 
	        (
		        partition by s.SalesOrderID 
		        order by ps.ProductCategoryID
	        ) as categories

	        From Sales.SalesOrderDetail as s
	        inner join Production.Product as p
	        on p.ProductID=s.ProductID
	        inner join Production.ProductSubcategory as ps
	        on p.ProductSubcategoryID = ps.ProductSubcategoryID
        )
        Select distinct salesId, max (subcategories) over 
        (
	        partition by salesId
        ) as subcategories, max(categories) over
        (
	        partition by salesId
        ) as categories
        From numered_data
2. Название товара, название категории, к которой он относится и общее количество товаров в категории.

        with numered_data (ProductName, CategoryName, productRank) as
        (
            Select p.Name, pc.Name, dense_rank() over 
            (
                partition by pc.Name
                order by p.ProductId
            )
            From Production.Product as p 
            inner join Production.ProductSubcategory as ps
            on p.ProductSubcategoryID = ps.ProductSubcategoryID
            inner join Production.ProductCategory as pc
            on ps.ProductCategoryID = pc.ProductCategoryID
        )

        Select distinct ProductName, CategoryName, max(productRank) over
        (
            partition by CategoryName
        ) as ProductAmountInCategory
        From numered_data
3. Для каждого товара найти соотношение покупателей, купивших товар к общему числу совершавших покупки

        Select distinct s.ProductID, count(h.CustomerId) over 
        (
            partition by s.ProductID
        ) as currentCustomers,
        (
            Select count(distinct CustomerId)
            From Sales.SalesOrderHeader
        ) as allCustomers

        From Sales.SalesOrderHeader as h
        inner join Sales.SalesOrderDetail as s
        on h.SalesOrderID = s.SalesOrderID
        Group by s.ProductID, h.CustomerID
        Order by s.ProductId
4. Для каждого продукта количество его продаж и соотношение числа его покупателей к числу покупателей в данной категории.

        with numered_data (ProductId, rankedOrders, rankedCustomers, rankedCustomersByCategory, CategoryId) as
        (
            Select s.ProductId, dense_rank() over
            (
                partition by s.ProductId
                order by h.SalesOrderId
            ), dense_rank() over
            (
                partition by s.ProductId
                order by h.CustomerId
            ), dense_rank() over
            (
                partition by ps.ProductCategoryId
                order by h.CustomerId
            ), ps.ProductCategoryId
            From Sales.SalesOrderHeader as h
            inner join Sales.SalesOrderDetail as s
            on h.SalesOrderID = s.SalesOrderID
            inner join Production.Product as p
            on p.ProductID = s.ProductID
            inner join Production.ProductSubcategory as ps
            on p.ProductSubcategoryID = ps.ProductSubcategoryID
        )
        Select distinct ProductId, max(rankedOrders) over
        (
            partition by ProductId
        ) as orders, max(rankedCustomers) over
        (
            partition by ProductId
        ) as customers, max (rankedCustomersByCategory) over
        (
            partition by CategoryId
        ) as allCustomers, CategoryId
        From numered_data
        order by ProductId
5. Название товара, название подкатегории и общее количество товаров в подкатегории.

        with numered_data (ProductName, SubcategoryName, productRank) as
        (
            Select p.Name, ps.Name, dense_rank() over 
            (
                partition by ps.Name
                order by p.ProductId
            )
            From Production.Product as p 
            inner join Production.ProductSubcategory as ps
            on p.ProductSubcategoryID = ps.ProductSubcategoryID
        )

        Select distinct ProductName, SubcategoryName, max(productRank) over
        (
            partition by SubcategoryName
        ) as ProductAmountInSubcategory
        From numered_data
6. Найти для каждого покупателя все чеки, и для каждого чека вывести информацию: номер покупателя, номер
чека, и сумма всех затрат этого покупателя с момента первой покупки и до данного чека.

        Select CustomerID, SalesOrderId, sum(TotalDue) over
        (
            partition by CustomerId
            order by OrderDate asc
            rows between UNBOUNDED PRECEDING and Current row

        ) as SumBeforeOrder
        From Sales.SalesOrderHeader
        order by CustomerId, SalesOrderId
7. Для каждого покупателя соотношение количества купленных им товаров из каждой категории к количеству не
купленных им товаров из той же категории.

        with numered_data (customerId, productId, categoryId, rankedProducts) as
        (
            Select h.CustomerId, p.ProductId, ps.ProductCategoryId, dense_rank() over
            (
                partition by h.CustomerId, ps.ProductCategoryId
                order by p.ProductId
            )
            From Sales.SalesOrderHeader as h
            inner join Sales.SalesOrderDetail as s
            on h.SalesOrderID = s.SalesOrderID
            inner join Production.Product as p
            on p.ProductID = s.ProductID
            inner join Production.ProductSubcategory as ps
            on p.ProductSubcategoryID = ps.ProductSubcategoryID
        ),
        category_data (amount, categoryId) as
        (
            Select count(ProductId), ps.ProductCategoryId
            From Production.Product as p
            inner join Production.ProductSubcategory as ps
            on p.ProductSubcategoryID = ps.ProductSubcategoryID
            Group by ps.ProductCategoryId
        ),
        final_data (customerId, categoryId, gotInCategory, allInCategory) as
        (
            Select customerId, c.categoryId, max(rankedProducts) over
            (
                partition by customerId, c.categoryId
            ) as amountInCategory, c.amount
            From numered_data as n inner join category_data as c
            on c.categoryId = n.categoryId
        )

        Select distinct categoryId, customerId, gotInCategory, allInCategory - gotInCategory as notGotInCategory
        From final_data
        Order by customerId
