<properties
   pageTitle="SQL 데이터 웨어하우스의 PolyBase 자습서 | Microsoft Azure"
   description="PolyBase 정의 및 데이터 웨어하우징 시나리오에 대해 사용하는 방법에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
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

> [AZURE.IMPORTANT]PolyBase에서 지원하는 Azure 저장소 계정 형식은 다음과 같습니다.
> 
> + 표준 로컬 중복 저장소(표준-LRS)
> + 표준 지역 중복 저장소(표준-GRS)
> + 표준 읽기 액세스 지역 중복 저장소(표준-RAGRS)
>
> 표준 영역 중복 저장소(표준-ZRS) 및 프리미엄 로컬 중복 저장소(Premium-LRS) 계정 유형은 PolyBase에서 지원되지 않습니다. 새 Azure 저장소 계정을 만드는 경우 가격 책정 계층에서 PolyBase 지원 저장소 계정 유형을 선택해야 합니다.


## 데이터베이스 마스터 키 만들기
서버에서 사용자 데이터베이스에 연결하여 데이터베이스 마스터 키를 만듭니다. 이 키는 다음 단계에서 사용자 자격 증명 암호를 암호화하는 데 사용됩니다.

```
-- Creating master key
CREATE MASTER KEY;
```

참조 항목: [CREATE MASTER KEY (TRANSACT-SQL)][].

## 데이터베이스 범위 자격 증명 만들기
Azure blob 저장소에 액세스하려면 Azure 저장소 계정에 대한 인증 정보를 저장하는 데이터베이스 범위 자격 증명을 만들어야 합니다. 데이터 웨어하우스 데이터베이스에 연결하고 액세스하려는 각 Azure 저장소 계정에 대한 데이터베이스 범위 자격 증명을 만듭니다. ID 이름 및 Azure 저장소 계정 키를 암호로 지정합니다. ID 이름은 Azure 저장소에 대한 인증에 영향을 주지 않습니다.

데이터베이스 범위 자격 증명이 이미 있는지 확인하려면 서버 자격 증명을 보여주는 sys.credentials가 아닌 sys.database\_credentials를 사용합니다.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

참조 항목: [CREATE CREDENTIAL (Transact-SQL)][].

데이터베이스를 끌어 놓으려면 다음 구문을 사용합니다.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

참조 항목: [DROP CREDENTIAL (Transact-SQL)][].

## 외부 데이터 원본 만들기
외부 데이터 원본은 Azure blob 저장소 데이터 및 액세스 정보의 위치를 저장하는 데이터베이스 개체입니다. 액세스하려는 각 Azure 저장소 컨테이너에 대한 외부 데이터 원본을 정의해야 합니다.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

참조 항목: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

외부 데이터 원본을 끌어 놓으려는 경우의 구문은 다음과 같습니다.

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

참조 항목: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## 외부 파일 형식 만들기
외부 파일 형식은 외부 데이터의 서식을 지정하는 데이터베이스 개체입니다. 이 예제에서는 텍스트 파일의 압축되지 않은 데이터가 있으며 해당 필드는 파이프 문자('| ')로 분리됩니다.

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase는 구분 기호로 분리된 텍스트, Hive RCFILE 및 HIVE ORC 형식으로 압축된 데이터와 압축되지 않은 테이터를 작업할 수 있습니다.

참조 항목: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

외부 파일 형식을 끌어 놓으려는 경우 구문은 다음과 같습니다.

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
참조 항목: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## 외부 테이블 만들기

외부 테이블 정의는 관계형 테이블 정의와 유사합니다. 주요 차이점은 데이터 형식 및 위치입니다. 외부 테이블 정의는 SQL 데이터 웨어하우스 데이터베이스에 저장됩니다. 데이터는 데이터 원본에서 지정한 위치에 저장됩니다.

위치 옵션은 데이터 원본의 루트에서의 데이터에 대한 경로를 지정합니다. 이 예제에서 데이터는 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/'에 있습니다. 동일 테이블의 모든 파일은 Azure BLOB의 동일한 논리 폴더 아래 있어야 합니다.

필요에 따라 외부 데이터 소스에서 받은 더티 레코드를 PolyBase에서 처리하는 방법을 결정하는 거부 옵션(REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE)을 지정할 수도 있습니다.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]이 때에는 외부 테이블에 통계를 만들 수 없습니다.

참조 항목: [CREATE EXTERNAL TABLE (Transact-SQL)][].

방금 만든 개체는 SQL 데이터 웨어하우스 데이터베이스에 저장됩니다. SQL Server 데이터 도구(SSDT) 개체 탐색기에서 볼 수 있습니다.

외부 테이블을 끌어 놓으려면 다음 구문을 사용합니다.

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]외부 테이블을 삭제하는 경우 `DROP EXTERNAL TABLE`을 사용해야 합니다. `DROP TABLE`을 사용할 수 **없습니다**.

참조 항목: [DROP EXTERNAL TABLE(Transact-SQL)][].

외부 테이블이 두 `sys.tables`에 모두 표시되는지, 특히 `sys.external_tables` 카탈로그 뷰에 표시되는지 확인하는 것이 좋습니다.

## 저장소 키 회전

때때로 보안을 위해 blob 저장소 액세스 키를 변경하는 것이 좋습니다.

이 작업을 수행하는 가장 세련된 방법은 "키 회전" 라고 하는 프로세스를 따르는 것입니다. Blob 저장소 계정에 두 개의 저장소 키가 있는 것을 알 수 있습니다. 이는 다음을 전환하기 위한 것입니다.

Azure 저장소 계정 키를 회전하는 것은 간단한 3단계 프로세스입니다.

1. 두 번째 저장소 액세스 키를 기반으로 두 번째 데이터베이스 범위가 지정된 자격 증명을 만듭니다.
2. 이 새 자격 증명을 기반으로 두 번째 외부 데이터 원본을 만듭니다.
3. 새 외부 테이블을 끌어 놓고 새 외부 데이터 원본을 가리키는 외부 테이블을 만듭니다.

외부 테이블을 새 외부 데이터 원본으로 모두 마이그레이션한 후에 정리 작업을 수행할 수 있습니다.
 
1. 첫번째 외부 데이터 원본을 끌어 놓습니다.
2. 기본 저장소 액세스 키를 기반으로 첫 번째 데이터베이스 범위 자격 증명을 끌어 놓습니다.
3. Azure에 로그인하고 다음번에 사용할 준비가 된 기본 액세스 키를 다시 생성합니다.

## Azure Blob 저장소 데이터 쿼리
외부 테이블에 대한 쿼리는 관계형 테이블인 것처럼 테이블 이름을 사용합니다.


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]외부 테이블에 대한 쿼리가 실패하고 *"쿼리가 중단되었습니다. 외부 소스에서 읽는 동안 최대 거부 임계값에 도달했습니다."* 오류가 표시될 수 있습니다. 이는 외부 데이터에 *더티* 레코드가 포함되어 있음을 나타냅니다. 열의 실제 데이터 형식/개수가 외부 테이블의 열 정의와 일치하지 않거나 데이터가 지정된 외부 파일 형식에 맞지 않는 경우 데이터 레코드가 '더티'로 간주됩니다. 이 문제를 해결하려면 외부 테이블 및 외부 파일 형식 정의가 올바른지, 그리고 외부 데이터가 이러한 정의를 준수하는지 확인합니다. 외부 데이터 레코드의 하위 집합이 더티인 경우 CREATE EXTERNAL TABLE DDL의 거부 옵션을 사용하여 쿼리에 대해 해당 레코드를 거부하도록 선택할 수 있습니다.


## Azure Blob 저장소에서 데이터 로드
이 예제에서는 SQL 데이터 웨어하우스 데이터베이스로 Azure blob 저장소에서 데이터를 로드합니다.

직접 데이터를 저장하면 쿼리를 위한 데이터 전송 시간을 제거합니다. Columnstore 인덱스를 사용하여 데이터를 저장하면 최대 10배까지 분석 쿼리를 위한 쿼리 성능을 개선합니다.

이 예제에서는 CREATE TABLE AS SELECT 문을 사용하여 데이터를 로드합니다. 새 테이블은 쿼리에서 명명된 열을 상속합니다. 외부 테이블 정의에서 해당 열의 데이터 형식을 상속합니다.

CREATE TABLE AS SELECT는 INSERT...SELECT를 대체하는 고성능 Transact-SQL 문입니다. 분석 플랫폼 시스템에는 방대한 병렬 처리(MPP) 엔진을 처음 개발했으며 이제 SQL 데이터 웨어하우스에 포함되었습니다.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

[CREATE TABLE AS SELECT (Transact-SQL)][]를 참조하세요.


## PolyBase UTF-8 요구 사항 해결
현재 PolyBase에서는 UTF-8로 인코딩된 데이터 파일의 로드를 지원합니다. UTF-8에서 ASCII와 같은 문자 인코딩을 사용하기 때문에, PolyBase에서는 ASCII로 인코딩된 데이터의 로드도 지원합니다. 그러나 PolyBase에서 UTF-16/유니코드 또는 확장 ASCII 문자 등의 다른 문자 인코딩은 지원하지 않습니다. 확장 ASCII에는 독일어에서 흔히 사용되는 움라우트와 같이 악센트가 있는 문자가 포함됩니다.

이 요구 사항을 해결하는 최상의 방법은 UTF-8 인코딩으로 다시 작성하는 것입니다.

이 작업을 수행하는 방법에는 몇 가지가 있습니다. 다음은 Powershell을 사용하는 두 가지 방법입니다.

### 작은 파일에 대한 간단한 예

다음은 파일을 만드는 간단한 한 줄 짜리 Powershell 스크립트입니다.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

데이터를 간단하게 다시 인코딩할 수 있으면서도 가장 효율적입니다. 다음 예에 있는 io 스트리밍을 통해서는 같은 결과를 훨씬 빠르게 얻을 수 있습니다.

### 큰 파일에 대한 IO 스트리밍의 예

다음 코드 샘플은 더 복잡하지만 원본에서 대상으로 데이터 행을 스트리밍하기 때문에 훨씬 효율적입니다. 큰 파일에는 이 방법을 사용합니다.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

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

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/ko-KR/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/ko-KR/library/mt146379.aspx
[DROP EXTERNAL TABLE(Transact-SQL)]: https://msdn.microsoft.com/ko-KR/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (TRANSACT-SQL)]: https://msdn.microsoft.com/ko-KR/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ko-KR/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ko-KR/library/ms189450.aspx

<!---HONumber=Oct15_HO4-->