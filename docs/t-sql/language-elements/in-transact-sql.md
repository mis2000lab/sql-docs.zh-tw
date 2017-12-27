---
title: "在 (TRANSACT-SQL) |Microsoft 文件"
ms.custom: 
ms.date: 08/29/2016
ms.prod: sql-non-specified
ms.prod_service: database-engine, sql-database, sql-data-warehouse, pdw
ms.service: 
ms.component: t-sql|language-elements
ms.reviewer: 
ms.suite: sql
ms.technology: database-engine
ms.tgt_pltfrm: 
ms.topic: language-reference
f1_keywords:
- IN_TSQL
- IN
dev_langs: TSQL
helpviewer_keywords:
- values [SQL Server], matching
- NOT IN keyword
- 8623 (Database Engine error)
- matching values in subquery or list [SQL Server]
- IN keyword
- 8632 (Database Engine error)
ms.assetid: 4419de73-96b1-4dfe-8500-f4507915db04
caps.latest.revision: "37"
author: BYHAM
ms.author: rickbyh
manager: jhubbard
ms.workload: Active
ms.openlocfilehash: 487c576ea2323ea7da9726dfb161889e12aff0d9
ms.sourcegitcommit: 66bef6981f613b454db465e190b489031c4fb8d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2017
---
# <a name="in-transact-sql"></a>IN (Transact-SQL)
[!INCLUDE[tsql-appliesto-ss2008-all-md](../../includes/tsql-appliesto-ss2008-all-md.md)]

  判斷指定的值是否符合子查詢或清單中的任何值。  
  
 ![主題連結圖示](../../database-engine/configure-windows/media/topic-link.gif "主題連結圖示") [Transact-SQL 語法慣例](../../t-sql/language-elements/transact-sql-syntax-conventions-transact-sql.md)  
  
## <a name="syntax"></a>語法  
  
```  
test_expression [ NOT ] IN   
    ( subquery | expression [ ,...n ]  
    )   
```  
  
## <a name="arguments"></a>引數  
 *test_expression*  
 任何有效[運算式](../../t-sql/language-elements/expressions-transact-sql.md)。  
  
 *子查詢*  
 這是有單一資料行結果集的子查詢。 這個資料行必須有相同的資料類型為*test_expression*。  
  
 *運算式*[ **，**...*n* ]  
 這是要進行相符測試的運算式清單。 所有運算式都必須都具有相同型別的*test_expression*。  
  
## <a name="result-types"></a>結果類型  
 **布林**  
  
## <a name="result-value"></a>結果值  
 如果值*test_expression*等於傳回的任何值*子查詢*或等於任何*運算式*從逗號分隔清單中，結果為 TRUE;否則，結果值為 FALSE。  
  
 使用 NOT IN 變換正負號*子查詢*值或*運算式*。  
  
> [!CAUTION]  
>  任何 null 值傳回*子查詢*或*運算式*，相較於*test_expression*使用 IN 或不在傳回 UNKNOWN。 將 Null 值與 IN 或 NOT IN 一起使用可能會產生非預期的結果。  
  
## <a name="remarks"></a>備註  
 明確地括在 IN 子句中包括極大量的值 （數千個以逗號分隔值），可以耗用資源，並傳回錯誤 8623 或 8632。 若要解決這個問題，將項目儲存在資料表中，清單中，並使用 IN 子句內的 SELECT 子查詢。  
  
 錯誤 8623:  
  
 `The query processor ran out of internal resources and could not produce a query plan. This is a rare event and only expected for extremely complex queries or queries that reference a very large number of tables or partitions. Please simplify the query. If you believe you have received this message in error, contact Customer Support Services for more information.`  
  
 錯誤 8632：  
  
 `Internal error: An expression services limit has been reached. Please look for potentially complex expressions in your query, and try to simplify them.`  
  
## <a name="examples"></a>範例  
  
### <a name="a-comparing-or-and-in"></a>A. 比較 OR 及 IN  
 下列範例會選取設計工程師、工具設計師或行銷助理等員工名單。  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p  
JOIN HumanResources.Employee AS e  
    ON p.BusinessEntityID = e.BusinessEntityID  
WHERE e.JobTitle = 'Design Engineer'   
   OR e.JobTitle = 'Tool Designer'   
   OR e.JobTitle = 'Marketing Assistant';  
GO  
```  
  
 不過，您將利用 IN 來擷取相同的結果。  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName, e.JobTitle  
FROM Person.Person AS p  
JOIN HumanResources.Employee AS e  
    ON p.BusinessEntityID = e.BusinessEntityID  
WHERE e.JobTitle IN ('Design Engineer', 'Tool Designer', 'Marketing Assistant');  
GO  
```  
  
 以下是任何一項查詢的結果集。  
  
```  
FirstName   LastName      Title  
---------   ---------   ---------------------  
Sharon      Salavaria   Design Engineer                                     
Gail        Erickson    Design Engineer                                     
Jossef      Goldberg    Design Engineer                                     
Janice      Galvin      Tool Designer                                       
Thierry     D'Hers      Tool Designer                                       
Wanida      Benshoof    Marketing Assistant                                 
Kevin       Brown       Marketing Assistant                                 
Mary        Dempsey     Marketing Assistant                                 
  
(8 row(s) affected)  
```  
  
### <a name="b-using-in-with-a-subquery"></a>B. 使用 IN 搭配子查詢  
 下列範例在 `SalesPerson` 資料表中，針對年度銷售配額超出 $250,000 之員工來尋找銷售人員的所有識別碼，之後，再從 `Employee` 資料表中，選取 `EmployeeID` 符合 `SELECT` 子查詢結果的所有員工姓名。  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName  
FROM Person.Person AS p  
    JOIN Sales.SalesPerson AS sp  
    ON p.BusinessEntityID = sp.BusinessEntityID  
WHERE p.BusinessEntityID IN  
   (SELECT BusinessEntityID  
   FROM Sales.SalesPerson  
   WHERE SalesQuota > 250000);  
GO  
```  
  
 [!INCLUDE[ssResult](../../includes/ssresult-md.md)]  
  
```  
FirstName   LastName                                             
---------   --------   
Tsvi         Reiter                                              
Michael      Blythe                                              
Tete         Mensa-Annan                                         
  
(3 row(s) affected)  
```  
  
### <a name="c-using-not-in-with-a-subquery"></a>C. 使用 NOT IN 搭配子查詢  
 下列範例會尋找配額不超出 $250,000 的銷售人員。 `NOT IN` 會尋找不符合值清單項目的銷售人員。  
  
```  
-- Uses AdventureWorks  
  
SELECT p.FirstName, p.LastName  
FROM Person.Person AS p  
    JOIN Sales.SalesPerson AS sp  
    ON p.BusinessEntityID = sp.BusinessEntityID  
WHERE p.BusinessEntityID NOT IN  
   (SELECT BusinessEntityID  
   FROM Sales.SalesPerson  
   WHERE SalesQuota > 250000);  
GO  
```  
  
## <a name="examples-includesssdwfullincludessssdwfull-mdmd-and-includesspdwincludessspdw-mdmd"></a>範例：[!INCLUDE[ssSDWfull](../../includes/sssdwfull-md.md)]和[!INCLUDE[ssPDW](../../includes/sspdw-md.md)]  
  
### <a name="d-using-in-and-not-in"></a>D. 在IN和 NOT IN中使用  
 下列範例會尋找所有項目`FactInternetSales`資料表符合`SalesReasonKey`值`DimSalesReason`資料表。  
  
```  
-- Uses AdventureWorks  
  
SELECT * FROM FactInternetSalesReason   
WHERE SalesReasonKey   
IN (SELECT SalesReasonKey FROM DimSalesReason);   
```  
  
 下列範例會尋找所有項目`FactInternetSalesReason`不相符的資料表`SalesReasonKey`值`DimSalesReason`資料表。  
  
```  
-- Uses AdventureWorks  
  
SELECT * FROM FactInternetSalesReason   
WHERE SalesReasonKey   
NOT IN (SELECT SalesReasonKey FROM DimSalesReason);  
```  
  
### <a name="e-using-in-with-an-expression-list"></a>E. 使用以運算式清單  
 下列範例會尋找所有識別碼中銷售人員`DimEmployee`資料表之員工的第一個名稱也就是 `Mike`或`Michael`。  
  
```  
-- Uses AdventureWorks  
  
SELECT FirstName, LastName  
FROM DimEmployee  
WHERE FirstName IN ('Mike', 'Michael');  
```  
  
## <a name="see-also"></a>請參閱＜  
 [案例 &#40;TRANSACT-SQL &#41;](../../t-sql/language-elements/case-transact-sql.md)   
 [運算式 &#40;TRANSACT-SQL &#41;](../../t-sql/language-elements/expressions-transact-sql.md)   
 [內建函數 &#40;Transact-SQL&#41;](~/t-sql/functions/functions.md)   
 [運算子 &#40;TRANSACT-SQL &#41;](../../t-sql/language-elements/operators-transact-sql.md)   
 [SELECT &#40;Transact-SQL&#41;](../../t-sql/queries/select-transact-sql.md)   
 [其中 &#40;TRANSACT-SQL &#41;](../../t-sql/queries/where-transact-sql.md)   
 [所有 &#40;TRANSACT-SQL &#41;](../../t-sql/language-elements/all-transact-sql.md)   
 [部分 &#124;任何 &#40;TRANSACT-SQL &#41;](../../t-sql/language-elements/some-any-transact-sql.md)  
  
  



