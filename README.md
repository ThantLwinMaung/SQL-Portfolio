# SQL-Portfolio
My personal SQL portfolio showcasing real-world projects using SQL Server, AdventureWorks, and WideWorldImporters.
Jooshu's SQL Portfolio

Welcome to my SQL portfolio. I use Microsoft SQL Server, AdventureWorks2022, and WideWorldImporters to build data analysis projects.

 Projects

Sales Trend Analysis
- Goal: Analyze monthly sales trends by product category.
- Tools: SQL Server, AdventureWorks2022
- Skills: `JOIN`, `GROUP BY`, `SUM`, `DATEPART`
- Query:
sql
SELECT 
  DATENAME(MONTH, OrderDate) AS Month,
  SUM(LineTotal) AS TotalSales
FROM Sales.SalesOrderHeader h
JOIN Sales.SalesOrderDetail d ON h.SalesOrderID = d.SalesOrderID
GROUP BY DATENAME(MONTH, OrderDate)
ORDER BY TotalSales DESC;
