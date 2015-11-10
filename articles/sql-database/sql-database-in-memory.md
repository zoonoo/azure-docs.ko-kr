<properties
	pageTitle="SQL 메모리 내 시작 | Microsoft Azure"
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
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# 메모리 내 시작(미리 보기)


SQL 메모리 내 기술은 트랜잭션 및 분석 작업의 성능을 크게 향상시킵니다. 워크로드에 따라 메모리 내 OLTP은 트랜잭션 처리량이 30배까지 향상될 수 있고 메모리 내 분석은 쿼리 성능이 100배까지 향상될 수 있습니다. 실시간 분석으로 이러한 기술을 결합하여 운영 데이터를 기반으로 실시간 비즈니스 상태를 얻을 수 있습니다.

메모리 내 분석은 일반적으로 Azure DB에서 사용 가능합니다. 메모리 내 OLTP 및 실시간 운영 성과 분석은 프리미엄 Azure SQL 데이터베이스에 대한 미리 보기에 있습니다.


## 시작

트랜잭션 워크로드에 대한 메모리 내 OLTP를 시도합니다.


- [메모리 내 OLTP 샘플 설치](#install-the-in-memory-oltp-sample).
- [기존 Azure SQL 응용 프로그램에서 메모리 내 OLTP 사용.](sql-database-in-memory-oltp-migration.md)
- [메모리 내 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md).


분석 워크로드에 대한 메모리 내 분석을 시도합니다.

- [메모리 내 분석 샘플을 설치합니다.](#install-the-in-memory-analytics-sample)
- MSDN에서 [Columnstore 인덱스](https://msdn.microsoft.com/library/gg492088.aspx)에 대해 자세히 알아봅니다.


## 메모리 내 OLTP 샘플 설치

Azure Preview 포털에서 몇 번 클릭으로 AdventureWorksLT [V12] 샘플 데이터베이스를 만들 수 있습니다. 그러면 다음 단계에서는 메모리 내 OLTP 개체를 설명하기 위해 AdventureWorksLT 데이터베이스, 테이블 및 고유하게 컴파일된 저장 프로시저를 보강하는 방법을 설명합니다.


1. [Azure Preview 포털](https://portal.azure.com/)에서 V12 서버의 Premium 데이터베이스를 만듭니다. AdventureWorksLT [V12] 샘플 데이터베이스에 소스를 설정합니다.
 - 이 단계에 대한 자세한 지침은 [이 문서](sql-database-get-started.md)를 참조하세요.

2. [SQL Server Management Studio(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx) 또는 유사한 유틸리티를 사용하여 데이터베이스에 연결합니다.

3. [메모리 내 OLTP 스크립트](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_oltp_sample.sql)를 클립보드에 복사합니다.
 - 스크립트는 1단계에서 만든 AdventureWorksLT 샘플 데이터베이스에서 필요한 메모리 내 개체를 만듭니다.

4. SSMS.exe에 Transact-SQL 스크립트를 붙여 넣고 스크립트를 실행합니다.



&nbsp;

샘플은 다음 메모리에 최적화된 테이블을 포함합니다.

- SalesLT.Product\_inmem
- SalesLT.SalesOrderHeader\_inmem
- SalesLT.SalesOrderDetail\_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed

개체 탐색기 또는 카탈로그 뷰 쿼리를 통해 메모리에 최적화된 테이블을 검사합니다.

예제:


```
		SELECT name, object_id, type, type_desc, is_memory_optimized, durability, durability_desc
		FROM sys.tables
	WHERE is_memory_optimized=1;
```


마찬가지로 고유하게 컴파일된 저장 프로시저 SalesLT.usp\_InsertSalesOrder\_inmem는 개체 탐색기 또는 카탈로그 뷰 쿼리를 통해 검사할 수 있습니다.

예제:


```
		SELECT object_name(object_id), object_id, definition, uses_native_compilation
		FROM sys.sql_modules
	WHERE uses_native_compilation=1;
```


## 샘플 워크로드 실행

SalesLT.usp\_InsertSalesOrder\_inmem 및 SalesLT.usp\_InsertSalesOrder\_ondisk 저장 프로시저를 사용하여 메모리에 최적화된 테이블 대 디스크 기반 테이블의 삽입 성능을 비교합니다.

샘플 데이터베이스와 동일한 Azure 지역에 있는 응용 프로그램에서 많은 동시 클라이언트 연결을 사용하여 워크로드를 실행하는 것이 좋습니다.

### 샘플 판매 주문 삽입

다음 스크립트는 메모리에 최적화된 테이블 SalesLT.SalesOrderHeader\_inmem 및 SalesLT.SalesOrderDetail\_inmem에 다섯 개의 항목과 샘플 판매 주문을 삽입합니다.


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

EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
			@CustomerID, @BillToAddressID, @ShipToAddressID, @od;
```


### 샘플 스트레스 워크로드 실행

샘플 스트레스 워크로드를 실행하려면 샘플 데이터베이스와 동일한 지역에서 [Azure 가상 컴퓨터](https://azure.microsoft.com/documentation/services/virtual-machines/)를 만듭니다. ostress의 명령줄 도구를 사용하여 워크로드를 실행합니다. [ostress 설치 및 실행](https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)에 대한 지침은 MSDN에서 찾을 수 있습니다.

RML Cmd 프롬프트에서 다음 명령을 실행할 때 다음 명령은 메모리에 최적화된 테이블에서 100개의 동시 연결을 사용하여 5개의 품목을 가진 백만 개의 판매 주문을 삽입합니다.


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		 -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()*  
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); while (@i <
		 20) begin; EXEC SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate,
		 @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1 end"
```


<servername>를 서버 이름으로, <database>를 데이터베이스의 이름으로, <login> 및 <password>를 로그인 정보로 바꿔야 합니다.

기존의 디스크 기반 테이블과 메모리에 최적화된 테이블의 삽입 성능의 비교하려면 다음 명령을 사용하여 디스크 기반 테이블에 동일한 백만 개의 판매 주문을 삽입합니다.


```
		ostress.exe –n100 –r500 –S<servername>.database.windows.net -U<login> -P<password>
		-d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_ondisk,
		@SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() *
		8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand() *
		10000; INSERT INTO @od SELECT OrderQty, ProductID FROM
		Demo.DemoSalesOrderDetailSeed with (snapshot) WHERE OrderID= cast((rand()*60) as
		int); while (@i < 20) begin; EXEC SalesLT.usp_InsertSalesOrder_ondisk
		@SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID,
		@od; set @i += 1 end"
```


테스트는 데이터베이스와 동일한 Azure 지역에 있는 VM에서 실행되는 ostress를 사용하여 이 워크로드에 대해 디스크 기반 테이블에 비해 메모리에 최적화된 테이블이 약 9배의 성능 향상을 보여줍니다.

각 테스트를 실행한 후에 메모리 내 저장소 공간 부족을 방지하기 위해 샘플을 다시 설정해야 합니다. 데이터베이스에서 다음 T-SQL 문을 실행합니다. 백만 개의 판매 주문을 삽입하는 단일 테스트 실행하면 메모리에 최적화된 테이블에서 >500MB의 데이터가 발생합니다.


```
EXECUTE Demo.usp_DemoReset;
```


## 메모리 내 분석 샘플 설치

**우선** Azure SQL 데이터베이스를 만듭니다.

- Premium Edition을 선택합니다.(Premium은 Columnstore에 필요함)
- 이 샘플에서 데이터베이스를 만들도록 합니다.

- 간단히 하기 위해 AdventureworksLT 데이터베이스의 이름을 지정합니다.



**다음**으로 [SQL Server Management Studio(SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015년 9월 미리 보기 이상을 통해 Azure SQL 데이터베이스에 연결합니다.


- 설치 스크립트 [sql\_in memory\_analytics\_sample](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/sql_in-memory_analytics_sample.sql)를 실행합니다.


- 스크립트를 실행하여 차원 및 팩트 테이블에 필요한 스키마를 만듭니다. 스크립트는 각각 350만 개의 행이 있는 2개의 팩트 테이블을 만듭니다.


- columnstore 테이블에 있는 FactResellerSales\_CCI


- 페이지 압축된 동일한 B-트리 테이블인 FactResellerSalesXL\_PageCompressed. **참고:** 이 스크립트는 데이터를 실행하고 생성하려면 최대 15분이 걸릴 수 있습니다.


[clustered\_columnstore\_sample\_queries.sql](https://raw.githubusercontent.com/Azure/azure-sql-database-samples/master/T-SQL/In-Memory/clustered_columnstore_sample_queries.sql) 파일에서 데모 쿼리를 통해 **실행**하고 기능을 탐색합니다.

## 메모리 내 OLTP 대한 자세한 정보

[메모리 내 OLTP(메모리 내 최적화)](https://msdn.microsoft.com/library/dn133186.aspx)

[일반적인 워크로드 패턴 및 마이그레이션 고려 사항에 대한 백서](https://msdn.microsoft.com/library/dn673538.aspx)는 메모리 내 OLTP이 일반적으로 상당한 성능 향상을 제공하는 워크로드 패턴을 설명합니다.

## 미리보기 고려 사항

메모리 내 OLTP는 Premium Edition 데이터베이스에**만** 지원됩니다.

메모리 내 OLTP는 새로 만들어진 데이터베이스에서 지원됩니다. 복사 또는 복원 기능을 통해 기존 데이터베이스를 기반으로 생성된 데이터베이스에서 지원되지 않습니다. 그러나 새 데이터베이스를 만든 후에 메모리 내 OLTP의 전체 기능을 유지하면서 이 데이터베이스를 복사 또는 복원할 수 있습니다.

지정된 데이터베이스에서 메모리 내 OLTP 지원되는지 여부를 확인하려면 다음 쿼리를 실행합니다.


```
SELECT DATABASEPROPERTYEX(DB_NAME(), 'IsXTPSupported');
```


쿼리가 **1**을 반환하는 경우 메모리 내 OLTP은 이 데이터베이스 뿐만 아니라 이 데이터베이스를 기반으로 생성된 어떤 데이터베이스 복사 및 데이터베이스 복원에서도 지원됩니다.

데이터베이스가 다음과 같은 종류의 개체 또는 형식을 포함하는 경우 데이터베이스의 서비스 계층을 기본 또는 표준으로 변경하는 작업은 지원되지 않습니다. 데이터베이스를 다운그레이드하려면 먼저 개체를 삭제합니다.

- 메모리 최적화된 테이블
- 메모리에 최적화된 테이블 형식
- 탄력적인 풀에서 데이터베이스와 메모리 내 OLTP를 사용하는 고유하게 컴파일된 모듈은 지원되지 않습니다.

SQL 데이터 웨어하우스와 함께 메모리 내 OLTP은 사용은 지원되지 않습니다.

쿼리 저장소는 내부에서 고유하게 컴파일된 모듈을 쿼리하지 않습니다.

일부 Transact-SQL 기능은 메모리 내 OLTP를 사용하여 지원되지 않습니다. 자세한 내용은 [메모리 내 OLTP에 대한 Transact-SQL 지원](https://msdn.microsoft.com/library/dn133180.aspx)을 참조하세요.

## 지원되는 도구

[SQL Server Management Studio(SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 2015년 9월 미리 보기 이상.

[SQL Server 데이터 도구 미리보기(SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 2015년 9월 미리 보기 이상.

## 추가 리소스

[MSDN의 메모리 내 OLTP에 대해 알아보기](https://msdn.microsoft.com/library/dn133186.aspx)

[MSDN의 메모리 내 분석(Columnstore)에 대해 알아보기](https://msdn.microsoft.com/library/gg492088.aspx)

[MSDN의 실시간 운영 성과 분석에 대해 알아보기](https://msdn.microsoft.com/library/dn817827.aspx)

[일반적인 워크로드 패턴 및 마이그레이션 고려 사항에 대한 백서](https://msdn.microsoft.com/library/dn673538.aspx)는 메모리 내 OLTP이 일반적으로 상당한 성능 향상을 제공하는 워크로드 패턴을 설명합니다.

## 다음 단계

[기존 Azure SQL 응용 프로그램에서 메모리 내 OLTP 사용](sql-database-in-memory-oltp-migration.md)을 시도합니다.

메모리 내 OLTP에 대한 [메모리 내 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md)입니다.

<!---HONumber=Nov15_HO2-->