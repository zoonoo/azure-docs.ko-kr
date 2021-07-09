---
title: Metrics Advisor에 서로 다른 원본의 데이터 피드를 추가하는 방법
titleSuffix: Azure Cognitive Services
description: Metrics Advisor에 다른 데이터 피드 추가
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: 8ed4f3463a4061fffe480da7006e8660e95a5af6
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891179"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Metrics Advisor에 서로 다른 데이터 원본의 데이터 피드 추가

이 문서를 사용하여 다양한 유형의 데이터 원본을 Metrics Advisor에 연결하기 위한 설정 및 요구 사항을 찾을 수 있습니다. Metrics Advisor에서 데이터를 사용하기 위한 주요 개념에 대해 알아보려면 [데이터 온보드](how-tos/onboard-your-data.md) 방법을 읽어보세요. \

## <a name="supported-authentication-types"></a>지원되는 인증 형식

| 인증 형식 | Description |
| ---------------------|-------------|
|**기본** | 데이터 원본에 액세스하기 위한 기본 매개 변수를 제공할 수 있어야 합니다. 예를 들어 연결 문자열 또는 키를 제공합니다. 데이터 피드 관리자는 이러한 자격 증명을 볼 수 있습니다. |
| **AzureManagedIdentity** | Azure 리소스에 대한 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)는 Azure Active Directory의 기능입니다. Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하여 Azure AD 인증을 지원하는 모든 서비스에 대해 인증할 수 있습니다.|
| **AzureSQLConnectionString**| Metrics Advisor에서 AzureSQL 연결 문자열을 **자격 증명 엔터티** 로 저장하고 메트릭 데이터를 온보딩할 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다. |
| **DataLakeGen2SharedKey**| Data Lake 계정 키를 Metrics Advisor에 **자격 증명 엔터티** 로 저장하고 메트릭 데이터를 온보딩할 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 이러한 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다.|
| **서비스 주체**| 서비스 주체를 Metrics Advisor에 **자격 증명 엔터티** 로 저장하고 메트릭 데이터를 온보딩할 때마다 직접 사용합니다. 자격 증명 엔터티의 관리자만 자격 증명을 볼 수 있지만 권한이 있는 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다.|
| **키 자격 증명 모음의 서비스 주체**|키 자격 증명 모음의 서비스 주체를 Metrics Advisor에 **자격 증명 엔터티** 로 저장하고 메트릭 데이터를 온보딩할 때마다 직접 사용합니다. **자격 증명 엔터티** 의 관리자만 자격 증명을 볼 수 있지만 뷰어는 자격 증명에 대한 세부 정보를 몰라도 데이터 피드를 만들 수 있습니다. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>지원되는 데이터 소스 및 해당 인증 유형


| 데이터 원본 | 인증 유형 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Basic |
|[**Azure Blob Storage(JSON)**](#blob) | Basic<br>ManagedIdentity|
|[**Azure Cosmos DB(SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer(Kusto)**](#kusto) | Basic<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체<br> |
|[**Azure SQL Database / SQL Server**](#sql) | Basic<br>ManagedIdentity<br>서비스 사용자<br>키 자격 증명 모음의 서비스 주체<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**HTTP 요청**](#http) | Basic | 
|[**InfluxDB(InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

자격 증명 엔터티**를 만들어 데이터 원본에 대한 인증에 사용합니다. 다음 섹션에서는 *기본* 인증에 필요한 매개 변수를 지정합니다. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **애플리케이션 ID**: Application Insights API를 사용할 때 이 애플리케이션을 식별하는 데 사용할 수 있습니다. 애플리케이션 ID를 가져오려면 다음을 수행합니다.

    1. Application Insights 리소스에서 API 액세스를 클릭합니다.

    2. 생성된 애플리케이션 ID를 Metrics Advisor에서 **애플리케이션 ID** 필드에 복사합니다. 
    
    자세한 내용은 [Azure Bot Service 설명서](/azure/bot-service/bot-service-resources-app-insights-keys#application-id)를 참조하세요.

* **API 키**: 브라우저 외부의 애플리케이션에서 이 리소스에 액세스하는 데 사용됩니다. API 키를 가져오려면 다음을 수행합니다.

    1. Application Insights 리소스에서 API 액세스를 클릭합니다.

    2. API 키 만들기를 클릭합니다.

    3. 간단한 설명을 입력하고 원격 분석 읽기 옵션을 선택한 다음, 키 생성 단추를 클릭합니다.

    4. API 키를 Metrics Advisor의 **API 키** 필드에 복사합니다.

* **쿼리**: Azure Application Insights 로그는 Azure Data Explorer에서 빌드되고, Azure Monitor 로그 쿼리는 동일한 Kusto 쿼리 언어의 한 버전을 사용합니다. [Kusto 쿼리 언어 문서](/azure/data-explorer/kusto/query/)에는 언어에 대한 모든 세부 정보가 나와 있으며, Application Insights에 대한 쿼리 작성을 위한 주요 리소스로 참조해야 합니다. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage(JSON)</span>

* **연결 문자열**: 이 문자열 검색에 대한 자세한 내용은 Azure Blob Storage [연결 문자열](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) 문서를 참조하세요.

* **컨테이너**: Metrics Advisor는 단일 컨테이너 아래에 Blob 파일(타임스탬프당 하나의 Blob)로 저장된 시계열 데이터를 예상합니다. 이는 컨테이너 이름 필드입니다.

* **Blob 템플릿**: Blob 파일 이름의 템플릿입니다. 예: `/%Y/%m/X_%Y-%m-%d-%h-%M.json` 지원되는 매개 변수는 다음과 같습니다.
  * `%Y`는 `yyyy` 형식의 연도입니다.
  * `%m`은 `MM` 형식의 월입니다.
  * `%d`는 `dd` 형식의 날짜입니다.
  * `%h`는 `HH` 형식의 시간입니다.
  * `%M`은 `mm` 형식의 분입니다.

* **JSON 형식 버전**: JSON 파일의 데이터 스키마를 정의합니다. 현재 Metrics Advisor는 다음 두 가지 버전을 지원합니다.
  
  * v1(기본값)

      메트릭 *이름* 과 *값* 만 허용됩니다. 예를 들면 다음과 같습니다.
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      메트릭 *차원* 과 *타임스탬프* 도 허용됩니다. 예를 들면 다음과 같습니다.
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

JSON 파일당 타임스탬프는 하나만 허용됩니다. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB(SQL)</span>

* **연결 문자열**: Azure Cosmos DB에 액세스하기 위한 연결 문자열입니다. 이 문자열은 Cosmos DB 리소스의 **키** 에서 찾을 수 있습니다. 
* **데이터베이스**: 쿼리할 데이터베이스입니다. **컨테이너** 섹션의 **찾아보기** 페이지에서 확인할 수 있습니다.
* **연결 ID**: 쿼리할 컬렉션 ID입니다. **컨테이너** 섹션의 **찾아보기** 페이지에서 확인할 수 있습니다.
* **SQL 쿼리**: 데이터를 가져와서 다차원 시계열 데이터로 작성하는 SQL 쿼리입니다. 쿼리에서 `@StartTime` 및 `@EndTime` 변수를 사용할 수 있습니다. `yyyy-MM-dd HH:mm:ss` 형식으로 지정해야 합니다.

    샘플 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12부터의 데이터 조각에 대한 샘플 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer(Kusto)</span>

* **연결 문자열**: Metrics Advisor는 Azure AD 애플리케이션 인증을 사용하여 Azure Data Explorer(Kusto)에 액세스할 수 있도록 지원합니다. Azure AD 애플리케이션을 만들고 등록한 다음 Azure Data Explorer 데이터베이스에 대한 액세스 권한을 부여해야 합니다. 연결 문자열을 가져오려면 [Azure Data Explorer](/azure/data-explorer/provision-azure-ad-app) 설명서를 참조하세요.

* **쿼리**: 데이터를 가져와 다차원 시계열 데이터로 작성하려면 [Kusto 쿼리 언어](/azure/data-explorer/kusto/query)를 참조하세요. 쿼리에서 `@StartTime` 및 `@EndTime` 변수를 사용할 수 있습니다. `yyyy-MM-dd HH:mm:ss` 형식으로 지정해야 합니다.

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **계정 이름**: Azure Data Lake Storage Gen2의 계정 이름입니다. **액세스 키** 의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 확인할 수 있습니다.

* **계정 키**: Azure Data Lake Storage Gen2에 액세스할 계정 이름을 지정합니다. **액세스 키** 설정의 Azure Storage 계정(Azure Data Lake Storage Gen2) 리소스에서 찾을 수 있습니다.

* **파일 시스템 이름(컨테이너)** : Metrics Advisor는 단일 컨테이너 아래에 Blob 파일(타임스탬프당 하나의 Blob)로 저장된 시계열 데이터를 예상합니다. 이는 컨테이너 이름 필드입니다. Azure storage 계정(Azure Data Lake Storage Gen2) 인스턴스에서 확인할 수 있으며 'Blob Service' 섹션에서 '컨테이너'를 클릭합니다.

* **디렉터리 템플릿**: Blob 파일의 디렉터리 템플릿입니다. 예: */%Y/%m/%d*. 지원되는 매개 변수는 다음과 같습니다.
  * `%Y`는 `yyyy` 형식의 연도입니다.
  * `%m`은 `MM` 형식의 월입니다.
  * `%d`는 `dd` 형식의 날짜입니다.
  * `%h`는 `HH` 형식의 시간입니다.
  * `%M`은 `mm` 형식의 분입니다.

* **파일 템플릿**: Blob 파일의 파일 템플릿입니다. 예: *X_%Y-%m-%d-%h-%M.json*. 지원되는 매개 변수는 다음과 같습니다.
  * `%Y`는 `yyyy` 형식의 연도입니다.
  * `%m`은 `MM` 형식의 월입니다.
  * `%d`는 `dd` 형식의 날짜입니다.
  * `%h`는 `HH` 형식의 시간입니다.
  * `%M`은 `mm` 형식의 분입니다.

현재 Metrics Advisor는 다음과 같이 JSON 파일의 데이터 스키마를 지원합니다. 예를 들면 다음과 같습니다.

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **연결 문자열**: Metrics Advisor는 SQL Server 데이터 원본에 대해 [ADO.NET 스타일 연결 문자열](/dotnet/framework/data/adonet/connection-string-syntax)을 허용합니다.

    샘플 연결 문자열:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **쿼리**: 데이터를 가져와서 다차원 시계열 데이터로 작성하는 SQL 쿼리입니다. 쿼리에서 `@StartTime` 변수를 사용하여 예상되는 메트릭 값을 가져올 수 있습니다.

  * `@StartTime`: `yyyy-MM-dd HH:mm:ss` 형식의 날짜/시간입니다.

    샘플 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    2019/12/12의 데이터 조각에 대해 실행된 실제 쿼리:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **연결 문자열**: SAS(공유 액세스 서명) URL을 만들어 여기에 입력합니다. SAS URL을 만드는 가장 간단한 방법은 Azure Portal을 사용하는 것입니다. Azure Portal을 사용하여 그래픽으로 탐색할 수 있습니다. Azure Portal을 통해 SAS URL을 만들려면 먼저 설정 섹션에서 액세스할 스토리지 계정으로 이동한 다음 공유 액세스 서명을 클릭합니다. 최소한 "테이블" 및 "개체" 확인란을 선택한 다음 SAS 및 연결 문자열 생성 단추를 클릭합니다. 테이블 서비스 SAS URL은 복사하여 Metrics Advisor 작업 영역의 텍스트 상자에 채워야 합니다.

* **테이블 이름**: 쿼리할 테이블을 지정합니다. Azure Storage 계정 인스턴스에서 찾을 수 있습니다. **테이블 서비스** 섹션에서 **테이블** 을 클릭합니다.

* **쿼리** 쿼리에서 `@StartTime`을 사용할 수 있습니다. `@StartTime`은 스크립트에서 yyyy-MM-ddTHH:mm:ss 형식의 문자열로 바뀝니다. 팁: Azure Storage Explorer를 사용하여 특정 시간 범위의 쿼리를 만들고 제대로 실행되는지 확인한 다음, 바꿉니다.

    ``` mssql
    date ge datetime'@StartTime' and date lt datetime'@EndTime'
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **호스트**: Elasticsearch 클러스터의 마스터 호스트를 지정합니다.
* **포트**: Elasticsearch 클러스터의 마스터 포트를 지정합니다.
* **인증 헤더**: Elasticsearch 클러스터의 인증 헤더 값을 지정합니다.
* **쿼리**: 데이터를 가져올 쿼리를 지정합니다. 자리 표시자 @StartTime이 지원됩니다(예: 2020-06-21T00:00:00Z의 데이터가 수집된 경우 @StartTime = 2020-06-21T00:00:00입니다.)

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 요청</span>

* **요청 URL**: JSON을 반환할 수 있는 HTTP URL입니다. 자리 표시자 %Y,%m,%d,%h,%M이 지원됩니다. %Y=yyyy 형식의 연도, %m=MM 형식의 월, %d=dd 형식의 날짜, %h=HH 형식의 시간, %M=mm 형식의 분입니다. 예: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`
* **요청 HTTP 메서드**: GET 또는 POST를 사용합니다.
* **요청 헤더**: 기본 인증을 추가할 수 있습니다. 
* **요청 페이로드**: JSON 페이로드만 지원됩니다. 페이로드에는 자리 표시자 @StartTime이 지원됩니다. 응답은 다음과 같은 JSON 형식이어야 합니다. [{"timestamp": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23}, {"timestamp": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}](예: 2020-06-21T00:00:00Z의 데이터가 수집된 경우 @StartTime = 2020-06-21T00:00:00.0000000+00:00입니다.)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB(InfluxQL)</span>

* **연결 문자열**: InfluxDB에 액세스하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.
* **사용자 이름**: 인증을 위한 선택 사항입니다. 
* **암호**: 인증을 위한 선택 사항입니다. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **연결 문자열**: MongoDB에 액세스하기 위한 연결 문자열입니다.
* **데이터베이스**: 쿼리할 데이터베이스입니다.
* **명령**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 명령입니다.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **연결 문자열**: MySQL DB에 액세스하기 위한 연결 문자열입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **연결 문자열**: PostgreSQL DB에 액세스하기 위한 연결 문자열입니다.
* **쿼리**: 수집하기 위해 데이터를 가져오고 다차원 시계열 데이터로 작성하는 쿼리입니다.

## <a name="next-steps"></a>다음 단계

* 메트릭 데이터가 시스템으로 수집될 때까지 기다리는 동안 [데이터 피드 구성 관리 방법](how-tos/manage-data-feeds.md)을 읽어 보세요.
* 메트릭 데이터가 수집되면 [메트릭을 구성하고 검색 구성을 미세 조정](how-tos/configure-metrics.md)할 수 있습니다.
