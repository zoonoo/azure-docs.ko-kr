<properties
   pageTitle="PolyBase 자습서를 사용하여 데이터 로드 | Microsoft Azure"
   description="PolyBase를 사용하여 데이터를 SQL 데이터 웨어하우스에 로드하는 방법에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/20/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase를 사용하여 데이터 로드
이 자습서는 PolyBase를 사용하여 Azure SQL 데이터 웨어하우스로 데이터를 로드하는 방법을 보여 줍니다.


## 필수 조건
이 자습서를 단계별로 실행하려면 다음을 수행해야 합니다.

- SQL 데이터 웨어하우스 데이터베이스
- Azure 저장소 계정


## 1단계: 원본 데이터 파일 만들기
메모장을 열고 새 파일로 다음 데이터 줄을 복사합니다. 로컬 임시 디렉터리, C:\\Temp\\DimDate2.txt로 저장합니다.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```


## 2단계: Azure Blob 저장소에 데이터 마이그레이션

- [최신 버전의 AzCopy][]를 다운로드합니다.
- 명령 창을 열고 AzCopy.exe가 있는 컴퓨터의 AzCopy 설치 디렉터리로 이동합니다. 기본적으로 설치 디렉터리는 64비트 Windows 컴퓨터에서 %ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\에 있습니다.
- 다음 명령을 실행하여 파일을 업로드합니다. /DestKey에 대한 Azure 저장소 계정 키를 지정합니다.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

AzCopy에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티 시작하기][]를 참조하세요.


## 3단계: 외부 테이블 만들기

다음으로 Azure Blob 저장소의 데이터를 참조하도록 SQL 데이터 웨어하우스에 외부 테이블을 만들어야 합니다. 외부 테이블을 만들려면 다음 단계를 사용합니다.

- [마스터 키 만들기][]\: 데이터베이스 범위 자격 증명의 암호를 암호화합니다.
- [데이터베이스 범위 자격 증명 만들기]\: Azure 저장소 계정에 대한 인증 정보를 지정합니다.
- [외부 데이터 원본 만들기]\: Azure Blob 저장소의 위치를 지정합니다.
- [외부 파일 형식 만들기]\: 데이터의 레이아웃을 지정합니다.
- [외부 테이블 만들기]\: Azure 저장소 데이터를 참조합니다.



```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## 4단계: SQL 데이터 웨어하우스에 데이터 로드

- 새 테이블로 데이터를 로드하려면 CREATE TABLE AS SELECT 문을 실행합니다. 새 테이블은 쿼리에서 명명된 열을 상속합니다. 외부 테이블 정의에서 해당 열의 데이터 형식을 상속합니다. 
- 기존 테이블에 데이터를 로드하려면 INSERT...SELECT 문을 사용합니다.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
		DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```
[CREATE TABLE AS SELECT (Transact-SQL)][]를 참조하세요.


PolyBase에 대한 자세한 내용은 [SQL 데이터 웨어하우스의 PolyBase 자습서][]를 참조하세요.


<!--Article references-->
[SQL 데이터 웨어하우스의 PolyBase 자습서]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[최신 버전의 AzCopy]: http://aka.ms/downloadazcopy
[AzCopy 명령줄 유틸리티 시작하기]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[외부 데이터 원본 만들기]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[외부 파일 형식 만들기]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[외부 테이블 만들기]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[마스터 키 만들기]: https://msdn.microsoft.com/ko-KR/library/ms174382.aspx
[데이터베이스 범위 자격 증명 만들기]: https://msdn.microsoft.com/ko-KR/library/mt270260.aspx
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx

<!---HONumber=Oct15_HO4-->