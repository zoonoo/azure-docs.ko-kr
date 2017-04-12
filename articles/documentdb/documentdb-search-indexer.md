---
title: "인덱서를 사용해서 DocumentDB를 Azure Search에 연결 | Microsoft Docs"
description: "이 문서에서는 DocumentDB에서 Azure 검색 인덱서를 데이터 소스로 사용하는 방법을 보여 줍니다."
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: fdef3d1d-b814-4161-bdb8-e47d29da596f
ms.service: documentdb
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/10/2017
ms.author: mimig
redirect_url: https://docs.microsoft.com/azure/search/search-howto-index-documentdb
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 9a5416b1c26d1e8eaecec0ada79d357f32ca5ab1
ms.openlocfilehash: c318d7133e26ec3a39d6fc97b0693b44d742d456
ms.lasthandoff: 01/12/2017


---
# <a name="connecting-documentdb-with-azure-search-using-indexers"></a>인덱서를 사용해서 DocumentDB를 Azure 검색에 연결
DocumentDB 데이터에 대해 뛰어난 검색 환경을 구현하기 위해서는 DocumentDB에 대한 Azure 검색 인덱서를 사용할 수 있습니다. 이 문서에서는 인덱싱 인프라를 유지 관리하기 위해 어떠한 코드도 작성할 필요 없이 Azure DocumentDB를 Azure 검색과 통합하는 방법을 보여 줍니다.

이를 설정하기 위해서는 [Azure Search 계정을 설정](../search/search-create-service-portal.md)하고(표준 검색으로 업그레이드할 필요 없음), [Azure Search REST API](https://msdn.microsoft.com/library/azure/dn798935.aspx)를 호출해서 DocumentDB **데이터 원본** 및 이 데이터 원본에 대한 **인덱서**를 만들어야 합니다.

REST API와 상호 작용하기 위한 요청을 보내려면 [Postman](https://www.getpostman.com/), [Fiddler](http://www.telerik.com/fiddler) 또는 기본 설정 도구를 사용할 수 있습니다.

## <a name="a-idconceptsaazure-search-indexer-concepts"></a><a id="Concepts"></a>Azure 검색 인덱서 개념
Azure 검색에서는 데이터 소스(DocumentDB 포함) 및 데이터 소스에 대해 작동하는 인덱서의 생성 및 관리가 지원됩니다.

**데이터 소스** 는 인덱싱해야 하는 데이터, 데이터에 액세스하기 위한 자격 증명, Azure 검색이 데이터 변경 사항(컬렉션 내에서 수정 또는 삭제된 문서)을 효율적으로 식별하기 위한 정책을 지정합니다. 데이터 소스는 독립 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

**인덱서** 는 데이터 소스에서 대상 검색 인덱스로 데이터 흐름 방법을 설명합니다. 모든 대상 인덱스 및 데이터 소스 조합에 대해 하나의 인덱스를 생성하도록 계획해야 합니다. 동일한 인덱스에 여러 인덱서 쓰기를 설정할 수 있지만 인덱서는 단일 인덱스에만 쓸 수 있습니다. 인덱서는 다음 목적으로 사용됩니다.

* 인덱스를 채우기 위해 데이터에 대한 일회성 복사를 수행합니다.
* 예약에 따라 인덱스를 데이터 소스의 변경 사항과 동기화합니다. 일정은 인덱서 정의의 일부입니다.
* 필요에 따라 인덱스에 대해 요청 시 업데이트를 호출합니다.

## <a name="a-idcreatedatasourceastep-1-create-a-data-source"></a><a id="CreateDataSource"></a>1단계: 데이터 소스 만들기
HTTP POST 요청을 실행해서 Azure 검색 서비스에서 다음 요청 헤더를 포함하는 새로운 데이터 소스를 만듭니다.

    POST https://[Search service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

`api-version` 은 필수 사항입니다. 유효한 값에는 `2015-02-28` 이상 버전이 포함됩니다. 지원되는 모든 검색 API 버전을 보려면 [Azure 검색의 API 버전](../search/search-api-versions.md) 을 확인하세요.

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

* **이름**: DocumentDB 데이터베이스를 표시할 이름을 선택합니다.
* **형식**: `documentdb`를 사용합니다.
* **자격 증명**:
  
  * **connectionString**: 필수입니다. Azure DocumentDB 데이터베이스에 대한 연결 정보를 다음 형식으로 지정합니다. `AccountEndpoint=<DocumentDB endpoint url>;AccountKey=<DocumentDB auth key>;Database=<DocumentDB database id>`
* **컨테이너**:
  
  * **이름**: 필수입니다. 인덱싱할 DocumentDB 컬렉션의 ID를 지정합니다.
  * **쿼리**: 선택 사항입니다. 추상 JSON 문서를 Azure 검색이 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다.
* **dataChangeDetectionPolicy**: 선택 사항입니다. 아래의 [데이터 변경 감지 정책](#DataChangeDetectionPolicy) 을 참조하십시오.
* **dataDeletionDetectionPolicy**: 선택 사항입니다. 아래의 [데이터 삭제 감지 정책](#DataDeletionDetectionPolicy) 을 참조하십시오.

아래 [요청 본문 예제](#CreateDataSourceExample)를 참조하세요.

### <a name="a-iddatachangedetectionpolicyacapturing-changed-documents"></a><a id="DataChangeDetectionPolicy"></a>변경된 문서 캡처
데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재까지 지원되는 유일한 정책은 DocumentDB에서 제공된 마지막으로 수정된 타임스탬프 속성인 `_ts`를 사용하는 `High Water Mark` 정책입니다. 이는 다음과 같이 지정됩니다.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

또한 프로젝션에 `_ts`를 추가하고 쿼리에 대한 `WHERE` 절을 추가해야 합니다. 예:

    SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts >= @HighWaterMark

### <a name="a-iddatadeletiondetectionpolicyacapturing-deleted-documents"></a><a id="DataDeletionDetectionPolicy"></a>삭제된 문서 캡처
소스 테이블에서 행을 삭제할 때는 검색 인덱스에서도 해당 행을 삭제해야 합니다. 데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재까지 지원되는 유일한 정책은 다음과 같이 지정되는 `Soft Delete` 정책입니다(삭제 시 일부 유형의 플래그로 표시됨).

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

> [!NOTE]
> 사용자 지정 프로젝션을 사용할 경우에는 SELECT 절에 softDeleteColumnName 속성을 포함해야 합니다.
> 
> 

### <a name="a-idleveagingqueriesaleveraging-queries"></a><a id="LeveagingQueries"></a>쿼리 활용
변경 및 삭제된 문서를 캡처하는 것뿐만 아니라 DocumentDB 쿼리를 사용하여 중첩된 속성을 평면화하고, 배열을 해제하고, json 속성을 프로젝션하고, 인덱싱할 데이터를 필터링할 수 있습니다. 인덱싱할 데이터를 조작하면 Azure Search 인덱서의 성능을 높일 수 있습니다.

예제 문서:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }


쿼리 결합:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark
    
    
프로젝션 쿼리:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark


배열 해제 쿼리:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark
    
    
필터 쿼리:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark


### <a name="a-idcreatedatasourceexamplearequest-body-example"></a><a id="CreateDataSourceExample"></a>요청 본문 예
다음 예제에서는 사용자 지정 쿼리 및 정책 힌트를 사용해서 데이터 소스를 만듭니다.

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": {
            "name": "myDocDbCollectionId",
            "query": "SELECT s.id, s.Title, s.Abstract, s._ts FROM Sessions s WHERE s._ts > @HighWaterMark"
        },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

### <a name="response"></a>응답
데이터 소스가 성공적으로 생성되었으면, HTTP 201 생성된 응답이 표시됩니다.

## <a name="a-idcreateindexastep-2-create-an-index"></a><a id="CreateIndex"></a>2단계: 인덱스 만들기
대상 Azure 검색 인덱스가 아직 없으면 만듭니다. 이 작업은 [Azure Portal UI](../search/search-create-index-portal.md) 또는 [인덱스 API 만들기](https://msdn.microsoft.com/library/azure/dn798941.aspx)를 사용해서 수행할 수 있습니다.

    POST https://[Search service name].search.windows.net/indexes?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]


대상 인덱스의 스키마가 소스 JSON 문서의 스키마 또는 사용자 지정 쿼리 프로젝션의 출력과 호환되는지 확인합니다.

> [!NOTE]
> 분할된 컬렉션의 경우 기본 문서 키는 DocumentDB의 `_rid` 속성이며 Azure Search에서 이름을 `rid`로 변경합니다. 또한 DocumentDB의 `_rid` 값은 Azure Search 키에 유효하지 않은 문자를 포함합니다. 따라서 `_rid` 값은 Base64로 인코딩됩니다.
> 
> 

### <a name="figure-a-mapping-between-json-data-types-and-azure-search-data-types"></a>그림 A: JSON 데이터 형식과 Azure 검색 데이터 형식 사이의 매핑
| JSON 데이터 형식 | 호환되는 대상 인덱스 필드 형식 |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| 정수와 같이 보이는 숫자 |Edm.Int32, Edm.Int64, Edm.String |
| 부동소수점처럼 보이는 숫자 |Edm.Double, Edm.String |
| String |Edm.String |
| 기본 형식의 배열, 예: "a", "b", "c" |Collection(Edm.String) |
| 날짜처럼 보이는 문자열 |Edm.DateTimeOffset, Edm.String |
| GeoJSON 개체, 예: { "type": "Point", "coordinates": [ long, lat ] } |Edm.GeographyPoint |
| 기타 JSON 개체 |해당 없음 |

### <a name="a-idcreateindexexamplearequest-body-example"></a><a id="CreateIndexExample"></a>요청 본문 예
다음 예제에서는 id 및 설명 필드를 사용해서 인덱스를 만듭니다.

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

### <a name="response"></a>응답
인덱스가 생성되었으면 HTTP 201 생성 응답이 수신됩니다.

## <a name="a-idcreateindexerastep-3-create-an-indexer"></a><a id="CreateIndexer"></a>3단계: 인덱서 만들기
다음 헤더와 함께 HTTP, POST 요청을 사용해서 Azure 검색 서비스 내에서 새 인덱서를 만듭니다.

    POST https://[Search service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [Search service admin key]

요청 본문에는 다음 필드를 포함해야 하는 인덱서 정의가 포함됩니다.

* **이름**: 필수입니다. 인덱서의 이름입니다.
* **dataSourceName**: 필수입니다. 기존 데이터 소스의 이름입니다.
* **targetIndexName**: 필수입니다. 기존 인덱스의 이름입니다.
* **일정**: 선택 사항입니다. 아래의 [인덱싱 예약](#IndexingSchedule) 을 참조하십시오.

### <a name="a-idindexingschedulearunning-indexers-on-a-schedule"></a><a id="IndexingSchedule"></a>일정에 따라 인덱서 실행
인덱서는 선택적으로 일정을 지정할 수 있습니다. 일정이 제공된 경우, 인덱서가 일정에 따라 주기적으로 실행됩니다. 일정은 다음과 같은 특성을 갖습니다.

* **간격**: 필수입니다. 인덱서 실행 간격 또는 기간을 지정하는 기간 값입니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.
* **startTime**: 필수입니다. 인덱서 실행을 시작해야 할 경우를 지정하는 UTC 날짜/시간입니다.

### <a name="a-idcreateindexerexamplearequest-body-example"></a><a id="CreateIndexerExample"></a>요청 본문 예
다음 예에서는 `myDocDbDataSource` 데이터 원본에서 참조되는 컬렉션에서 2015년 1월 1일 UTC에 시작하고 시간별로 실행되는 일정의 `mySearchIndex` 인덱스로 데이터를 복사하는 인덱서를 만듭니다.

    {
        "name" : "mysearchindexer",
        "dataSourceName" : "mydocdbdatasource",
        "targetIndexName" : "mysearchindex",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" }
    }

### <a name="response"></a>응답
인덱서를 만든 다음에는 HTTP 201 응답 생성이 수신됩니다.

## <a name="a-idrunindexerastep-4-run-an-indexer"></a><a id="RunIndexer"></a>4단계: 인덱서 실행
일정에 따라 주기적으로 실행하는 것 외에도 다음 HTTP POST 요청을 실행해서 요청 시에 인덱서를 호출할 수도 있습니다.

    POST https://[Search service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>응답
인덱서를 호출한 다음에는 HTTP 202 수락된 응답이 수신됩니다.

## <a name="a-namegetindexerstatusastep-5-get-indexer-status"></a><a name="GetIndexerStatus"></a>5단계: 인덱서 상태 가져오기
HTTP GET 요청을 실행해서 인덱서의 현재 상태 및 실행 기록을 검색할 수 있습니다.

    GET https://[Search service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [Search service admin key]

### <a name="response"></a>응답
전반적인 인덱서 상태, 마지막 인덱서 호출에 대한 정보와 최근 인덱서 호출 기록(있는 경우)이 포함된 응답 본문과 함께 HTTP 200 OK 응답이 표시됩니다.

응답은 다음과 같아야 합니다.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

실행 기록은 50개의 최근에 완료한 실행까지 포함할 수 있으며, 시간 순서의 반대로 정렬됩니다(최신 항목이 응답에서 먼저 표시됨)

## <a name="a-namenextstepsanext-steps"></a><a name="NextSteps"></a>다음 단계
축하합니다. 지금까지 DocumentDB의 인덱서를 사용해서 Azure 검색에 Azure DocumentDB를 통합하는 방법에 대해 배웠습니다.

* Azure DocumentDB에 대해 알아보려면 [DocumentDB 서비스 페이지](https://azure.microsoft.com/services/documentdb/)를 참조하세요.
* Azure 검색에 대해 알아보려면 [검색 서비스 페이지](https://azure.microsoft.com/services/search/)를 참조하세요.


