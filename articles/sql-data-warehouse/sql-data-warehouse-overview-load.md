   <properties
   pageTitle="SQL 데이터 웨어하우스에 데이터 로드 | Microsoft Azure"
   description="SQL 데이터 웨어하우스에서 데이터 로드를 위한 일반적인 시나리오에 대해 알아봅니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="lodipalm;barbkess"/>

# SQL 데이터 웨어하우스에 데이터 로드
SQL 데이터 웨어하우스는 다음을 포함한 데이터 로드의 다양한 옵션을 제공합니다.

- PolyBase
- Azure 데이터 팩터리
- BCP 명령줄 유틸리티
- SQL Server 통합 서비스(SSIS)
- 타사 데이터 로드 도구

위의 모든 방법을 SQL 데이터 웨어하우스에 사용할 수 있지만 Azure Blob 저장소에서 로드를 투명하게 병렬화할 수 있는 PolyBase가 데이터를 로드하는 가장 빠른 도구입니다. [PolyBase를 사용하여 로드][]하는 방법을 자세히 알아보세요. 또한 대부분의 사용자는 온-프레미스 소스의 초기 로드를 수백 기가바이트에서 수십 테라바이트 정도로 예상하기 때문에 아래 섹션에서는 초기 데이터 로드에 대한 몇 가지 지침을 제공합니다.

## SQL Server에서 SQL 데이터 웨어하우스로 초기 로드. 
온-프레미스 SQL Server 인스턴스에서 SQL 데이터 웨어하우스로 로드하는 경우 다음 단계를 권장합니다.

1. 플랫 파일로 SQL Server 데이터 **BCP** 
2. **AZCopy** 또는 **가져오기/내보내기**(큰 데이터 집합의 경우)를 사용하여 Azure로 파일 이동
3. 저장소 계정의 파일을 읽을 PolyBase 구성
4. 새 테이블을 만들고 **PolyBase**를 사용하여 데이터 로드

다음 섹션에서는 각 단계에 대해 자세히 살펴보고, 프로세스의 예를 제공합니다.

> [AZURE.NOTE]SQL Server와 같은 시스템에서 데이터를 이동하기 전에 설명서의 [스키마 마이그레이션][] 및 [코드 마이그레이션][] 문서를 검토하는 것이 좋습니다.

## BCP를 사용하여 파일 내보내기

Azure로 파일 이동을 준비하기 위해, 플랫 파일로 내보내야 합니다. BCP 명령줄 유틸리티를 사용하는 것이 가장 좋은 방법입니다. 아직 유틸리티가 없다면 [SQL Server용 Microsoft 명령줄 유틸리티][]를 사용하여 다운로드할 수 있습니다. 샘플 BCP 명령은 다음과 같을 수 있습니다.

```
bcp "<Directory><File>" -c -T -S <Server Name> -d <Database Name>
```

이 명령은 쿼리로 결과가 나오고, 이쿼리를 선택한 디렉터리 안에 있는 파일로 내보냅니다. 개별 테이블에 대해 여러 BCP 명령을 실행하여 프로세스를 병렬화할 수 있습니다. 이 방법은 서버의 코어 당 BCP 프로세스를 증가시킬 수 있고, 이 정보는 사용자 환경에서 어떤 작업이 제일 적합한지 다른 구성에서 작은 작업을 시도함으로써 확인할 수 있습니다.

PolyBase는 아직 UTF-16을 지원하지 않고, PolyBase를 사용하여 로드하기 때문에, 모든 파일은 UTF-8에 있어야합니다. BCP 명령에 있는 ‘-c’ 플래그를 포함한 다음 코드를 사용하여 간단하게 수행하거나, 다음 코드를 사용하여 플랫 파일을 UTF-16에서 UTF-8로 변환시킬 수 있습니다.

```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```
 
성공적으로 파일에 데이터를 내보냈다면, Azure로 이동시킬 차례입니다. 다음 섹션에서 설명하는 AZCopy 또는 가져오기/내보내기를 사용하여 수행할 수 있습니다.

## AZCopy 또는 가져오기/내보내기를 사용하여 Azure에 로드
5-10테라바이트 범위 이상의 데이터를 이동하는 경우 이 작업을 수행하기 위해 디스크 전달 서비스인 [가져오기/내보내기][]를 사용하는 것이 좋습니다. 그러나, 단일 숫자 TB 범위 데이터를 이동하려면 AZCopy를 포함한 공용 인터넷을 사용하는 편이 더욱 편리합니다. Express Route를 사용하여 이 프로세스의 속도를 올리거나 확장할 수 있습니다.

다음 단계에서는 AZCopy를 사용하여 온-프레미스 서버에서 Azure 저장소 계정으로 이동하는 방법에 대해 자세히 설명합니다. 동일한 지역에 Azure 저장소 계정이 없는 경우 [Azure 저장소 설명서][]에 따라 새로운 계정을 만들 수 있습니다. 다른 지역의 저장소 계정에서 로드할 수 있지만, 이 경우 성능이 최적화되지 않습니다.

> [AZURE.NOTE]이 설명서는 AZCopy 명령줄 유틸리티를 설치했으며 PowerShell을 사용하여 실행 가능한 상태를 가정합니다. 이러한 상태가 아닐 경우, [AZCopy 설치 지침][]을 따르세요.

이제 BCP를 사용하여 생성한 파일 집합을 사용하여, AZCopy를 Azure Powershell에서 또는 powershell 스크립트를 실행하여 간단하게 실행할 수 있습니다. 더 높은 수준에서, AZCopy 실행을 요구하는 프롬프트는 다음 형식을 따릅니다.

```
AZCopy /Source:<File Location> /Dest:<Storage Container Location> /destkey:<Storage Key> /Pattern:<File Name> /NC:256
```

기본 사항에 추가하여, AZCopy를 사용한 로드에 대해 다음의 가장 적합한 실습을 권장합니다.


+ **동시 연결**: 대상에 대한 많은 동시 연결을 여는 /NC 매개 변수를 설정하면 한 번에 실행되는 AZCopy 작업 수가 증가하는 것은 물론 AZCopy 작업 자체를 더욱 병렬화할 수 있습니다. 매개변수의 최대 설정은 512이고, 256이 데이터 전송에 최적화되어있지만, 사용자 구성의 최적값을 찾기 위해 많은 값을 테스트하는것을 권장합니다.

+ **Express 경로**: 위에서 설명한 대로, Express 경로를 사용하도록 설정하면 이 프로세스의 속도를 증가시킬 수 있습니다. Express 경로 및 구성 단계에 대한 개요는 [Express 경로 설명서][]에서 확인할 수 있습니다.

+ **폴더 구조**: PolyBase를 사용하여 더 쉽게 전송하려면 각 테이블을 해당 폴더에 매핑합니다. 이것은 나중에 PolyBase를 사용해 로드할 때 해당 단계를 최소화하고 간소화시킵니다. 테이블이 여러 파일 또는 폴더 내의 하위 디렉터리에 분할된 경우, 아무런 영향도 주지 않습니다.
	 

## PolyBase 구성 

이제 데이터가 Azure 저장소 BLOB에 있으므로, PolyBase를 사용하여 SQL 데이터 웨어하우스 인스턴스로 가져올 것입니다. 아래 단계는 오직 구성에만 해당되고, 대부분은 SQL 데이터 웨어하우스 인스턴스, 사용자 또는 저장소 계정마다 한번씩만 완료하면 됩니다. 이러한 단계는 [PolyBase를 사용하여 로드][] 설명서에도 자세히 나와 있습니다.

1. **데이터베이스 마스터 키 생성** 이 작업은 데이터베이스마다 한 번씩만 완료할 수 있습니다. 

2. **데이터베이스 범위 자격 증명 생성** 이 작업은 새 자격 증명/사용자 만들기의 경우에만 필요하고, 그 외의 경우 전에 만들었던 자격 증명을 사용할 수 있습니다.

3. **외부 파일 형식 생성** 외부 파일 형식은 재사용이 가능하지만, 새로운 유형의 파일을 업로드 하는 경우에는 꼭 새로 만들어야 합니다.

4. **외부 데이터 원본 생성** 저장소 계정을 가리킬 때, 외부 데이터 원본은 동일 컨테이너에서 로드할 때 사용됩니다. ‘LOCATION’ 매개 변수에 대하여, 서식의 위치를 사용합니다: 'wasbs://mycontainer@ test.blob.core.windows.net/path’.

```
-- Creating master key
CREATE MASTER KEY;

-- Creating a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL <Credential Name> 
WITH 
    IDENTITY = '<User Name>'
,   Secret = '<Azure Storage Key>'
;

-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(
    FORMAT_TYPE = DELIMITEDTEXT 
,   FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|' 
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
);

--Creating an external data source
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH 
(
    TYPE = HADOOP 
,   LOCATION ='wasbs://<Container>@<Blob Path>'
,   CREDENTIAL = <Credential Name>
)
;
```

이제 저장소 계정이 제대로 구성되었으므로, 데이터를 SQL 데이터 웨어하우스로 로드할 수 있습니다.

## PolyBase 사용하여 데이터 로드 
PolyBase 구성 후, 저장소에 있는 데이터를 가리키는 외부 테이블을 간단히 만들어서 데이터를 SQL 데이터 웨어하우스로 직접 로드할 수 있고, SQL 데이터 웨어하우스 내의 새 테이블에 데이터를 매핑합니다. 아래 두 간단한 명령을 사용하여 수행할 수 있습니다.

1. ‘CREATE EXTERNAL TABLE’ 명령을 사용하여 데이터의 구조를 정의합니다. 데이터의 상태를 빠르고 효율적으로 캡쳐할 수 있는지 확인하기 위해, SSMS에서 SQL Server 테이블을 스크립팅하고, 그 후에 외부 테이블의 차이점의 원인을 직접 조정하는 것을 권장합니다. Azure에서 외부 테이블을 생성하면, 데이터를 업데이트하거나 추가 데이터를 추가할 경우에도 계속 같은 위치를 가리킵니다.  

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE <External Table Name> 
(
    <Column name>, <Column type>, <NULL/NOT NULL>
)
WITH 
(   LOCATION='<Folder Path>'
,   DATA_SOURCE = <Data Source>
,   FILE_FORMAT = <File Format>      
);
```

2. ‘CREATE TABE...AS SELECT’ 문을 사용하여 데이터를 로드합니다. 

```
CREATE TABLE <Table Name> 
WITH 
(
	CLUSTERED COLUMNSTORE INDEX
)
AS 
SELECT  * 
FROM    <External Table Name>
;
```

더욱 세부적인 SELECT 문을 사용하면 테이블의 행 하위 섹션을 로드할 수 있습니다. 그러나 이번에 PolyBase가 저장소 계정에 추가적인 계산을 푸시하지 않는 것처럼, SELECT 문을 사용하여 하위 섹션을 로드하는 경우는 전체 데이터 집합을 로드하는 것보다 빠르지 않습니다.

`CREATE TABLE...AS SELECT` 문 외에도, ‘INSERT...INTO’ 문을 사용하여 외부 테이블의 데이터를 기존 테이블로 로드할 수 있습니다.

##  새로 로드한 데이터에 대한 통계 만들기 

Azure SQL 데이터 웨어하우스는 자동 만들기 또는 통계 자동 업데이트를 아직 지원하지 않습니다. 쿼리에서 최상의 성능을 얻으려면, 데이터를 처음 로드하거나 데이터에 상당한 변화가 발생한 후에 모든 테이블의 모든 열에서 통계가 만들어지는 것이 중요합니다. 통계에 대한 자세한 설명은 개발 항목 그룹의 [통계][] 항목을 참조하세요. 다음은 이 예제에 로드한 테이블에 대한 통계를 만드는 방법을 간략히 보여주는 예입니다.


```
create statistics [<name>] on [<Table Name>] ([<Column Name>]);
create statistics [<another name>] on [<Table Name>] ([<Another Column Name>]);
```

## 다음 단계
더 많은 개발 팁은 [개발 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[PolyBase를 사용하여 로드]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[개발 개요]: sql-data-warehouse-overview-develop.md
[스키마 마이그레이션]: sql-data-warehouse-migrate-schema.md
[코드 마이그레이션]: sql-data-warehouse-migrate-code.md
[통계]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141237.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

<!--Other Web references-->
[AZCopy 설치 지침]: https://azure.microsoft.com/ko-KR/documentation/articles/storage-use-azcopy/
[SQL Server용 Microsoft 명령줄 유틸리티]: http://www.microsoft.com/ko-KR/download/details.aspx?id=36433
[가져오기/내보내기]: https://azure.microsoft.com/ko-KR/documentation/articles/storage-import-export-service/
[Azure 저장소 설명서]: https://azure.microsoft.com/ko-KR/documentation/articles/storage-create-storage-account/
[Express 경로 설명서]: http://azure.microsoft.com/documentation/services/expressroute/

<!---HONumber=Nov15_HO2-->