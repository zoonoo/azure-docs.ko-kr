---
title: 다른 원본에서 메트릭 Advisor로 데이터 피드를 추가 하는 방법
titleSuffix: Azure Cognitive Services
description: 메트릭 관리자에 다른 데이터 피드 추가
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: aahi
ms.openlocfilehash: 11b75bcadc6292c17ef7e1e0f482d0c53bd9f8f5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971951"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>다른 데이터 원본의 데이터 피드를 메트릭 Advisor에 추가

이 문서를 사용 하 여 다양 한 유형의 데이터 원본을 메트릭 관리자에 연결 하기 위한 설정 및 요구 사항을 찾을 수 있습니다. 메트릭 Advisor를 사용 하 여 데이터를 사용 하는 주요 개념에 대 한 자세한 내용은 [데이터](how-tos/onboard-your-data.md) 를 등록 하는 방법을 참조 하세요. 

## <a name="supported-authentication-types"></a>지원되는 인증 형식

| 인증 형식 | Description |
| ---------------------|-------------|
|**기본** | 데이터 원본에 액세스 하기 위한 기본 매개 변수를 제공할 수 있어야 합니다. 예를 들어 연결 문자열 또는 키입니다. 데이터 피드 관리자는 이러한 자격 증명을 볼 수 있습니다. |
| **AzureManagedIdentity** | Azure 리소스에 대 한 [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 는 Azure Active Directory의 기능입니다. Azure AD에서 자동으로 관리 되는 id를 Azure 서비스에 제공 합니다. Id를 사용 하 여 Azure AD 인증을 지 원하는 모든 서비스에 인증할 수 있습니다.|
| **AzureSQLConnectionString**| 메트릭 관리자에서 AzureSQL 연결 문자열을 **자격 증명 엔터티로** 저장 하 고 메트릭 데이터를 온 보 딩 할 때마다 직접 사용 합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만, 권한 있는 뷰어가 자격 증명에 대 한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다. |
| **DataLakeGen2SharedKey**| 메트릭 관리자에서 data lake 계정 키를 **자격 증명 엔터티로** 저장 하 고 메트릭 데이터를 온 보 딩 할 때마다 직접 사용 합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만, 권한 있는 뷰어가 자격 증명 정보를 몰라도 데이터 피드를 만들 수 있습니다.|
| **서비스 주체**| 메트릭 관리자에서 서비스 주체를 **자격 증명 엔터티로** 저장 하 고 메트릭 데이터를 온 보 딩 할 때마다 직접 사용 합니다. 자격 증명 엔터티의 관리자만 자격 증명을 볼 수 있지만 자격 증명 정보를 알 필요 없이 권한 있는 뷰어가 데이터 피드를 만들 수 있습니다.|
| **Key vault의 서비스 사용자**|메트릭 관리자에서 키 자격 증명 모음에 서비스 주체를 저장 하 고 메트릭 **데이터를 다시** 등록할 때마다 직접 사용 합니다. 자격 증명 **엔터티의** 관리자만 자격 증명을 볼 수 있지만 자세한 자격 증명을 몰라도 데이터 피드를 만들 수 있습니다. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>지원 되는 데이터 원본 및 해당 인증 유형


| 데이터 원본 | 인증 유형 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>Microsoft.managedidentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer(Kusto)**](#kusto) | Basic<br>Microsoft.managedidentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>서비스 주체<br>Key vault의 서비스 사용자<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Basic<br>Microsoft.managedidentity<br>서비스 주체<br>Key vault의 서비스 사용자<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**Http 요청**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

**자격 증명 엔터티** 를 만들어 데이터 원본에 인증 하는 데 사용 합니다. 다음 섹션에서는 *기본* 인증에에 필요한 매개 변수를 지정 합니다. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **응용 프로그램 ID**: Application Insights API를 사용 하는 경우이 응용 프로그램을 식별 하는 데 사용 됩니다. 애플리케이션 ID를 가져오려면 다음을 수행합니다.

    1. Application Insights 리소스에서 API 액세스를 클릭합니다.

    2. 메트릭 Advisor의 **응용** 프로그램 id 필드에 생성 된 응용 프로그램 id를 복사 합니다. 
    
    자세한 내용은 [Azure Bot Service 설명서](https://docs.microsoft.com/azure/bot-service/bot-service-resources-app-insights-keys#application-id) 를 참조 하세요.

* **Api 키**: api 키는 브라우저 외부의 응용 프로그램에서이 리소스에 액세스 하는 데 사용 됩니다. API 키를 가져오려면 다음을 수행합니다.

    1. Application Insights 리소스에서 API 액세스를 클릭합니다.

    2. API 키 만들기를 클릭합니다.

    3. 간단한 설명을 입력 하 고, 원격 분석 읽기 옵션을 선택 하 고, 키 생성 단추를 클릭 합니다.

    4. API 키를 메트릭 관리자의 **api 키** 필드에 복사 합니다.

* **쿼리**: Azure 애플리케이션 Insights 로그는 Azure 데이터 탐색기에 빌드되고 Azure Monitor 로그 쿼리는 동일한 Kusto 쿼리 언어의 버전을 사용 합니다. [Kusto 쿼리 언어 설명서](https://docs.microsoft.com/azure/data-explorer/kusto/query/) 는 언어에 대 한 모든 세부 정보를 포함 하며 Application Insights에 대 한 쿼리를 작성 하는 데 필요한 기본 리소스입니다. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **연결 문자열**:이 문자열을 검색 하는 방법에 대 한 자세한 내용은 Azure Blob Storage [연결 문자열](https://docs.microsoft.com/azure/storage/common/storage-configure-connection-string#view-and-copy-a-connection-string) 문서를 참조 하세요.

* **컨테이너**: 메트릭 관리자는 단일 컨테이너 아래에 blob 파일 (타임 스탬프 당 하나의 blob)로 저장 된 시계열 데이터를 예상 합니다. 컨테이너 이름 필드입니다.

* **Blob 템플릿**: blob 파일 이름의 템플릿입니다. 예: `/%Y/%m/X_%Y-%m-%d-%h-%M.json` 지원 되는 매개 변수는 다음과 같습니다.
  * `%Y` 는로 서식이 지정 된 연도입니다. `yyyy`
  * `%m` 는로 형식이 지정 된 월입니다. `MM`
  * `%d` 날짜 형식이 `dd`
  * `%h` 는로 서식이 지정 된 시간입니다. `HH`
  * `%M` 는로 서식이 지정 된 분입니다. `mm`

* **Json 형식 버전**: json 파일의 데이터 스키마를 정의 합니다. 현재 메트릭 관리자는 다음 두 가지 버전을 지원 합니다.
  
  * v1 (기본값)

      메트릭 *이름과* *값* 만 허용 됩니다. 예를 들면 다음과 같습니다.
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      메트릭 *차원과* *타임 스탬프로* 도 허용 됩니다. 예를 들면 다음과 같습니다.
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

JSON 파일당 타임 스탬프는 하나만 허용 됩니다. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **연결 문자열**: Azure Cosmos DB 액세스 하는 데 사용할 연결 문자열입니다. 이는 Cosmos DB 리소스의 **키**에서 찾을 수 있습니다. 
* **데이터베이스**: 쿼리할 데이터베이스입니다. 이는 **찾아보기** 페이지의 **컨테이너** 섹션에서 찾을 수 있습니다.
* **컬렉션 id**: 쿼리할 컬렉션 id입니다. 이는 **찾아보기** 페이지의 **컨테이너** 섹션에서 찾을 수 있습니다.
* **Sql 쿼리**: 다차원 시계열 데이터에 대 한 데이터를 가져오고 작성 하는 sql 쿼리입니다. 쿼리에서 및 변수를 사용할 수 있습니다 `@StartTime` `@EndTime` . 형식을 지정 해야 합니다. `yyyy-MM-dd HH:mm:ss`

    예제 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12에서 데이터 조각에 대 한 샘플 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer(Kusto)</span>

* **연결 문자열**: 메트릭 관리자는 azure AD 응용 프로그램 인증을 사용 하 여 azure 데이터 탐색기 (Kusto)에 액세스할 수 있도록 지원 합니다. Azure AD 응용 프로그램을 만들고 등록 한 다음 Azure 데이터 탐색기 데이터베이스에 액세스 하도록 권한을 부여 해야 합니다. 연결 문자열을 가져오려면 [Azure 데이터 탐색기](https://docs.microsoft.com/azure/data-explorer/provision-azure-ad-app) 설명서를 참조 하세요.

* **쿼리**: 다중 차원 시계열 데이터로 데이터를 가져오고 작성 하려면 [Kusto 쿼리 언어](https://docs.microsoft.com/azure/data-explorer/kusto/query) 를 참조 하세요. 쿼리에서 및 변수를 사용할 수 있습니다 `@StartTime` `@EndTime` . 형식을 지정 해야 합니다. `yyyy-MM-dd HH:mm:ss`

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **계정 이름**: Azure Data Lake Storage Gen2의 계정 이름입니다. 이는 **액세스 키**의 Azure Data Lake Storage Gen2 (Azure Storage 계정) 리소스에서 찾을 수 있습니다.

* **계정 키**: Azure Data Lake Storage Gen2에 액세스할 계정 이름을 지정 하십시오. 이는 **액세스 키** 설정의 Azure Data Lake Storage Gen2 (Azure Storage 계정) 리소스에서 찾을 수 있습니다.

* **파일 시스템 이름 (컨테이너)**: 메트릭 관리자는 단일 컨테이너 아래에 blob 파일로 저장 된 시계열 데이터 (타임 스탬프 당 하나의 blob)를 필요로 합니다. 컨테이너 이름 필드입니다. Azure storage 계정 (Azure Data Lake Storage Gen2) 인스턴스에서 찾을 수 있으며 ' Blob Service ' 섹션에서 ' 컨테이너 '를 클릭 합니다.

* **디렉터리 템플릿**: Blob 파일의 디렉터리 템플릿입니다. 예: */%Y/%m/%d*. 지원 되는 매개 변수는 다음과 같습니다.
  * `%Y` 는로 서식이 지정 된 연도입니다. `yyyy`
  * `%m` 는로 형식이 지정 된 월입니다. `MM`
  * `%d` 날짜 형식이 `dd`
  * `%h` 는로 서식이 지정 된 시간입니다. `HH`
  * `%M` 는로 서식이 지정 된 분입니다. `mm`

* **파일 템플릿**: Blob 파일의 파일 템플릿입니다. 예: *X_% Y-% m-% d-% h-% M.json*. 지원 되는 매개 변수는 다음과 같습니다.
  * `%Y` 는로 서식이 지정 된 연도입니다. `yyyy`
  * `%m` 는로 형식이 지정 된 월입니다. `MM`
  * `%d` 날짜 형식이 `dd`
  * `%h` 는로 서식이 지정 된 시간입니다. `HH`
  * `%M` 는로 서식이 지정 된 분입니다. `mm`

현재 메트릭 관리자는 다음과 같이 JSON 파일의 데이터 스키마를 지원 합니다. 예를 들면 다음과 같습니다.

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **연결 문자열**: 메트릭 관리자는 sql server 데이터 원본에 대 한 [ADO.NET 스타일 연결 문자열](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax) 을 허용 합니다.

    샘플 연결 문자열:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **쿼리**: 다차원 시계열 데이터에 대 한 데이터를 가져오고 작성 하는 SQL 쿼리입니다. 쿼리에 변수를 사용 하 여 `@StartTime` 예상 되는 메트릭 값을 쉽게 가져올 수 있습니다.

  * `@StartTime`: 형식의 datetime `yyyy-MM-dd HH:mm:ss`

    예제 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12의 데이터 조각에 대해 실행 된 실제 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **연결 문자열**: Azure Table Storage에서 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [연결 문자열 보기 및 복사](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage?toc=%2Fazure%2Fstorage%2Ftables%2Ftoc.json&tabs=azure-portal#view-account-access-keys) 를 참조 하세요.

* **테이블 이름**: 쿼리할 테이블을 지정 합니다. 이는 Azure Storage 계정 인스턴스에서 찾을 수 있습니다. **테이블 서비스** 섹션에서 **테이블** 을 클릭 합니다.

* **쿼리** 쿼리에서를 사용할 수 있습니다 `@StartTime` . `@StartTime` 스크립트에서 yyyy-MM-Yyyy-mm-ddthh: MM: ss 형식 문자열로 바뀝니다.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **호스트**: Elasticsearch 클러스터의 마스터 호스트를 지정 합니다.
* **포트**: Elasticsearch 클러스터의 마스터 포트를 지정 합니다.
* **권한 부여 헤더**: Elasticsearch Cluster의 인증 헤더 값을 지정 합니다.
* **쿼리**: 데이터를 가져올 쿼리를 지정 합니다. 자리 표시 자가 @StartTime 지원 됩니다. ( 예: 2020-06-21T00:00:00Z의 데이터가 수집, @StartTime = 2020-06-21T00:00:00) 인 경우

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 요청</span>

* **요청 URL**: JSON을 반환할 수 있는 HTTP URL입니다. 자리 표시자% Y,% m,% d,% h,% M이 (가) 지원 됩니다 .% Y = 연도 형식 yyyy,% m = 월 (MM),% d = 월 형식 예: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`
* **요청 HTTP 메서드**: GET 또는 POST를 사용 합니다.
* **요청 헤더**: 기본 인증을 추가할 수 있습니다. 
* **요청 페이로드**: JSON 페이로드만 지원 됩니다. 페이로드에서 자리 표시 자가 @StartTime 지원 됩니다. 응답은 다음과 같은 JSON 형식 이어야 합니다. [{"timestamp": "2018-01-01T00:00:00Z", "market": "en-us", "count": 11, "수익": 1.23}, {"timestamp": "2018-01-01T00:00:00Z", "market": "zh-cn-cn", "count": 22, "수익": 4.56}]. (예: 2020-06-21T00:00:00Z의 데이터가 수집, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **연결 문자열**: InfluxDB에 액세스 하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **쿼리**: 수집을 위해 다차원 시계열 데이터에 대 한 데이터를 가져오고 작성 하는 쿼리입니다.
* **사용자 이름**: 인증에 대 한 선택 사항입니다. 
* **암호**: 인증에 대 한 선택 사항입니다. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **연결 문자열**: MongoDB에 액세스 하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **Command**: 수집을 위해 다차원 시계열 데이터로 데이터를 가져오고 작성 하는 명령입니다.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **연결 문자열**: MySQL DB에 액세스 하기 위한 연결 문자열입니다.
* **쿼리**: 수집을 위해 다차원 시계열 데이터에 대 한 데이터를 가져오고 작성 하는 쿼리입니다.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **연결 문자열**: PostgreSQL DB에 액세스 하기 위한 연결 문자열입니다.
* **쿼리**: 수집을 위해 다차원 시계열 데이터에 대 한 데이터를 가져오고 작성 하는 쿼리입니다.

## <a name="next-steps"></a>다음 단계

* 메트릭 데이터가 시스템에 수집 될 때까지 기다리는 동안 [데이터 피드 구성을 관리 하는 방법](how-tos/manage-data-feeds.md)을 참조 하세요.
* 메트릭 데이터가 수집 면 [메트릭을 구성 하 고 검색 구성을 세부적으로 조정할](how-tos/configure-metrics.md)수 있습니다.
