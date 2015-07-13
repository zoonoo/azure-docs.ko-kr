<properties
   pageTitle="SQL 데이터 웨어하우스의 PolyBase 자습서 | Microsoft Azure"
   description="PolyBase 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase를 사용하여 데이터 로드
PolyBase 기술로 TRANSACT-SQL 명령을 사용하여 여러 원본에서 데이터를 쿼리하고 조인할 수 있습니다.

PolyBase를 사용하여 Azure blob 저장소에 저장된 데이터를 쿼리하고 다음 단계에서 SQL 데이터 웨어하우스 데이터베이스에 로드할 수 있습니다.

- 데이터베이스 마스터 키 및 자격 증명을 만듭니다.
- PolyBase 개체 만들기: 외부 데이터 원본, 외부 파일 형식 및 외부 테이블입니다. 
- Azure blob 저장소에 저장된 데이터를 쿼리합니다.
- Azure blob 저장소에서 SQL 데이터 웨어하우스로 데이터를 로드합니다.


## 필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

- Azure 저장소 계정
- 구분된 텍스트 파일로 Azure blob 저장소에 저장된 데이터

먼저, PolyBase가 연결하여 Azure blob 저장소에서 데이터를 쿼리하는 데 필요한 개체를 만듭니다.

## 데이터베이스 마스터 키 만들기
서버에서 마스터 데이터베이스에 연결하여 데이터베이스 마스터 키를 만듭니다. 이 키는 다음 단계에서 사용자 자격 증명 암호를 암호화하는 데 사용됩니다.

```
-- Creating master key
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
```

참조 항목: [CREATE MASTER KEY (TRANSACT-SQL)][].

## 데이터베이스 범위 자격 증명 만들기
Azure blob 저장소에 액세스하려면 Azure 저장소 계정에 대한 인증 정보를 저장하는 데이터베이스 범위 자격 증명을 만들어야 합니다. 데이터 웨어하우스 데이터베이스에 연결하고 액세스하려는 각 Azure 저장소 계정에 대한 데이터베이스 범위 자격 증명을 만듭니다. ID 이름 및 Azure 저장소 계정 키를 암호로 지정합니다. ID 이름은 Azure 저장소에 대한 인증에 영향을 주지 않습니다.

```
-- Creating credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

참조 항목: [CREATE CREDENTIAL (Transact-SQL)][].

Azure 저장소 계정 키를 회전하면 자격 증명을 삭제하고 새 키를 암호로 사용하여 다시 만들어야 합니다.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

참조 항목: [DROP CREDENTIAL (Transact-SQL)][].


## 외부 데이터 원본 만들기
외부 데이터 원본은 Azure blob 저장소 데이터 및 액세스 정보의 위치를 저장하는 데이터베이스 개체입니다. 액세스하려는 각 Azure 저장소 컨테이너에 대한 외부 데이터 원본을 정의해야 합니다.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

참조 항목: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## 외부 파일 형식 만들기
외부 파일 형식은 외부 데이터의 서식을 지정하는 데이터베이스 개체입니다. 이 예제에서는 텍스트 파일의 압축되지 않은 데이터가 있으며 해당 필드는 파이프 문자('| ')로 분리됩니다.

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

PolyBase는 구분 기호로 분리된 텍스트, Hive RCFILE 및 HIVE ORC 형식으로 압축된 데이터와 압축되지 않은 테이터를 작업할 수 있습니다.

참조 항목: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## 외부 테이블 만들기

외부 테이블 정의는 관계형 테이블 정의와 유사합니다. 주요 차이점은 데이터 형식 및 위치입니다. 외부 테이블 정의는 SQL 데이터 웨어하우스 데이터베이스에 저장됩니다. 데이터는 데이터 원본에서 지정한 위치에 저장됩니다.

위치 옵션은 데이터 원본의 루트에서의 데이터에 대한 경로를 지정합니다. 이 예제에서 데이터는 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'에 있습니다.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]이 때에는 외부 테이블에 통계를 만들 수 없습니다.

동일 테이블의 모든 파일은 Azure BLOB의 동일한 논리 폴더 아래 있어야 합니다. 모범 사례로, SQL 데이터 웨어하우스로 병렬 처리가 가능한 경우 1GB 미만의 파일로 Azure 저장소 데이터를 구분합니다.

참조 항목: [CREATE EXTERNAL TABLE (Transact-SQL)][].

방금 만든 개체는 SQL 데이터 웨어하우스 데이터베이스에 저장됩니다. SQL Server 데이터 도구(SSDT) 개체 탐색기에서 볼 수 있습니다.


## Azure Blob 저장소 데이터 쿼리
외부 테이블에 대한 쿼리는 관계형 테이블인 것처럼 테이블 이름을 사용합니다.

Azure 저장소 blob에 저장된 자동차 센서 데이터와 함께 SQL 데이터 웨어하우스에 저장된 보험 고객 데이터를 조인하는 임시 쿼리입니다. 결과는 다른 드라이브보다 빠른 드라이브를 표시합니다.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Azure Blob 저장소에서 데이터 로드
이 예제에서는 SQL 데이터 웨어하우스 데이터베이스로 Azure blob 저장소에서 데이터를 로드합니다.

직접 데이터를 저장하면 쿼리를 위한 데이터 전송 시간을 제거합니다. Columnstore 인덱스를 사용하여 데이터를 저장하면 최대 10배까지 분석 쿼리를 위한 쿼리 성능을 개선합니다.

이 예제에서는 CREATE TABLE AS SELECT 문을 사용하여 데이터를 로드합니다. 새 테이블은 쿼리에서 명명된 열을 상속합니다. 외부 테이블 정의에서 해당 열의 데이터 형식을 상속합니다.

CREATE TABLE AS SELECT는 INSERT...SELECT를 대체하는 고성능 Transact-SQL 문입니다. 분석 플랫폼 시스템에는 방대한 병렬 처리(MPP) 엔진을 처음 개발했으며 이제 SQL 데이터 웨어하우스에 포함되었습니다.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

[CREATE TABLE AS SELECT (Transact-SQL)][]를 참조하세요.


## 제한 사항
PolyBase로 로딩하면 UTF-8 인코딩 스타일만 지원합니다. 다른 인코딩 스타일의 경우, UTF-16은 bcp 유틸리티, SSIS 또는 Azure 데이터 팩터리를 사용하여 데이터를 SQL 데이터 웨어하우스 데이터베이스로 로드합니다.

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[개발 개요]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (TRANSACT-SQL)]: https://msdn.microsoft.com/ko-kr/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ko-kr/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ko-kr/library/ms189450.aspx

<!---HONumber=July15_HO1-->