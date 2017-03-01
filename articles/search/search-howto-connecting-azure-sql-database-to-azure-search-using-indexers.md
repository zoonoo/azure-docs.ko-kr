---
title: "인덱서를 사용하여 Azure Search에 Azure SQL Database 연결 | Microsoft Docs"
description: "인덱서를 사용하여 Azure SQL 데이터베이스에서 Azure 검색 인덱스로 데이터를 가져오는 방법에 대해 알아봅니다."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 02/15/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 0841744b806f3dba38dddee21fb7fe881e07134f
ms.openlocfilehash: 51c9d9afb6c2ed460abd4c47a6afbc404b97a85e
ms.lasthandoff: 02/16/2017

---

# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>인덱서를 사용하여 Azure 검색에 Azure SQL 데이터베이스 연결
Azure 검색 서비스는 호스팅되는 클라우드 검색 서비스로, 훌륭한 검색 환경을 간편하게 제공할 수 있습니다. 검색에 앞서 데이터를 Azure 검색 인덱스에 입력해야 합니다. 데이터가 Azure SQL Database에 있는 경우 새 **Azure SQL Database용 Azure Search 인덱서**(또는 줄여서 **Azure SQL 인덱서**)를 사용하여 인덱싱 프로세스를 자동화할 수 있습니다. 이는 작성할 코드 및 관리할 인프라가 없음을 의미합니다.

이 문서에서는 인덱서 사용 원리를 다루지만 Azure SQL Database에서만 사용할 수 있는 기능(예: 통합 변경 내용 추적)에 대해서도 설명합니다. Azure Search는 Azure DocumentDB, Blob Storage, Table Storage 등 다른 데이터 원본도 지원합니다. 추가 데이터 원본에 대한 지원을 확인하려면 [Azure Search 사용자 의견 포럼](https://feedback.azure.com/forums/263029-azure-search/)에 의견을 남겨 주시기 바랍니다.

## <a name="indexers-and-data-sources"></a>인덱서 및 데이터 원본
다음을 사용하여 Azure SQL 인덱서를 설정하고 구성할 수 있습니다.

* [Azure Portal](https://portal.azure.com)의 데이터 가져오기 마법사
* Azure Search [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)
* Azure Search [REST API](http://go.microsoft.com/fwlink/p/?LinkID=528173)

이 문서에서는 REST API를 사용하여 **인덱서** 및 **데이터 원본**을 만들고 관리하는 방법을 보여 줍니다.

**데이터 원본**은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 이는 독립된 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

**인덱서** 는 데이터 원본을 대상 검색 인덱스에 연결하는 리소스입니다. 인덱서는 다음과 같은 방법으로 사용됩니다.

* 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
* 예약에 따라 데이터 원본의 변경 내용으로 인덱스를 업데이트합니다.
* 필요에 따라 요청 시 인덱스 업데이트를 실행합니다.

## <a name="when-to-use-azure-sql-indexer"></a>Azure SQL 인덱서를 사용하는 경우
데이터와 관련된 여러 요소에 따라 Azure SQL 인덱서를 사용하는 것이 적절할 수도 있고 그렇지 않을 수도 있습니다. 데이터가 다음 요구 사항에 적합한 경우 Azure SQL 인덱서를 사용할 수 있습니다.

* 모든 데이터가 단일 테이블 또는 뷰에서 제공됩니다.
  * 데이터가 여러 테이블에 분산된 경우 뷰를 만들고 이 뷰를 인덱서에서 사용할 수 있습니다. 그러나 뷰를 사용하는 경우 SQL Server 통합 변경 내용 검색을 사용할 수 없습니다. 자세한 내용은 [이 섹션](#CaptureChangedRows)을 참조하세요.
* 데이터 원본에 사용된 데이터 형식이 인덱서에서 지원됩니다. 전부는 아니지만 대부분의 SQL 형식이 지원됩니다. 자세한 내용은 [Azure 검색에서 데이터 형식 매핑](http://go.microsoft.com/fwlink/p/?LinkID=528105)을 참조하세요.
* 행이 변경될 때 인덱스에 대한 실시간 업데이트가 필요 없습니다.
  * 인덱서는 최대 5분마다 테이블을 다시 인덱싱할 수 있습니다. 데이터가 자주 변경되고 변경 내용을 몇 초 또는 몇 분 이내에 인덱스에 반영해야 하는 경우에는 [Azure 검색 인덱스 API](https://msdn.microsoft.com/library/azure/dn798930.aspx) 를 직접 사용하는 것이 좋습니다.
* 데이터 집합이 크고 일정에 따라 인덱서를 실행하려는 경우 사용자의 스키마를 통해 변경된 행(및 해당되는 경우 삭제된 행)을 효율적으로 식별할 수 있습니다. 자세한 내용은 아래의 “변경 및 삭제된 행 캡처”를 참조하세요.
* 행에서 인덱싱된 필드의 크기가 Azure 검색 인덱싱 요청의 최대 크기(16MB)를 초과하지 않습니다.

## <a name="create-and-use-an-azure-sql-indexer"></a>Azure SQL 인덱서 만들기 및 사용
먼저, 데이터 원본을 만듭니다.

    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


`ADO.NET connection string` 옵션을 사용하여 [Azure 클래식 포털](https://portal.azure.com)에서 연결 문자열을 가져올 수 있습니다.

그런 다음 대상 Azure 검색 인덱스가 없는 경우 새로 만듭니다. [포털 UI](https://portal.azure.com) 또는 [인덱스 만들기 API](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 사용하여 인덱스를 만들 수 있습니다. 대상 인덱스의 스키마가 원본 테이블의 스키마와 호환되는지 확인합니다. [SQL 데이터 형식과 Azure Search 데이터 형식 사이의 매핑](#TypeMapping)을 참조하세요.

마지막으로, 이름을 지정하고 데이터 원본 및 대상 인덱스를 참조하여 인덱서는 만듭니다.

    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

이 방법으로 만든 인덱서에는 일정이 없습니다. 만들어지면 자동으로 한 번 실행됩니다. 언제든지 **run indexer** 요청을 사용하여 다시 실행할 수 있습니다.

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

인덱서 동작의 몇 가지 측면(예: 배치 크기, 인덱서 실행에 실패하기 전에 건너뛸 수 있는 문서 수)을 사용자 지정할 수 있습니다. 자세한 내용은 [인덱서 API 만들기](https://msdn.microsoft.com/library/azure/dn946899.aspx)를 참조하세요.

Azure 서비스에서 데이터베이스에 연결하도록 허용해야 할 수 있습니다. 이 작업을 수행하는 방법에 대한 지침은 [Azure에서 연결](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 을 참조하세요.

인덱서 상태 및 실행 기록(인덱싱된 항목 수, 오류 등)을 모니터링하려면 **indexer status** 요청을 사용합니다.

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

응답은 다음과 같아야 합니다.

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)
응답에 대한 추가 정보는 [인덱서 상태 가져오기](http://go.microsoft.com/fwlink/p/?LinkId=528198)에서 확인할 수 있습니다.

## <a name="run-indexers-on-a-schedule"></a>일정에 따라 인덱서 실행
일정에 따라 주기적으로 실행되도록 인덱서를 정렬할 수도 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 추가합니다. 아래 예제에서는 인덱서를 업데이트하는 PUT 요청을 보여 줍니다.

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

선택적 **startTime**은 예약된 실행을 시작해야 하는 시점을 나타냅니다. 생략한 경우 현재 UTC 시간이 사용됩니다. 이 시간은 과거의 시간일 수 있습니다. 이 경우 첫 번째 실행은 인덱서가 startTime 이후에 지속적으로 실행된 것처럼 예약됩니다.  

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 인덱서가 실행 중일 때 실행이 예약된 경우 다음 예약 시간까지 실행이 연기됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 다음 시간별 일정을 구성했다고 가정해 보겠습니다.

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

다음과 같은 상황이 발생합니다.

1. 첫 번째 인덱서 실행은 2015년 3월 1일 오전 12시경(UTC)에 시작됩니다.
2. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
3. 두 번째 인덱서 실행은 2015년 3월 1일 오전 1시경에 시작됩니다.
4. 이제 이 실행이 오전 2시 10분경에 완료되도록 1시간이 넘게(예: 70분) 걸린다고 가정해 보겠습니다.
5. 세 번째 실행의 시작 시간은 오전 2시입니다. 그러나 오전 1시에 시작된 두 번째 실행이 여전히 실행 중이므로 세 번째 실행을 건너뜁니다. 세 번째 실행은 오전 3시에 시작됩니다.

**PUT indexer** 요청을 사용하여 기존 인덱서에 대한 일정을 추가, 변경 또는 삭제할 수 있습니다.

<a name="CaptureChangedRows"></a>

## <a name="capturing-new-changed-and-deleted-rows"></a>새 행, 변경된 행 및 삭제된 행 캡처
테이블에 많은 행이 있으면 데이터 변경 검색 정책을 사용해야 합니다. 변경 검색을 사용하면 전체 테이블을 다시 인덱싱하지 않고도 새 행 또는 변경된 행만 효율적으로 검색할 수 있습니다.

### <a name="sql-integrated-change-tracking-policy"></a>SQL 통합 변경 내용 추적 정책
SQL 데이터베이스에서 [변경 내용 추적](https://msdn.microsoft.com/library/bb933875.aspx)을 지원하는 경우 **SQL 통합 변경 내용 추적 정책**을 사용하는 것이 좋습니다. 가장 효율적인 정책입니다. 또한 테이블에 "soft delete" 열을 명시적으로 추가하지 않고도 Azure Search에서 삭제된 행을 식별할 수 있습니다.

통합 변경 내용 추적은 다음 SQL Server 데이터베이스 버전부터 지원됩니다.

* SQL Server 2008 R2 이상(Azure VM의 SQL Server를 사용하는 경우)
* Azure SQL 데이터베이스 V12(Azure SQL 데이터베이스를 사용하는 경우)

SQL 통합 변경 내용 추적 정책을 사용할 때는 별도의 데이터 삭제 검색 정책을 지정하지 마세요. 이 정책은 삭제된 행 식별을 기본적으로 지원합니다.

이 정책은 테이블에만 사용할 수 있으며 보기에는 사용할 수 없습니다. 이 정책을 사용하려면 사용 중인 테이블에 대해 변경 내용 추적을 사용하도록 설정해야 합니다. 지침은 [변경 내용 추적 설정 및 해제](https://msdn.microsoft.com/library/bb964713.aspx) 를 참조하세요.

이 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책
SQL 통합 변경 내용 추적 정책이 권장되지만 이 정책은 테이블에서만 사용할 수 있고 뷰에서는 사용할 수 없습니다. 뷰를 사용하는 경우 상위 워터 마크 정책을 사용하는 것이 좋습니다. 이 정책은 테이블 또는 뷰에 다음 조건을 충족하는 열이 포함된 경우에 사용할 수 있습니다.

* 모든 삽입 시 열의 값을 지정합니다.
* 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
* 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.
* 다음 WHERE 및 ORDER BY 절이 포함된 쿼리를 효율적으로 실행할 수 있습니다. `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`.

> [!IMPORTANT] 
> 변경 추적 시 **rowversion** 열을 사용하는 것이 좋습니다. 다른 데이터 형식을 사용하는 경우 변경 추적이 인덱서 쿼리와 동시에 실행되는 트랜잭션의 모든 변경 내용을 캡처하지는 않습니다.

높은 워터 마크 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> 원본 테이블의 상위 워터 마크 열에 인덱스가 없는 경우 SQL 인덱서에서 사용하는 쿼리가 시간 초과될 수 있습니다. 특히 테이블에 많은 행이 포함된 경우 `ORDER BY [High Water Mark Column]` 절에서 인덱스를 효율적으로 실행해야 합니다.
>
>

시간 초과 오류를 발생하는 경우 `queryTimeout` 인덱서 구성 설정을 사용하여 쿼리 제한 시간을 기본값(5분)보다 높은 값으로 설정할 수 있습니다. 예를 들어 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

`ORDER BY [High Water Mark Column]` 절을 사용하지 않도록 설정할 수도 있습니다. 그러나 인덱서 실행이 오류로 인해 중단된 경우 그때까지 거의 모든 행을 처리했더라도 나중에 실행될 때 모든 행을 다시 처리해야 하므로 이는 권장되지 않습니다. `ORDER BY` 절을 사용하지 않도록 설정하려면 인덱서 정의에서 `disableOrderByHighWaterMarkColumn` 설정을 사용합니다.  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Soft Delete 열 삭제 검색 정책
원본 테이블에서 행이 삭제된 경우 검색 인덱스에서도 해당 행을 삭제할 수 있습니다. SQL 통합 변경 내용 추적 정책을 사용하는 경우 이 작업이 자동으로 수행됩니다. 그러나 상위 워터 마크 변경 내용 추적 정책은 삭제된 행을 지원하지 않습니다. 그렇다면 어떻게 해야 할까요?

행이 테이블에서 실제로 제거된 경우 Azure Search에서 더 이상 존재하지 않는 레코드의 현재 상태를 유추할 방법이 없습니다.  그러나 "일시 삭제" 기술을 사용하여 테이블에서 제거하지 않고 논리적으로 행을 삭제할 수 있습니다. 테이블 또는 뷰에 열을 추가하고 이 열을 사용하여 행을 삭제된 것으로 표시합니다.

일시 삭제 기술을 사용하는 경우 데이터 원본을 만들거나 업데이트할 때 다음과 같이 일시 삭제 정책을 지정할 수 있습니다.

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**softDeleteMarkerValue**는 문자열이어야 합니다. 실제 값의 문자열 표현을 사용하세요. 예를 들어 삭제된 행이 값 1로 표시된 정수 열이 있는 경우 `"1"`을 사용합니다. 삭제된 행이 부울 true 값으로 표시된 BIT 열이 있는 경우 `"True"`를 사용합니다.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>SQL 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑
| SQL 데이터 형식 | 허용되는 대상 인덱스 필드 유형 | 참고 사항 |
| --- | --- | --- |
| bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.Int64, Edm.String | |
| real, float |Edm.Double, Edm.String | |
| smallmoney, money decimal numeric |Edm.String |Azure 검색에서는 decimal 형식을 Edm.Double로 변환하면 정밀도가 떨어지기 때문에 이를 지원하지 않습니다. |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |SQL 문자열은 문자열이 JSON 배열을 나타내는 경우 Collection(Edm.String) 필드를 채우는 데 사용할 수 있습니다. `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |SRID가 4326(기본값)인 POINT 형식의 지리 인스턴스만 지원됩니다. |
| rowversion |해당 없음 |행 버전 열은 변경 내용 추적에 사용할 수 있지만 검색 인덱스에 저장할 수는 없습니다. |
| time, timespan, binary, varbinary, image, xml, geometry, CLR types |해당 없음 |지원되지 않음 |

## <a name="configuration-settings"></a>구성 설정
SQL 인덱서는 여러 구성 설정을 노출합니다.

| 설정 | 데이터 형식 | 목적 | 기본값 |
| --- | --- | --- | --- |
| queryTimeout |string |SQL 쿼리 실행의 제한 시간 설정 |5분("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |상위 워터 마크 정책에서 사용하는 SQL 쿼리에서 ORDER BY 절이 생략되도록 합니다. [상위 워터 마크 정책](#HighWaterMarkPolicy)을 참조하세요. |false |

이러한 설정은 인덱서 정의의 `parameters.configuration` 개체에 사용됩니다. 예를 들어 쿼리 제한 시간을 10분으로 설정하려면 다음 구성을 사용하여 인덱서를 만들거나 업데이트합니다.

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="frequently-asked-questions"></a>질문과 대답
**Q:** Azure의 IaaS VM에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

A: 예. 그러나 검색 서비스에서 데이터베이스에 연결할 수 있도록 허용해야 합니다. 자세한 내용은 [Azure VM에서 Azure Search 인덱서로부터 SQL Server로의 연결 구성](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) 문서를 참조하세요.

**Q:** 온-프레미스에서 실행되는 SQL 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

A: 이는 권장되거나 지원되지 않습니다. 이렇게 하려면 데이터베이스를 인터넷 트래픽에 개방해야 하기 때문입니다.

**Q:** Azure의 IaaS에서 실행되는 SQL Server가 아닌 데이터베이스에서 Azure SQL 인덱서를 사용할 수 있습니까?

A: SQL Server이 아닌 데이터베이스에서는 인덱서를 테스트하지 않았기 때문에 이 시나리오는 지원되지 않습니다.  

**Q:** 일정에 따라 실행되는 여러 인덱서를 만들 수 있습니까?

A: 예. 그러나 한 번에 하나의 인덱서만 실행할 수 있습니다. 여러 인덱서를 동시에 실행하려면 둘 이상의 검색 단위로 검색 서비스를 확장하는 것이 좋습니다.

**Q:** 인덱서를 실행하면 쿼리 작업이 영향을 받습니까?

A: 예. 인덱서는 검색 서비스의 노드 중 하나에서 실행되므로 해당 노드의 리소스가 인덱싱 및 쿼리 지원 트래픽과 다른 API 요청 간에 공유됩니다. 많은 인덱싱 및 쿼리 작업을 실행하는 경우 503 오류가 자주 발생하거나 응답 시간이 증가하면 검색 서비스를 확장하는 것이 좋습니다.

