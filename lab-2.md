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
