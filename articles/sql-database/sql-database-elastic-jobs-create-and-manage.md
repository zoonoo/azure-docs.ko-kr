<properties
	pageTitle="탄력적 데이터베이스 작업 만들기 및 관리"
	description="탄력적 데이터베이스 작업 만들기 및 관리 과정을 단계별로 안내합니다."
	services="sql-database"
	documentationCenter=""
	manager="jhubbard"
	authors="sidneyh"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/12/2015"
	ms.author="sidneyh"/>

# 탄력적 데이터베이스 작업 만들기 및 관리

**탄력적 데이터베이스 풀**은 많은 수의 데이터베이스를 배포할 때 예측 가능한 모델을 제공합니다. 설정 비용에서 각 데이터베이스에 최소 DTU(데이터 처리량 단위)를 설정할 수 있습니다. 이 데이터베이스에 있는 공통 개체를 관리하는 작업은 **탄력적 데이터베이스 작업**을 사용하여 가장 손쉽게 수행할 수 있습니다.. 서비스를 통해 단일 작업에서 풀에 포함된 모든 데이터베이스에 대해 T-SQL 스크립트를 실행할 수 있습니다. 예를 들어, 올바른 자격 증명을 가진 사용자만이 중요한 데이터를 볼 수 있도록 각 데이터베이스에 정책을 설정할 수 있습니다.

## 필수 조건

* Azure 구독. 무료 평가판에 대한 내용은 [무료 1개월 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* 탄력적 데이터베이스 풀 [탄력적 데이터베이스 풀 정보](sql-database-elastic-pool.md)를 참조하세요.
* 탄력적 데이터베이스 작업 서비스 구성 요소의 설치 [탄력적 데이터베이스 작업 서비스 설치](sql-database-elastic-jobs-service-installation.md)를 참조하세요.

## 작업 만들기

1. 탄력적 데이터베이스 작업 풀 블레이드에서 **작업 만들기**를 클릭합니다.
2. 작업 제어 DB(작업에 대한 메타 데이터 저장소)에 대한 데이터베이스 관리자(작업의 설치 시 생성됨)의 사용자 이름과 암호를 입력합니다.

	![작업 이름을 지정하고 코드에 입력하거나 붙여넣은 다음 실행을 클릭합니다.][1]
2. **작업 만들기** 블레이드에 작업 이름을 입력합니다.
3. 스크립트 실행이 성공하기에 충분한 권한이 있는 대상 데이터베이스에 연결하려면 사용자 이름과 암호를 입력합니다.
4. T-SQL 스크립트를 붙여넣거나 입력합니다.
5. **저장**을 클릭한 다음 **실행**을 클릭합니다.

	![작업 만들기 및 실행][5]

## 멱등원 작업 실행

데이터베이스 집합에 대해 스크립트를 실행할 때는 해당 스크립트가 멱등원인지 확인해야 합니다. 즉, 스크립트가 이전에 불완전한 상태에서 실패한 경우에도 여러 번 실행될 수 있어야 합니다. 예를 들어, 스크립트가 실패하면 성공할 때까지 작업이 자동으로 다시 시도됩니다(한도 내에서, 결국은 재시도 논리가 재시도를 중단하게 됨). 이 작업을 수행하는 방법은 "IF EXISTS" 절을 사용하여 새 개체를 만들기 전에 발견된 모든 인스턴스를 삭제하는 것입니다. 아래에 예가 나와 있습니다.

	IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
	GO
	CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

또는 새 인스턴스를 만들기 전에 "IF NOT EXISTS" 절을 사용합니다.

	IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
	BEGIN
	 CREATE TABLE TestTable(
	  TestTableId INT PRIMARY KEY IDENTITY,
	  InsertionTime DATETIME2
	 );
	END
	GO

	INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
	GO

그러면 스크립트에서 이전에 만든 테이블을 업데이트합니다.

	IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
	BEGIN

	ALTER TABLE TestTable

	ADD AdditionalInformation NVARCHAR(400);
	END
	GO

	INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
	GO


## 작업 상태 확인

작업이 시작된 후에 진행 상태를 확인할 수 있습니다.

1. 탄력적 데이터베이스 풀 페이지에서 **작업 관리**를 클릭합니다.

	!["작업 관리"를 클릭합니다.][2]

2. 작업 이름을 클릭합니다. **상태**는 "완료" 또는 "실패"일 수 있습니다. 생성 및 실행 날짜와 시간을 포함한 작업 세부 정보가 표시됩니다. 아래 목록(c)은 날짜와 시간 정보를 포함하여 풀에 있는 데이터베이스에 대한 스크립트의 진행 상태를 보여줍니다.

	![완료된 작업 확인][3]


## 실패한 작업 확인

작업이 실패할 경우 실행 로그가 생깁니다. 실패한 작업의 이름을 클릭하면 세부 정보를 볼 수 있습니다.

![실패한 작업 확인][4]


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png

 

<!---HONumber=58_postMigration-->