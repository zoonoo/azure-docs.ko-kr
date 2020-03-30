---
title: JSON Blob 검색
titleSuffix: Azure Cognitive Search
description: Azure 인지 검색 Blob 인덱서를 사용하여 텍스트 콘텐츠에 대한 Azure JSON Blob을 크롤링합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533964"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure 인지 검색에서 Blob 인덱서를 사용하여 JSON Blob을 인덱싱하는 방법

이 문서에서는 Azure Blob 저장소의 JSON 문서에서 구조화 된 콘텐츠를 추출 하 고 Azure 인지 검색에서 검색할 수 있도록 Azure 인지 검색 Blob [인덱서를](search-indexer-overview.md) 구성 하는 방법을 보여 주며 있습니다. 이 워크플로는 Azure 인지 검색 인덱스를 만들고 JSON Blob에서 추출된 기존 텍스트로 로드합니다. 

[포털](#json-indexer-portal), [REST API](#json-indexer-rest) 또는 [.NET SDK](#json-indexer-dotnet)를 사용하여 JSON 콘텐츠를 인덱싱할 수 있습니다. 모든 접근 방식에 공통적인 것은 JSON 문서가 Azure Storage 계정의 Blob 컨테이너에 있다는 것입니다. 다른 비 Azure 플랫폼에서 JSON 문서를 푸시하는 방법에 대한 지침은 [Azure 인지 검색의 데이터 가져오기를](search-what-is-data-import.md)참조하십시오.

Azure Blob 저장소의 JSON Blob은 일반적으로 단일 JSON 문서(구문 분석 모드) 또는 JSON 엔터티 의 컬렉션입니다. `json` 컬렉션의 경우 Blob에는 잘 구성된 JSON 요소(구문 분석 `jsonArray`모드)가 있을 수 있습니다. **array** Blob은 줄 바이라고 구분하여 구분되는 여러 개별 JSON 엔터티로 `jsonLines`구성될 수도 있습니다(구문 분석 모드는). 요청의 **구문 분석Mode** 매개 변수는 출력 구조를 결정합니다.

> [!NOTE]
> 단일 Blob에서 여러 검색 문서를 인덱싱하는 것에 대한 자세한 내용은 [일대다 인덱싱](search-howto-index-one-to-many-blobs.md)을 참조하십시오.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>포털 사용

JSON 문서를 인덱싱하는 가장 쉬운 방법은 [Azure Portal](https://portal.azure.com/)에서 마법사를 사용하는 것입니다. Azure Blob 컨테이너의 메타데이터를 구문 분석하여 [**데이터 가져오기**](search-import-data-portal.md) 마법사는 기본 인덱스를 만들고, 원본 필드를 대상 인덱스 필드에 매핑하고, 인덱스를 로드하는 과정을 단일 작업으로 진행할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

대기 시간을 낮추고 대역폭 요금을 피하기 위해 Azure 인지 검색 및 Azure 저장소모두에 동일한 지역 또는 위치를 사용하는 것이 좋습니다.

### <a name="1---prepare-source-data"></a>1 - 원본 데이터 준비

[Azure 포털에 로그인하고](https://portal.azure.com/) 데이터를 포함하는 [Blob 컨테이너를 만듭니다.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) 공용 액세스 수준은 유효한 값으로 설정할 수 있습니다.

**가져오기 데이터** 마법사에서 데이터를 검색하려면 저장소 계정 이름, 컨테이너 이름 및 액세스 키가 필요합니다.

### <a name="2---start-import-data-wizard"></a>2 - 데이터 가져오기 마법사 시작

검색 서비스의 개요 페이지에서 명령 모음에서 [마법사를 시작할](search-import-data-portal.md) 수 있습니다.

   ![포털에서 데이터 명령 가져오기](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

### <a name="3---set-the-data-source"></a>3 - 데이터 원본 설정

**데이터 원본** 페이지에서 원본은 다음 사양을 가진 **Azure Blob 스토리지**여야 합니다.

+ **추출할 데이터**는 *콘텐츠 및 메타데이터*여야 합니다. 이 옵션을 선택하면 마법사가 인덱스 스키마를 유추하고 가져올 필드를 매핑할 수 있습니다.
   
+ **구문 분석 모드는** *JSON,* *JSON 배열* 또는 *JSON 라인으로*설정되어야 합니다. 

  *JSON*은 각 Blob을 단일 검색 문서로 구분하여 검색 결과에서 개별 항목으로 표시합니다. 

  *JSON 배열은* 잘 형성된 JSON 데이터를 포함하는 Blob용으로, 잘 형성된 JSON은 개체 배열에 해당하거나 개체 배열인 속성을 가지며 각 요소를 독립실행형 독립 검색 문서로 관절하도록 합니다. Blob이 복잡하고 *JSON 배열*을 선택하지 않을 경우 전체 Blob이 단일 문서로 수집됩니다.

  *JSON 줄은* 각 엔터티를 독립 실행형 독립 검색 문서로 관절할 수 있도록 줄바호로 구분된 여러 JSON 엔터티로 구성된 Blob에 대한 것입니다. Blob이 복잡하고 *JSON 선* 구문 분석 모드를 선택하지 않으면 전체 Blob이 단일 문서로 통합됩니다.
   
+ **스토리지 컨테이너**는 스토리지 계정 및 컨테이너 또는 컨테이너로 확인되는 연결 문자열을 지정해야 합니다. Blob service 포털 페이지에서 연결 문자열을 가져올 수 있습니다.

   ![Blob 데이터 원본 정의](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - 마법사에서 "콘텐츠 보강" 페이지 건너뛰기

인지 능력(또는 농축)을 추가하는 것은 가져오기 요건이 아닙니다. 인덱싱 파이프라인에 [AI 보강을 추가해야](cognitive-search-concept-intro.md) 하는 특별한 필요가 없는 경우 이 단계를 건너뛰어야 합니다.

단계를 건너뛰려면 페이지 하단의 파란색 버튼을 클릭하여 "다음" 및 "건너뛰기"를 클릭합니다.

### <a name="5---set-index-attributes"></a>5 - 인덱스 특성 설정

**인덱스** 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 메타데이터를 기반으로 원본 데이터를 샘플링하여 필드 목록을 생성할 수 있습니다. 

특성 열 상단의 확인란을 클릭하여 특성을 일괄 선택할 수 있습니다. 클라이언트 앱으로 반환되고 전체 텍스트 검색 처리가 적용되는 모든 필드에 대해 **검색 가능** 및 **검색 가능을** 선택합니다. 정수는 전체 텍스트또는 퍼지 검색 가능하지 않습니다(숫자는 그대로 평가되며 필터에서 종종 유용합니다).

자세한 내용은 [인덱스 특성](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) 및 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support) 설명을 검토합니다. 

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 이러한 필드를 편집할 수 없습니다.

   ![Blob 인덱스 정의](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 인덱서 만들기

완전히 지정된 마법사는 검색 서비스에서 세 개의 고유한 개체를 만듭니다. 데이터 원본 개체 및 인덱스 개체는 Azure Cognitive Search 서비스의 명명된 리소스로 저장됩니다. 마지막 단계는 인덱서 개체를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

인덱서에 익숙하지 않은 경우 *인덱서가* Azure Cognitive Search의 리소스로 검색 가능한 콘텐츠에 대한 외부 데이터 원본을 크롤링합니다. **가져오기 데이터** 마법사의 출력은 JSON 데이터 원본을 크롤링하고 검색 가능한 콘텐츠를 추출한 다음 Azure Cognitive Search의 인덱스로 가져오는 인덱서입니다.

   ![Blob 인덱서 정의](media/search-howto-index-json/import-wizard-json-indexer.png)

**확인**을 클릭하여 마법사를 실행하고 모든 개체를 만듭니다. 인덱싱이 즉시 시작됩니다.

포털 페이지에서 데이터 가져오기를 모니터링할 수 있습니다. 진행률 알림은 인덱싱 상태 및 업로드된 문서 수를 나타냅니다. 

인덱싱이 완료되면 [검색 탐색기](search-explorer.md)를 사용하여 인덱스를 쿼리할 수 있습니다.

> [!NOTE]
> 예상한 데이터가 표시되지 않으면 더 많은 필드에 더 많은 특성을 설정해야 할 수 있습니다. 방금 만든 인덱스 및 인덱서를 삭제하고 마법사를 다시 단계별로 단계별로 5단계에서 인덱스 특성에 대한 선택을 수정합니다. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API 사용

REST API를 사용하여 Azure Cognitive Search의 모든 인덱서에 공통적인 세 부분으로 구성된 워크플로에 따라 데이터 원본 을 만들고 인덱스를 만들고 인덱서를 만드는 세 부분으로 구성된 워크플로에 따라 JSON Blob을 인덱싱할 수 있습니다. Blob 저장소에서 데이터 추출은 인덱서 만들기 요청을 제출할 때 발생합니다. 이 요청이 완료되면 쿼리 가능한 인덱스가 표시됩니다. 

이 섹션의 끝에 있는 [REST 예제 코드를](#rest-example) 검토하여 세 개체를 모두 만드는 방법을 보여 주는 것입니다. 이 섹션에는 [JSON 구문 분석 모드,](#parsing-modes) [단일 Blob,](#parsing-single-blobs) [JSON 배열](#parsing-arrays)및 [중첩 배열에](#nested-json-arrays)대한 세부 정보도 포함되어 있습니다.

코드 기반 JSON 인덱싱의 경우 [Postman](search-get-started-postman.md) 및 REST API를 사용하여 다음 개체를 만듭니다.

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [데이터 소스](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [인덱서](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

작업 순서를 사용하려면 이 순서로 개체를 만들고 호출해야 합니다. 포털 워크플로와 달리 코드 접근 방식에는 **인덱서 만들기** 요청을 통해 전송된 JSON 문서를 수락할 수 있는 인덱스가 필요합니다.

Azure Blob 저장소의 JSON Blob은 일반적으로 단일 JSON 문서 또는 JSON "배열"입니다. Azure Cognitive Search의 Blob 인덱서에서는 요청에서 **구문 분석Mode** 매개 변수를 설정하는 방법에 따라 구성을 구문 분석할 수 있습니다.

| JSON 문서 | parsingMode | 설명 | 가용성 |
|--------------|-------------|--------------|--------------|
| Blob 당 하나 | `json` | JSON Blob을 텍스트의 단일 청크로 구문 분석합니다. 각 JSON Blob은 단일 Azure 인지 검색 문서가 됩니다. | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 모두에서 사용할 수 있습니다. |
| Blob 당 여러 개 | `jsonArray` | 배열의 각 요소가 별도의 Azure 인지 검색 문서가 되는 Blob에서 JSON 배열을 구문 분석합니다.  | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 모두에서 사용할 수 있습니다. |
| Blob 당 여러 개 | `jsonLines` | 줄 바래로 구분된 여러 JSON 엔터티("배열")가 포함된 Blob을 구문 분석하여 각 엔터티가 별도의 Azure Cognitive Search 문서가 됩니다. | 일반적으로 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 모두에서 사용할 수 있습니다. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - 요청에 대한 입력 어셈블

각 요청에 대해 POST 헤더에서 Azure Cognitive Search의 서비스 이름 및 관리 키와 Blob 저장소에 대한 저장소 계정 이름 및 키를 제공해야 합니다. [Postman을](search-get-started-postman.md) 사용하여 AZURE 인지 검색에 HTTP 요청을 보낼 수 있습니다.

다음 네 값을 메모장에 복사하여 요청에 붙여넣을 수 있습니다.

+ Azure 인지 검색 서비스 이름
+ Azure 인지 검색 관리자 키
+ Azure Storage 계정 이름
+ Azure 저장소 계정 키

포털에서 다음 값을 찾을 수 있습니다.

1. Azure 인지 검색에 대 한 포털 페이지에서 개요 페이지에서 검색 서비스 URL을 복사 합니다.

2. 왼쪽 탐색 창에서 **키를** 클릭한 다음 기본 키 또는 보조 키(동일)를 복사합니다.

3. 저장소 계정의 포털 페이지로 전환합니다. 왼쪽 탐색 창에서 **설정**에서 **액세스 키를 클릭합니다.** 이 페이지에서는 계정 이름과 키를 모두 제공합니다. 저장소 계정 이름과 키 중 하나를 메모장에 복사합니다.

### <a name="2---create-a-data-source"></a>2 - 데이터 원본 만들기

이 단계는 인덱서에서 사용하는 데이터 원본 연결 정보를 제공합니다. 데이터 원본은 연결 정보를 유지한다는 Azure Cognitive Search의 명명된 개체입니다. 데이터 원본 형식은 `azureblob`인덱서에서 호출되는 데이터 추출 동작을 결정합니다. 

서비스 이름, 관리자 키, 저장소 계정 및 계정 키 자리 표시자에 대해 유효한 값을 대체합니다.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - 대상 검색 인덱스 만들기 

인덱서는 인덱스 스키마과 쌍을 이룹니다. 포털이 아닌 API를 사용하는 경우 인덱서 작업에서 지정할 수 있도록 사전에 인덱스를 준비합니다.

인덱스는 Azure 인지 검색에 검색 가능한 콘텐츠를 저장합니다. 인덱스를 만들려면 문서의 필드, 특성 및 검색 환경을 형성하는 기타 항목을 지정하는 스키마를 제공합니다. 원본과 동일한 필드 이름 및 데이터 형식을 갖는 인덱스를 만드는 경우 인덱서는 사용자에게 명시적으로 필드를 매핑하도록 요구하지 않고, 원본 및 대상 필드를 일치시킵니다.

다음 예제는 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 요청을 보여 줍니다. 인덱스에는 blob에서 추출된 텍스트를 저장하기 위한 검색 가능한`content` 필드가 표시됩니다.   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - 인덱서 구성 및 실행

인덱스 및 데이터 원본과 마찬가지로 인덱서도 Azure Cognitive Search 서비스에서 만들고 다시 사용하는 명명된 개체입니다. 인덱서를 만들기 위해 완전히 지정된 요청은 다음과 같이 보일 수 있습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

인덱서 구성은 요청본문에 있습니다. Azure Cognitive Search에 이미 있는 데이터 원본과 빈 대상 인덱스가 필요합니다. 

일정 및 매개 변수는 선택 사항입니다. 생략하면 인덱서가 구문 분석 `json` 모드로 사용하여 즉시 실행됩니다.

이 특정 인덱서에는 필드 매핑이 포함되지 않습니다. 인덱서 정의 내에서 원본 JSON 문서의 속성이 대상 검색 인덱스의 필드와 일치하는 경우 **필드 매핑을** 제외할 수 있습니다. 


### <a name="rest-example"></a>나머지 예

이 섹션은 개체를 만드는 데 사용되는 모든 요청을 요약한 것입니다. 구성 요소 부분에 대한 설명은 이 문서의 이전 섹션을 참조하십시오.

### <a name="data-source-request"></a>데이터 원본 요청

모든 인덱서에는 기존 데이터에 대한 연결 정보를 제공하는 데이터 원본 개체가 필요합니다. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>인덱스 요청

모든 인덱서에는 데이터를 수신하는 대상 인덱스가 필요합니다. 요청본문에는 검색 가능한 인덱스에서 원하는 동작을 지원하는 필드로 구성된 인덱스 스키마를 정의합니다. 인덱서를 실행할 때 이 인덱스는 비어 있어야 합니다. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>인덱서 요청

이 요청에는 완전히 지정된 인덱서가 표시됩니다. 여기에는 이전 예제에서 생략된 필드 매핑이 포함됩니다. 사용 가능한 기본값이 있는 한 "일정", "매개 변수" 및 "필드매핑"은 선택 사항입니다. "일정"을 생략하면 인덱서가 즉시 실행됩니다. "구문 분석 모드"를 생략하면 인덱스가 "json" 기본값을 사용합니다.

Azure 인지 검색에서 인덱서를 만들면 데이터 가져오기가 트리거됩니다. 그것은 즉시 실행, 그리고 그 후 일정에 당신이 하나를 제공 한 경우.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK 사용

.NET SDK는 REST API와 전체 패리티를 가합니다. 개념, 워크플로 및 요구 사항을 알아보려면 이전 REST API 섹션을 검토하는 것이 좋습니다. 그런 후, 다음 .NET API 참조 설명서를 참조하여 관리되는 코드에서 JSON 인덱서를 구현할 수 있습니다.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>구문 분석 모드

JSON Blob은 여러 양식을 가정할 수 있습니다. JSON 인덱서의 **구문 분석Mode** 매개 변수는 JSON Blob 콘텐츠가 Azure 인지 검색 인덱스에서 구문 분석 및 구조화되는 방법을 결정합니다.

| parsingMode | 설명 |
|-------------|-------------|
| `json`  | 각 Blob을 단일 문서로 인덱싱합니다. 이것이 기본값입니다. |
| `jsonArray` | Blob이 JSON 배열로 구성되고 배열의 각 요소가 Azure Cognitive Search에서 별도의 문서가 되어야 하는 경우 이 모드를 선택합니다. |
|`jsonLines` | Blob이 새 줄로 구분된 여러 JSON 엔터티로 구성되고 각 엔터티가 Azure Cognitive Search에서 별도의 문서가 되어야 하는 경우 이 모드를 선택합니다. |

문서를 검색 결과의 단일 항목으로 생각할 수 있습니다. 배열의 각 요소를 검색 결과에 독립 항목으로 표시하려면 또는 `jsonArray` `jsonLines` 옵션을 적절하게 사용합니다.

인덱서 정의 내에서 [필드 매핑](search-indexer-field-mappings.md)을 사용하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택할 수도 있습니다. 구문 분석 모드의 경우 `jsonArray` 배열이 하위 수준 속성으로 존재하는 경우 배열이 Blob 내에 배치되는 위치를 나타내는 문서 루트를 설정할 수 있습니다.

> [!IMPORTANT]
> 을 `json` `jsonArray` 사용하거나 `jsonLines` 구문 분석 모드를 사용하는 경우 Azure Cognitive Search는 데이터 원본의 모든 Blob에 JSON이 포함되어 있다고 가정합니다. 동일한 데이터 원본에서 JSON 및 비 JSON BLOB을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>구문 분석 단일 JSON blobs

기본적으로 [Azure 인지 검색 Blob 인덱서에서는](search-howto-indexing-azure-blob-storage.md) JSON Blob을 단일 텍스트 청크로 구문 분석합니다. JSON 문서의 구조를 유지하려는 경우가 많습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 인덱서는 JSON 문서를 단일 Azure 인지 검색 문서로 구문 분석합니다. 인덱서는 동일하게 명명되고 형식이 지정된 대상 인덱스 필드에 대해 원본의 "text", "datePublished" 및 "tags"를 일치시켜 인덱스를 로드합니다.

명시된 대로 필드 매핑이 필요하지 않습니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>구문 분석 JSON 어레이

또는 JSON 배열 옵션을 사용할 수 있습니다. 이 옵션은 Blob에 잘 *구성된 JSON 개체 배열이*포함되어 있고 각 요소가 별도의 Azure 인지 검색 문서가 되도록 하려는 경우에 유용합니다. 예를 들어 다음 JSON Blob이 주어지면 Azure Cognitive Search 인덱스에 각각 "id" 및 "텍스트" 필드가 있는 세 개의 개별 문서로 채울 수 있습니다.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다. parsingMode 매개 변수는 `jsonArray` 파서를 지정합니다. 올바른 파서를 지정하고 올바른 데이터 입력을 갖는 것은 JSON Blob 인덱싱을 위한 두 가지 배열별 요구 사항뿐입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

다시 말하지만 필드 매핑은 생략할 수 있습니다. 동일하게 명명된 "id" 및 "text" 필드를 포함하는 인덱스가 있는 경우 Blob 인덱서는 명시적인 필드 매핑 목록 없이 올바른 매핑을 유추할 수 있습니다.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>중첩 배열 구문 분석
중첩된 요소가 있는 JSON 배열의 `documentRoot` 경우 다중 레벨 구조를 나타내는 a를 지정할 수 있습니다. 예를 들어 blob은 다음과 같습니다.

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>줄 바선으로 구분된 구문 분석 Blob

Blob에 줄 바net로 구분된 여러 JSON 엔터티가 포함되어 있고 각 요소가 별도의 Azure 인지 검색 문서로 지정하려는 경우 JSON 줄 옵션을 선택할 수 있습니다. 예를 들어 다음과 같은 Blob(세 개의 서로 다른 JSON 엔터티가 있는 경우)이 주어지면 Azure Cognitive Search 인덱스에 각각 "id" 및 "text" 필드가 있는 세 개의 개별 문서로 채울 수 있습니다.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

JSON 선의 경우 인덱서 정의는 다음 예제와 유사해야 합니다. parsingMode 매개 변수는 `jsonLines` 파서를 지정합니다. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

다시 말하지만 `jsonArray` 구문 분석 모드와 유사하게 필드 매핑을 생략할 수 있습니다.

## <a name="add-field-mappings"></a>필드 매핑 추가

원본 및 대상 필드가 완벽하게 정렬되지 않은 경우 명시적 필드 간 연결을 위해 요청 본문에서 필드 매핑 섹션을 정의할 수 있습니다.

현재 Azure Cognitive Search는 기본 데이터 형식, 문자열 배열 및 GeoJSON 포인트만 지원하므로 임의JSON 문서를 직접 인덱싱할 수 없습니다. 그러나 **필드 매핑** 을 사용하여 JSON 문서의 부분을 선택하고 검색 문서의 최상위 필드로 이 부분을 "올릴" 수 있습니다. 필드 매핑 기본 사항에 대해 자세히 알아보려면 [Azure 인지 검색 인덱서의 필드 매핑을](search-indexer-field-mappings.md)참조하십시오.

예제 JSON 문서 다시 방문:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

매핑의 원본 필드 이름은 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 필드 매핑 경로의 원본 필드 이름이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 다른 스키마를 사용하여 문서를 지원할 수 있도록 이 작업을 수행합니다(일반적인 사용 사례). 유효성을 검사하지 않기 때문에 필드 매핑 사양에 오타가 발생하지 않도록 주의해야 합니다.
>
>

## <a name="see-also"></a>참조

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure 인지 검색을 사용하여 Azure Blob 저장소 인덱싱](search-howto-index-json-blobs.md)
+ [Azure 인지 검색 Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob 저장소에서 반구조화 된 데이터 검색](search-semi-structured-data.md)
