---
title: Azure 코스모스 DB 데이터 검색
titleSuffix: Azure Cognitive Search
description: Azure Cosmos DB의 데이터를 Azure 인지 검색에서 검색 가능한 인덱스로 가져옵니다. 인덱서는 Azure Cosmos DB와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283005"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서를 사용하여 Cosmos DB 데이터를 인덱싱하는 방법 

> [!IMPORTANT] 
> SQL API는 일반적으로 사용할 수 있습니다.
> MongoDB API, 그렘린 API 및 카산드라 API 지원은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성하여 미리 보기에 대한 액세스를 요청할 수 있습니다. [REST API 버전 2019-05-06-Preview](search-api-preview.md)는 미리 보기 기능을 제공합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

> [!WARNING]
> [일관으로](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) 설정된 [인덱싱 정책이](https://docs.microsoft.com/azure/cosmos-db/index-policy) 있는 Cosmos DB 컬렉션만 Azure 인지 검색에서 지원됩니다. 지연 인덱싱 정책으로 컬렉션을 인덱싱하는 것은 권장되지 않으며 데이터가 누락될 수 있습니다. 인덱싱을 사용하지 않도록 설정한 컬렉션은 지원되지 않습니다.

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure Cosmos DB [인덱서를](search-indexer-overview.md) 구성하는 방법을 보여 주며 이 문서에서는 이 워크플로는 Azure 인지 검색 인덱스를 만들고 Azure Cosmos DB에서 추출한 기존 텍스트로 로드합니다. 

용어는 혼동될 수 있으므로 [Azure Cosmos DB 인덱싱](https://docs.microsoft.com/azure/cosmos-db/index-overview) 및 [Azure 인지 검색 인덱싱은](search-what-is-an-index.md) 각 서비스에 고유한 고유한 작업이라는 점에 유의해야 합니다. Azure 인지 검색 인덱싱을 시작하기 전에 Azure Cosmos DB 데이터베이스가 이미 존재하고 데이터를 포함해야 합니다.

Azure 인지 검색의 Cosmos DB 인덱서에서는 다양한 프로토콜을 통해 액세스하는 [Azure Cosmos DB 항목을](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) 크롤링할 수 있습니다. 

+ 일반적으로 사용할 수 있는 [SQL API의](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)경우 [포털,](#cosmos-indexer-portal) [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)또는 [.NET SDK를](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) 사용하여 데이터 원본 및 인덱서를 만들 수 있습니다.

+ [MongoDB API(미리 보기)의](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)경우 [포털](#cosmos-indexer-portal) 또는 [REST API 버전 2019-05-06-미리 보기를](search-api-preview.md) 사용하여 데이터 원본 및 인덱서를 만들 수 있습니다.

+ [Cassandra API(미리 보기)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) 및 [그렘린 API(미리 보기)의](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)경우 [REST API 버전 2019-05-06-미리 보기만](search-api-preview.md) 사용하여 데이터 원본 및 인덱서를 만들 수 있습니다.


> [!Note]
> Azure Cognitive Search에서 지원되는 것을 보려면 [테이블 API에](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) 대한 사용자 음성에 대한 투표를 할 수 있습니다.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>포털 사용

> [!Note]
> 포털은 현재 SQL API 및 MongoDB API(미리 보기)를 지원합니다.

Azure Cosmos DB 항목을 인덱싱하는 가장 쉬운 방법은 [Azure Portal에서](https://portal.azure.com/)마법사를 사용하는 것입니다. Azure Cognitive Search의 [**데이터 가져오기**](search-import-data-portal.md) 마법사는 데이터를 샘플링하고 컨테이너에서 메타데이터를 읽음으로써 기본 인덱스를 만들고, 소스 필드를 대상 인덱스 필드에 매핑하고, 단일 작업에서 인덱스를 로드할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

대기 시간이 더 낮고 대역폭 요금이 발생하지 않도록 Azure 인지 검색 및 Azure Cosmos DB모두에 동일한 지역 또는 위치를 사용하는 것이 좋습니다.

### <a name="1---prepare-source-data"></a>1 - 원본 데이터 준비

Cosmos DB 계정, SQL API에 매핑된 Azure 코스모스 DB 데이터베이스, MongoDB API(미리 보기) 또는 그렘린 API(미리 보기) 및 데이터베이스의 콘텐츠가 있어야 합니다.

Cosmos DB 데이터베이스에 데이터가 포함되어 있는지 확인합니다. [가져오기 데이터 마법사는](search-import-data-portal.md) 메타데이터를 읽고 데이터 샘플링을 수행하여 인덱스 스키마를 추론하지만 Cosmos DB의 데이터도 로드합니다. 데이터가 누락된 경우 마법사는 "데이터 원본에서 인덱스 스키마를 검색하는 오류: 데이터 원본 'emptycollection'이 데이터를 반환하지 않았기 때문에 프로토타입 인덱스를 빌드할 수 없습니다"라는 오류로 중지합니다.

### <a name="2---start-import-data-wizard"></a>2 - 데이터 가져오기 마법사 시작

Azure 인지 검색 서비스 페이지의 명령 모음에서 [마법사를 시작하거나](search-import-data-portal.md) Cosmos DB SQL API에 연결하는 경우 Cosmos DB 계정의 왼쪽 탐색 창의 **설정** 섹션에서 **Azure 인지 검색 추가를** 클릭할 수 있습니다.

   ![포털에서 데이터 명령 가져오기](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

### <a name="3---set-the-data-source"></a>3 - 데이터 원본 설정

데이터 **원본** 페이지에서 소스는 **코스모스 DB여야**합니다.

+ **이름은** 데이터 원본 개체의 이름입니다. 생성된 후에는 다른 워크로드에 대해 선택할 수 있습니다.

+ **코스모스 DB 계정은** 코스모스 DB의 기본 또는 보조 `AccountEndpoint` 연결 `AccountKey`문자열이어야 하며. MongoDB 컬렉션의 경우 연결 문자열 끝에 **ApiKind=MongoDb를** 추가하고 세미콜론이 있는 연결 문자열과 분리합니다. 그렘린 API 및 카산드라 API의 경우 [REST API에](#cosmosdb-indexer-rest)대한 지침을 사용합니다.

+ **데이터베이스는** 계정의 기존 데이터베이스입니다. 

+ **컬렉션은** 문서의 컨테이너입니다. 가져오기가 성공하려면 문서가 있어야 합니다. 

+ 모든 문서를 원하는 경우 **쿼리가** 비어 있을 수 있으며, 그렇지 않으면 문서 하위 집합을 선택하는 쿼리를 입력할 수 있습니다. **쿼리는** SQL API에서만 사용할 수 있습니다.

   ![코스모스 DB 데이터 소스 정의](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "코스모스 DB 데이터 소스 정의")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - 마법사에서 "콘텐츠 보강" 페이지 건너뛰기

인지 능력(또는 농축)을 추가하는 것은 가져오기 요건이 아닙니다. 인덱싱 파이프라인에 [AI 보강을 추가해야](cognitive-search-concept-intro.md) 하는 특별한 필요가 없는 경우 이 단계를 건너뛰어야 합니다.

단계를 건너뛰려면 페이지 하단의 파란색 버튼을 클릭하여 "다음" 및 "건너뛰기"를 클릭합니다.

### <a name="5---set-index-attributes"></a>5 - 인덱스 특성 설정

**인덱스** 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 메타데이터를 기반으로 원본 데이터를 샘플링하여 필드 목록을 생성할 수 있습니다. 

특성 열 상단의 확인란을 클릭하여 특성을 일괄 선택할 수 있습니다. 클라이언트 앱으로 반환되고 전체 텍스트 검색 처리가 적용되는 모든 필드에 대해 **검색 가능** 및 **검색 가능을** 선택합니다. 정수는 전체 텍스트또는 퍼지 검색 가능하지 않습니다(숫자는 그대로 평가되며 필터에서 종종 유용합니다).

자세한 내용은 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) 및 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support) 설명을 검토합니다. 

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 이러한 필드를 편집할 수 없습니다.

   ![코스모스 DB 지수 정의](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "코스모스 DB 지수 정의")

### <a name="6---create-indexer"></a>6 - 인덱서 만들기

완전히 지정된 마법사는 검색 서비스에서 세 개의 고유한 개체를 만듭니다. 데이터 원본 개체 및 인덱스 개체는 Azure Cognitive Search 서비스의 명명된 리소스로 저장됩니다. 마지막 단계는 인덱서 개체를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

인덱서에 익숙하지 않은 경우 *인덱서가* Azure Cognitive Search의 리소스로 검색 가능한 콘텐츠에 대한 외부 데이터 원본을 크롤링합니다. **가져오기 데이터** 마법사의 출력은 Cosmos DB 데이터 원본을 크롤링하고 검색 가능한 콘텐츠를 추출한 다음 Azure Cognitive Search의 인덱스로 가져오는 인덱서입니다.

다음 스크린샷은 기본 인덱서 구성을 보여 주며 있습니다. 인덱서를 한 번 실행하려는 경우 **Once로** 전환할 수 있습니다. **제출을** 클릭하여 마법사를 실행하고 모든 개체를 만듭니다. 인덱싱이 즉시 시작됩니다.

   ![코스모스 DB 인덱서 정의](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "코스모스 DB 인덱서 정의")

포털 페이지에서 데이터 가져오기를 모니터링할 수 있습니다. 진행률 알림은 인덱싱 상태 및 업로드된 문서 수를 나타냅니다. 

인덱싱이 완료되면 [검색 탐색기](search-explorer.md)를 사용하여 인덱스를 쿼리할 수 있습니다.

> [!NOTE]
> 예상한 데이터가 표시되지 않으면 더 많은 필드에 더 많은 특성을 설정해야 할 수 있습니다. 방금 만든 인덱스 및 인덱서를 삭제하고 마법사를 다시 단계별로 단계별로 5단계에서 인덱스 특성에 대한 선택을 수정합니다. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API 사용

REST API를 사용하여 Azure Cognitive Search의 모든 인덱서에 공통적인 세 부분으로 구성된 워크플로에 따라 Azure Cosmos DB 데이터를 인덱싱할 수 있습니다. 코스모스 DB의 데이터 추출은 인덱서 만들기 요청을 제출할 때 발생합니다. 이 요청이 완료되면 쿼리 가능한 인덱스가 표시됩니다. 

> [!NOTE]
> 코스모스 DB 그렘린 API 또는 코스모스 DB 카산드라 API에서 데이터를 인덱싱하려면 먼저 [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성하여 게이트 된 미리 보기에 대한 액세스를 요청해야 합니다. 요청이 처리되면 [REST API 버전 2019-05-06-미리 보기를](search-api-preview.md) 사용하여 데이터 원본을 만드는 방법에 대한 지침을 받게 됩니다.

이 문서의 앞에서 [Azure Cosmos DB 인덱싱](https://docs.microsoft.com/azure/cosmos-db/index-overview) 및 [Azure 인지 검색 인덱싱](search-what-is-an-index.md) 인덱싱은 별개의 작업이라고 언급했습니다. Cosmos DB 인덱싱의 경우 기본적으로 카산드라 API를 제외한 모든 문서가 자동으로 인덱싱됩니다. 자동 인덱싱을 끄면 문서 ID를 사용하여 자체 링크 또는 쿼리를 통해서만 문서에 액세스할 수 있습니다. Azure 인지 검색 인덱싱을 사용하려면 Azure 인지 검색에 의해 인덱싱되는 컬렉션에서 Cosmos DB 자동 인덱싱을 켜야 합니다. 코스모스 DB 카산드라 API 인덱서 미리 보기에 가입하면 코스모스 DB 인덱싱 을 설정하는 방법에 대한 지침이 제공됩니다.

> [!WARNING]
> Azure Cosmos DB는 DocumentDB의 다음 세대입니다. 이전에 API 버전 **2017-11-11에서** `documentdb` 구문을 사용할 수 있었습니다. 즉, 데이터 원본 형식을 또는 `cosmosdb` 로 `documentdb`지정할 수 있습니다. API 버전 **2019-05-06부터** Azure 인지 검색 API와 포털은 이 문서에서 지시한 대로 `cosmosdb` 구문만 지원합니다. 즉, Cosmos DB `cosmosdb` 끝점에 연결하려면 데이터 원본 형식이 있어야 합니다.

### <a name="1---assemble-inputs-for-the-request"></a>1 - 요청에 대한 입력 어셈블

각 요청에 대해 POST 헤더에서 Azure Cognitive Search의 서비스 이름 및 관리 키와 Blob 저장소에 대한 저장소 계정 이름 및 키를 제공해야 합니다. [Postman을](search-get-started-postman.md) 사용하여 AZURE 인지 검색에 HTTP 요청을 보낼 수 있습니다.

다음 네 값을 메모장에 복사하여 요청에 붙여넣을 수 있습니다.

+ Azure 인지 검색 서비스 이름
+ Azure 인지 검색 관리자 키
+ 코스모스 DB 연결 문자열

포털에서 다음 값을 찾을 수 있습니다.

1. Azure 인지 검색에 대 한 포털 페이지에서 개요 페이지에서 검색 서비스 URL을 복사 합니다.

2. 왼쪽 탐색 창에서 **키를** 클릭한 다음 기본 키 또는 보조 키(동일)를 복사합니다.

3. Cosmos 저장소 계정의 포털 페이지로 전환합니다. 왼쪽 탐색 창에서 **설정**에서 **키**를 클릭합니다. 이 페이지에서는 URI, 두 개의 연결 문자열 및 두 개의 키 집합을 제공합니다. 연결 문자열 중 하나를 메모장에 복사합니다.

### <a name="2---create-a-data-source"></a>2 - 데이터 원본 만들기

**데이터 원본**은 인덱싱할 데이터, 자격 증명 및 데이터의 변경 사항 식별 정책(예: 컬렉션 내 수정 또는 삭제된 문서)을 지정합니다. 데이터 소스는 독립 리소스로 정의되므로 여러 인덱서에서 사용할 수 있습니다.

데이터 원본을 만들려면 POST 요청을 공식화합니다.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | 설명 |
|---------|-------------|
| **(이름)** | 필수 사항입니다. 데이터 원본 개체를 나타내는 이름을 선택합니다. |
|**종류**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**자격 증명** | 필수 사항입니다. 코스모스 DB 연결 문자열이어야 합니다.<br/>SQL 컬렉션의 경우 연결 문자열은 다음과 같은 형식입니다.`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>. MongoDB 컬렉션의 경우 **ApiKind=MongoDb**를 연결 문자열에 추가합니다. <br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>그렘린 그래프와 Cassandra 테이블의 경우 [게이트인 인덱서 미리 보기에](https://aka.ms/azure-cognitive-search/indexer-preview) 등록하여 미리 보기 및 자격 증명 의 포맷 방법에 대한 정보를 얻으십시오.<br/><br/>엔드포인트 URL에는 포트 번호를 사용하지 않습니다. 포트 번호를 포함하는 경우 Azure Cognitive Search는 Azure Cosmos DB 데이터베이스를 인덱싱할 수 없습니다.|
| **컨테이너** | 다음과 같은 요소가 있습니다. <br/>**이름**: 필수입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.<br/>**쿼리**: 선택 사항입니다. 임의JSON 문서를 Azure 인지 검색이 인덱싱할 수 있는 플랫 스키마로 병합하는 쿼리를 지정할 수 있습니다.<br/>MongoDB API, 그렘린 API 및 카산드라 API의 경우 쿼리가 지원되지 않습니다. |
| **데이터 변경 검색정책** | 권장됩니다. [변경된 문서 인덱싱](#DataChangeDetectionPolicy) 섹션을 참조하세요.|
|**데이터 삭제 감지정책** | (선택 사항) [삭제된 문서 인덱싱](#DataDeletionDetectionPolicy) 섹션을 참조하세요.|

### <a name="using-queries-to-shape-indexed-data"></a>쿼리를 사용하여 인덱싱된 데이터 형성
중첩된 속성 또는 배열을 평면화하고, JSON 속성을 프로젝션하고, 인덱싱할 데이터를 필터링하는 SQL 쿼리를 지정할 수 있습니다. 

> [!WARNING]
> **MongoDB API,** **그렘린 API**및 **Cassandra API**: `container.query` 매개 변수는 null 또는 생략으로 설정되어야 합니다. 사용자 지정 쿼리를 사용해야 하는 경우 [사용자 의견](https://feedback.azure.com/forums/263029-azure-search)에서 알려주세요.

예제 문서:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

필터 쿼리:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

평면화 쿼리:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
프로젝션 쿼리:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


배열 평면화 쿼리:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - 대상 검색 인덱스 만들기 

아직 없는 경우 [대상 Azure 인지 검색 인덱스를 만듭니다.](/rest/api/searchservice/create-index) 다음 예제는 ID 및 설명 필드가 있는 인덱스를 만듭니다.

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

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

대상 인덱스의 스키마가 소스 JSON 문서의 스키마 또는 사용자 지정 쿼리 프로젝션의 출력과 호환되는지 확인합니다.

> [!NOTE]
> 분할된 컬렉션의 경우 기본 문서 키는 Azure Cosmos DB의 `_rid` 속성이며, 필드 `rid` 이름은 밑줄 문자로 시작할 수 없기 때문에 Azure Cognitive Search가 자동으로 이름을 바꿉니다. 또한 Azure Cosmos `_rid` DB 값에는 Azure 인지 검색 키에서 유효하지 않은 문자가 포함되어 있습니다. 따라서 `_rid` 값은 Base64로 인코딩됩니다.
> 
> MongoDB 컬렉션의 경우 Azure 인지 검색에서 `_id` 속성의 이름을 `id`로 자동으로 바꿉니다.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 데이터 유형과 Azure 인지 검색 데이터 형식 간의 매핑
| JSON 데이터 형식 | 호환되는 대상 인덱스 필드 형식 |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| 정수와 같이 보이는 숫자 |Edm.Int32, Edm.Int64, Edm.String |
| 부동소수점처럼 보이는 숫자 |Edm.Double, Edm.String |
| String |Edm.String |
| 기본 형식의 배열, 예: ["a", "b", "c"] |Collection(Edm.String) |
| 날짜처럼 보이는 문자열 |Edm.DateTimeOffset, Edm.String |
| GeoJSON 개체, 예: { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 기타 JSON 개체 |해당 없음 |

### <a name="4---configure-and-run-the-indexer"></a>4 - 인덱서 구성 및 실행

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정 정의에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 를 예약하는 방법을](search-howto-schedule-indexers.md)참조하십시오.

## <a name="use-net"></a>.NET 사용

일반적으로 사용 가능한 .NET SDK는 일반적으로 사용 가능한 REST API와 전체 패리티를 가합니다. 개념, 워크플로 및 요구 사항을 알아보려면 이전 REST API 섹션을 검토하는 것이 좋습니다. 그런 후, 다음 .NET API 참조 설명서를 참조하여 관리되는 코드에서 JSON 인덱서를 구현할 수 있습니다.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>변경된 문서 인덱싱

데이터 변경 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재 지원되는 정책은 Azure [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) Cosmos DB에서 제공하는 `_ts` (timestamp) 속성을 사용하는 것입니다.

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

적절한 인덱서 성능을 보장하기 위해서는 반드시 이 정책을 사용하는 것이 좋습니다. 

사용자 지정 쿼리를 사용하는 경우 `_ts` 속성이 쿼리에 의해 프로젝션되어야 합니다.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>증분 진행률 및 사용자 지정 쿼리

인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다. 

사용자 지정 쿼리를 사용하는 경우 증분 진행률을 사용하려면 쿼리가 `_ts` 열을 기준으로 결과를 정렬해야 합니다. 이렇게 하면 Azure Cognitive Search가 오류가 있는 경우 증분 진행률을 제공하는 데 사용하는 주기적인 검사 가리키는 작업을 수행할 수 있습니다.   

경우에 따라 쿼리에 절이 `ORDER BY [collection alias]._ts` 포함되어 있더라도 Azure Cognitive Search는 `_ts`에서 쿼리의 순서가 지정되지 않을 수 있습니다. Azure Cognitive Search에서 구성 속성을 사용하여 `assumeOrderByHighWaterMarkColumn` 결과가 정렬되도록 알 릴 수 있습니다. 이 힌트를 지정하려면 다음과 같이 인덱서를 만들거나 업데이트합니다. 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>삭제된 문서 인덱싱

컬렉션에서 행이 삭제된 경우 일반적으로 검색 인덱스에서도 해당 행을 삭제하려고 할 것입니다. 데이터 삭제 감지 정책의 목적은 변경된 데이터 항목을 효율적으로 식별하는 것입니다. 현재까지 지원되는 유일한 정책은 다음과 같이 지정되는 `Soft Delete` 정책입니다(삭제 시 일부 유형의 플래그로 표시됨).

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

사용자 지정 쿼리를 사용하는 경우 `softDeleteColumnName`에서 참조되는 속성이 쿼리에 의해 프로젝션됩니다.

다음 예제에서는 일시 삭제 정책을 사용하여 데이터 원본을 만듭니다.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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

## <a name="next-steps"></a><a name="NextSteps"></a>다음 단계

축하합니다! 인덱서를 사용하여 Azure Cosmos DB를 Azure 코그너티브 검색과 통합하는 방법을 배웠습니다.

* Azure Cosmos DB에 대한 자세한 내용은 [Azure Cosmos DB 서비스 페이지](https://azure.microsoft.com/services/cosmos-db/)를 참조하세요.
* Azure 인지 검색에 대한 자세한 내용은 [검색 서비스 페이지를](https://azure.microsoft.com/services/search/)참조하십시오.
