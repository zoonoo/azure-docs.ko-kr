<properties
	pageTitle="SQL 메모리 내, 시작 | Microsoft Azure"
	description="SQL 메모리 내 기술은 트랜잭션 및 분석 작업의 성능을 크게 향상시킵니다. 이러한 기술을 활용하는 방법을 알아봅니다."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/20/2015"
	ms.author="jodebrui"/>


# SQL 데이터베이스에서 메모리 내 시작(미리 보기)


메모리 내 기술은 합당한 상황에서 트랜잭션 및 분석 워크로드의 성능을 크게 향상시킵니다.

이 문서는 두 가지 데모 즉, 메모리 내 OLTP에 대한 데모와 메모리 내 분석에 대한 데모를 강조합니다. 각 데모에는 데모 실행에 필요한 단계와 코드가 완비되어 있습니다. 다음 중 하나가 가능합니다.

- 코드를 사용하여 성능 결과의 차이를 보기 위한 변형을 테스트하거나
- 코드를 읽어서 시나리오를 이해하고 메모리 내 개체를 만들어서 활용하는 방법을 알아봅니다.


#### 메모리 내 OLTP

메모리 내 [OLTP](#install_oltp_manuallink)(온라인 트랜잭션 프로세스)의 기능:

- 메모리 최적화된 테이블.
- 고유하게 컴파일된 저장 프로시저.


메모리 액세스에 최적화된 테이블에는 하드 드라이브에 있는 표준 표현 외에도 활성 메모리에 하나의 표현이 있습니다. 테이블에 대한 비즈니스 트랜잭션은 활성 메모리에 있는 표현과만 직접적으로 상호 작용하기 때문에 더 빨리 수행됩니다.

메모리 내 OLTP를 사용하면 워크로드의 고유 정보에 따라서, 트랜잭션 처리량을 최대 30배까지 높일 수 있습니다.


고유하게 컴파일된 저장 프로시저는 이전 방식으로 해석되는 저장 프로시저로 생성되는 경우에 필요한 명령보다 런타임 중에 컴퓨터 명령이 덜 필요합니다. 네이티브 컴파일 결과의 시간이 해석되는 시간의 100분의 1인 경우를 보았습니다.


#### 메모리 내 분석 

메모리 내 [분석](#install_analytics_manuallink)의 기능:

- Columnstore 인덱스


columnstore 인덱스는 데이터를 색다른 방법으로 압축하여 쿼리 워크로드의 성능을 향상시킵니다.

다른 서비스에서 columnstore 인덱스는 필수적으로 최적화된 메모리입니다. 하지만 Azure SQL 데이터베이스에서 columnstore 인덱스는 인덱스를 생성하는 기존 테이블과 함께 하드 드라이브에 존재할 수 있습니다.


#### 실시간 분석

[실시간 분석](http://msdn.microsoft.com/library/dn817827.aspx)의 경우, 다음을 위해 메모리 내 OLTP와 분석을 결합합니다.

- 운영 데이터 기반 실시간 비즈니스 통찰력.


#### Availability


GA, 일반 공급:

- *디스크에 있는* [columnstore 인덱스](http://msdn.microsoft.com/library/dn817827.aspx).


미리 보기:

- 메모리 내 OLTP
- 메모리 액세스에 최적화된 columnstore 인덱스를 포함하는 메모리 내 분석
- 실시간 운영 분석


메모리 내 기능이 미리 보기일 때 고려해야 할 사항은 [이 문서의 뒷부분](#preview_considerations_for_in_memory)에 설명되어 있습니다.


> [AZURE.NOTE]미리 보기 기능은 표준 또는 기본 서비스 계층의 데이터베이스에는 제공되지 않고, [*프리미엄*](sql-database-service-tiers.md) Azure SQL 데이터베이스에만 제공됩니다.



<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## A. 메모리 내 OLTP 샘플 설치

[Azure Preview 포털](http://portal.azure.com/)에서 몇 번 클릭으로 AdventureWorksLT [V12] 샘플 데이터베이스를 만들 수 있습니다. 그런 다음 이 섹션의 단계에서는 AdventureWorksLT 데이터베이스를 보강할 수는 방법을 설명합니다.

- 메모리 내 테이블.
- 고유하게 컴파일된 저장 프로시저.


#### 설치 단계

1. [Azure Preview 포털](http://portal.azure.com/)에서 V12 서버의 Premium 데이터베이스를 만듭니다. AdventureWorksLT [V12] 샘플 데이터베이스에 **소스**를 설정합니다.
 - 자세한 지침은 [첫 번째 Azure SQL 데이터베이스 만들기](sql-database-get-started.md)를 참조하세요.

2. SQL Server Management Studio[(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx)를 사용하여 데이터베이스에 연결합니다.

3. [메모리 내 OLTP Transact-SQL 스크립트](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)를 클립보드에 복사합니다.
 - T-SQL 스크립트는 1단계에서 만든 AdventureWorksLT 샘플 데이터베이스에서 필요한 메모리 내 개체를 만듭니다.

4. SSMS에 T-SQL 스크립트를 붙여 넣고 스크립트를 실행합니다.
 - 다음과 같은 `MEMORY_OPTIMIZED = ON` 절 CREATE TABLE 문이 중요합니다.


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
	[SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
	...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### 오류 40536


T-SQL 스크립트를 실행할 때 오류 40536을 받게 되면 다음 T-SQL 스크립트를 실행하여 데이터베이스에서 메모리를 지원하는지 여부를 확인합니다.


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


결과 **0**은 메모리 내가 지원되지 않음을 나타내고 1은 지원됨을 나타냅니다. 문제를 진단하려면:

- 데이터베이스가 메모리 내 OLTP 기능이 미리 보기에서 활성이 된 후에 생성되었는지 확인합니다.
- 데이터베이스가 프리미엄 서비스 계층에 있는지 확인합니다.


#### 생성된 메모리 최적화된 항목에 대한 정보

**테이블**: 샘플은 다음과 같은 메모리 액세스에 최적화된 테이블을 포함합니다.

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


다음으로 SSMS의 **개체 탐색기**를 통해 메모리 액세스에 최적화된 테이블을 검사할 수 있습니다.

- 마우스 오른쪽 단추로 **테이블** > **필터** > **필터 설정** > **메모리 액세스에 최적화된**을 클릭하면 1과 같습니다.


또는 다음과 같은 카탈로그 뷰를 쿼리할 수 있습니다.


```
SELECT is_memory_optimized, name, type_desc, durability_desc
	FROM sys.tables
	WHERE is_memory_optimized = 1;
```


**고유하게 컴파일된 저장 프로시저**: SalesLT.usp\_InsertSalesOrder\_inmem는 카탈로그 뷰 쿼리를 통해 검사할 수 있습니다.


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
	FROM sys.sql_modules
	WHERE uses_native_compilation = 1;
```


&nbsp;

## 샘플 OLTP 워크로드 실행

다음 두 *저장 프로시저* 간의 유일한 차이점은 첫 번째 절차는 메모리 액세스에 최적화된 테이블 버전을 사용하는 반면 두 번째 절차에서는 일반 디스크상의 테이블을 사용한다는 점입니다.

- SalesLT**.**usp\_InsertSalesOrder**\_inmem**
- SalesLT**.**usp\_InsertSalesOrder**\_ondisk**


이 섹션에는 편리한 **ostress.exe** 유틸리티를 사용하여 스트레스 수준에서 두 저장된 프로시저를 실행하는 방법을 살펴봅니다. 두 스트레스가 실행 완료하는 데 걸리는 시간을 비교할 수 있습니다.


ostress.exe를 실행하는 경우 모두에게 설계된 매개 변수 값을 전달하는 것이 좋습니다.

- -n100을 사용하여 많은 수의 동시 연결을 실행합니다.
- -r500을 사용하여 각 연결 루프를 수백 번 가집니다.


그러나 모든 것이 작동하도록 -n10 및 -r50과 같은 훨씬 작은 값으로 시작하려고 할 수도 있습니다.


### ostress.exe에 대한 스크립트


이 섹션에서는 ostress.exe 명령줄에 포함된 T-SQL 스크립트를 표시합니다. 스크립트는 이전에 설치한 T-SQL 스크립트에 의해 생성된 항목을 사용합니다.


다음 스크립트는 다음과 같은 메모리 액세스에 최적화된 *테이블*에 다섯 줄 항목의 샘플 판매 주문을 삽입합니다.

- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem


```
DECLARE
	@i int = 0,
	@od SalesLT.SalesOrderDetailType_inmem,
	@SalesOrderID int,
	@DueDate datetime2 = sysdatetime(),
	@CustomerID int = rand() * 8000,
	@BillToAddressID int = rand() * 10000,
	@ShipToAddressID int = rand() * 10000;
	
INSERT INTO @od
	SELECT OrderQty, ProductID
	FROM Demo.DemoSalesOrderDetailSeed
	WHERE OrderID= cast((rand()*60) as int);
	
WHILE (@i < 20)
begin;
	EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
		@DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
end
```


ostress.exe에 대한 이전 T-SQL의 \_ondisk 버전을 만들려면 *\_inmem* 문자열의 두 항목을 *\_ondisk*로 간단히 대체합니다. 이러한 대체는 테이블의 이름 및 저장된 프로시저에 영향을 줍니다.


### RML 유틸리티 및 ostress 설치


이상적으로 Azure VM에 ostress.exe를 실행합니다. AdventureWorksLT 데이터베이스가 있는 곳과 동일한 Azure 지리적 지역에 [Azure 가상 컴퓨터](http://azure.microsoft.com/documentation/services/virtual-machines/)를 만듭니다. 하지만 대신 노트북에서 ostress.exe를 실행할 수 있습니다.


VM 또는 선택한 호스트에서 ostress.exe를 포함하는 RML(Replay Markup Language) 유틸리티를 설치합니다.

- [메모리 내 OLTP에 대한 샘플 데이터베이스](http://msdn.microsoft.com/library/mt465764.aspx)에서 ostress.exe 관련 내용을 참조하세요.
 - 또는 [메모리 내 OLTP에 대한 샘플 데이터베이스](http://msdn.microsoft.com/library/mt465764.aspx)를 참조하세요.
 - 또는 [ostress.exe 설치에 대한 블로그](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)를 참조하세요.



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### 먼저 \_inmem 스트레스 작업을 실행합니다.


*RML Cmd Prompt* 창을 사용하여 ostress.exe 명령줄을 실행할 수 있습니다. 명령줄 매개 변수는 ostress에게 다음을 명령합니다.

- 동시에 100개의 연결(-n100)을 실행하도록 합니다.
- 각 연결이 T-SQL 스크립트를 50회(-r50) 실행하도록 설정합니다.


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


위의 ostress.exe 명령줄을 실행하려면:


1. 이전 실행으로 삽입된 모든 데이터를 삭제하도록 SSMS에서 다음 명령을 실행하여 데이터베이스 데이터 콘텐츠를 다시 설정합니다.```
EXECUTE Demo.usp_DemoReset;
```

2. 이전 ostress.exe 명령줄의 텍스트를 클립보드에 복사합니다.

3. 매개 변수-S -U -P -d에 대한 <placeholders>을(를) 올바른 실제 값으로 변경합니다.

4. 편집한 명령줄을 RML Cmd 창에서 실행합니다.


#### 결과는 기간입니다


ostress.exe가 완료되면 출력의 마지막 줄로 실행 기간을 RML Cmd 창에 작성합니다. 예를 들어 짧은 테스트 실행은 약 1.5분 동안 지속됩니다.

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### \_ondisk에 대해 다시 설정하고 편집한 다음 다시 실행


\_Inmem 실행에서 결과를 얻은 후 \_indisk 실행에 대해 다음 단계를 수행합니다.


1. 이전 실행으로 삽입된 모든 데이터를 삭제하도록 SSMS에서 다음 명령을 실행하여 데이터베이스를 다시 설정합니다. ```
EXECUTE Demo.usp_DemoReset;
```

2. ostress.exe 명령줄을 편집하여 모든 *\_inmem*을 *\_ondisk*로 대체합니다.

3. ostress.exe를 두 번째로 실행하고 기간 결과를 캡처합니다.

4. 양이 커질 수 있는 테스트 데이터를 확실히 제거하기 위해서 데이터베이스를 다시 설정합니다.


#### 예상된 비교 결과

데이터베이스와 동일한 Azure 지역의 Azure VM에서 ostress를 실행하여, 이렇게 간단한 워크로드에 대해 출력 메모리 내 테스트의 성능이 **9배** 향상되었습니다.


성능 향상은 고유하게 컴파일된 저장된 프로시저에 대한 변환이 추가되면 높을 수 있습니다.


## B. 메모리 내 분석 샘플 설치


이 섹션에서는 columnstore 인덱스와 일반 인덱스를 사용하는 경우의 IO 및 통계 결과를 비교합니다.


columnstore 인덱스는 논리적으로 일반 인덱스와 동일하지만 물리적으로 서로 다릅니다. columnstore 인덱스는 색다른 방법으로 데이터를 구성하여 데이터를 압축합니다. 이는 주요 성능 향상을 제공합니다.


OLTP 작업의 실시간 분석의 경우 비클러스터형 columnstore 인덱스를 사용하는 것이 좋습니다. 자세한 내용은 [Columnstore Indexes Described](http://msdn.microsoft.com/library/gg492088.aspx)를 참조하세요.



### columnstore 분석 테스트 준비


1. Azure 포털을 사용하여 샘플에서 새로운 AdventureWorksLT 데이터베이스를 만듭니다.
 - 정확한 이름을 사용합니다.
 - Premium 서비스 계층을 선택합니다.

2. [sql\_in memory\_analytics\_sample](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql)을 클립보드에 복사합니다.
 - T-SQL 스크립트는 1단계에서 만든 AdventureWorksLT 샘플 데이터베이스에서 필요한 메모리 내 개체를 만듭니다.
 - 스크립트는 차원 테이블과 두 개의 팩트 테이블을 만듭니다. 팩트 테이블은 각각 350만 개의 행으로 채워집니다.
 - 스크립트는 완료하는 데 15분 정도 걸릴 수 있습니다.

3. SSMS에 T-SQL 스크립트를 붙여 넣고 스크립트를 실행합니다.
 - 다음과 같은 **CREATE INDEX** 문의 **COLUMNSTORE** 키워드가 중요합니다.<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. AdventureWorksLT를 호환성 수준 130으로 설정합니다.<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - 수준 130은 메모리 내 기능과 직접적으로 연관되지 않습니다. 하지만 수준 130은 일반적으로 120이 제공하는 것보다 빠른 쿼리 성능을 제공합니다.


#### 중요한 테이블 및 columnstore 인덱스


- dbo.FactResellerSalesXL\_CCI는 클러스터형 **columnstore** 인덱스를 포함하는 테이블이고, 여기에는 *데이터* 수준의 고급 압축이 포함됩니다.

- dbo.FactResellerSalesXL\_PageCompressed는 동등한 일반 클러스터형 인덱스를 가지며 *페이지* 수준에서만 압축된 테이블입니다.


#### columnstore 인덱스를 비교하는 중요한 쿼리


[여기](http://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql)에 성능 향상을 확인하도록 실행할 수 있는 여러 유형의 T-SQL 쿼리가 있습니다. T-SQL 스크립트의 2단계에 직접 관심 있는 쿼리의 쌍이 있습니다. 두 쿼리는 한 줄에 대해서만 다릅니다.


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


클러스터형 columnstore 인덱스는 FactResellerSalesXL**\_CCI** 테이블에 있습니다.

다음 T-SQL 스크립트 발췌는 각 테이블의 쿼리에 대한 IO 및 TIME에 대한 통계를 출력합니다.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
	,e.ProductCategoryKey
	,FirstName + ' ' + LastName AS FullName
	,count(SalesOrderNumber) AS NumSales
	,sum(SalesAmount) AS TotalSalesAmt
	,Avg(SalesAmount) AS AvgSalesAmt
	,count(DISTINCT SalesOrderNumber) AS NumOrders
	,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
	AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## 메모리 내 OLTP에 대한 미리 보기 고려 사항


Azure SQL 데이터베이스의 메모리 내 OLTP 기능은 [2015년 10월 28일에 미리 보기에 대해 활성화](http://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/)되었습니다.


GA(일반 공급) 전의 미리 보기 단계 동안 메모리 내 OLTP는 다음에 대해서만 지원됩니다.

- *프리미엄* 서비스 계층에 있는 데이터베이스.

- 메모리 내 OLTP 기능이 활성화된 후 만들어진 데이터베이스.
 - 메모리 내 OLTP 기능이 활성화되기 전에 생성된 데이터베이스에서 복원된 새 데이터베이스는 메모리 내 OLTP를 지원할 수 없습니다.


확실하지 않은 경우 다음 T-SQL SELECT를 실행하여 데이터베이스에서 메모리 내 OLTP를 지원하는지 여부를 확인할 수 있습니다. 결과 **1**은 데이터베이스에서 메모리 내 OLTP 기능을 지원함을 의미합니다.

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


쿼리가 **1**을 반환하는 경우 메모리 내 OLTP는 이 데이터베이스 뿐만 아니라 이 데이터베이스를 기반으로 생성되는 모든 데이터베이스 복사 및 데이터베이스 복원에 지원됩니다.


#### 프리미엄에서만 허용되는 개체


데이터베이스가 다음과 같은 종류의 메모리 내 OLTP 개체 또는 형식을 포함하는 경우 데이터베이스의 서비스 계층을 프리미엄에서 기본 또는 표준으로 다운그레이드하는 작업은 지원되지 않습니다. 데이터베이스를 다운그레이드하려면 먼저 해당 개체를 삭제합니다.

- 메모리 최적화된 테이블
- 메모리에 최적화된 테이블 형식
- 고유하게 컴파일된 모듈


#### 기타 관계


- 탄력적 풀의 데이터베이스로 메모리 내 OLTP 기능 사용은 미리 보기 중에 지원되지 않지만 나중에 지원을 받을 수 있습니다.

- SQL 데이터 웨어하우스와 함께 메모리 내 OLTP은 사용은 지원되지 않습니다.
 - 메모리 내 분석의 columnstore 인덱스 기능은 SQL 데이터 웨어하우스에서 지원됩니다.

- 쿼리 저장소는 고유하게 컴파일된 모듈 내의 쿼리를 미리 보기 중에 캡처하지 않지만 나중에 캡처할 수 있습니다.

- 일부 Transact-SQL 기능은 메모리 내 OLTP를 사용하여 지원되지 않습니다. 이는 Microsoft SQL Server와 Azure SQL 데이터베이스에 적용됩니다. 자세한 내용은 다음을 참조하세요.
 - [메모리 내 OLTP에 대한 Transact-SQL 지원](http://msdn.microsoft.com/library/dn133180.aspx)
 - [메모리 내 OLTP에서 지원되지 않는 TRANSACT-SQL 항목](http://msdn.microsoft.com/library/dn246937.aspx)


## 추가 단계


- [기존 Azure SQL 응용 프로그램에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)을 시도합니다.


## 추가 리소스

#### 자세한 정보

- [Microsoft SQL Server와 Azure SQL 데이터베이스에 적용되는 메모리 내 OLTP에 대해 알아보기](http://msdn.microsoft.com/library/dn133186.aspx)

- [MSDN의 실시간 운영 성과 분석에 대해 알아보기](http://msdn.microsoft.com/library/dn817827.aspx)

- [일반적인 워크로드 패턴 및 마이그레이션 고려 사항에 대한 백서](http://msdn.microsoft.com/library/dn673538.aspx)는 메모리 내 OLTP가 일반적으로 상당한 성능 향상을 제공하는 워크로드 패턴을 설명합니다.

#### 응용 프로그램 설계

- [메모리 내 OLTP(메모리 내 최적화)](http://msdn.microsoft.com/library/dn133186.aspx)

- [기존 Azure SQL 응용 프로그램에서 메모리 내 OLTP 사용.](sql-database-in-memory-oltp-migration.md)

#### 도구

- 월별 최신 버전에 대한 [SQL Server Data Tools 미리 보기(SSDT)](http://msdn.microsoft.com/library/mt204009.aspx).

- [SQL 서버에 대한 RML(Replay Markup Language) 유틸리티의 설명](http://support.microsoft.com/ko-KR/kb/944837)

- 메모리 내 OLTP에 대한 [메모리 내 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md).

<!---HONumber=AcomDC_1125_2015-->