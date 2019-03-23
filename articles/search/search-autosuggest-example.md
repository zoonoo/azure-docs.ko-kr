---
title: 검색 상자-Azure Search에 드롭다운 용어를 추가 하는 것에 대 한 예제 autosuggest
description: 확인 기를 만들고 Azure Search에서 제안 된 쿼리를 호출 하는 요청을 공식화 하 여 제안 된 쿼리 입력을 추가 합니다.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373077"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>예제: 추가 드롭다운 쿼리 선택 항목에 대 한 자동 제안

검색 용어 입력에는 제안 된 쿼리 용어의 드롭다운 목록을 포함할 수 있습니다. 상용 검색 엔진에서이 기능을 살펴본 및 Azure Search를 사용 하 여 비슷한 환경을 구현할 수 있습니다는 [suggester 구문을](index-add-suggesters.md) 및 쿼리 요청에 제안 작업 합니다. 이 문서에서는 예제를 사용 하 여 이미 정의한 하는 확인 기를 사용 하 여 autosuggest 쿼리의 공식화를 보여 줍니다. 

## <a name="rest-api"></a>REST API

사용할 수 있습니다 [Postman](search-fiddler.md) 또는 [PowerShell](search-create-index-rest-api.md) 하며 [REST API](https://docs.microsoft.com/rest/api/searchservice/) 이 있지만, 결과 사용해 JSON 문서로 반환 될 됩니다. 더 현실적이 고 visual 경험을 사용 하 여 확인할 수 있습니다 합니다 [자동 완성에 대 한 코드 샘플](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)합니다.

### <a name="1---index-with-a-suggester-construct"></a>1-suggester 구문을 사용 하 여 인덱스 합니다.

Autosuggest로 시작 하는 [suggester 구문을](index-add-suggesters.md) 드롭다운 값에 기여할 필드로 구성 된 인덱스에 추가 합니다. 다음 스키마에 지정 제안 된 쿼리를 작성할 수 같은 hotelName 및 범주 필드에서 값을 사용 하 여.

빠른 시작에 최소한의 샘플 데이터 집합으로 가정 하 고 호텔 이름에 "멋진 유지" 및 "Roach motel"을 포함 하 고 범주 "Luxury" 및 "Budget"를 포함 합니다.

호텔 인덱스에 이미 있는 경우 삭제 하 고 다음 스키마를 사용 하 여 다시 만듭니다. 이 스키마는 확인 기를 추가합니다. 도 데이터를 다시 로드 해야 합니다.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2-제안 연산자를 사용 하 여 쿼리 합니다.

Suggester를 사용 하 여 호출 autosuggest를 보내야 하는 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) GET 또는 POST를 사용 하 여 요청 합니다. 이러한 요청을 search 서비스에서 처음 세 문자 수신 되 면 잠재적 일치 항목에 대 한 있는지 검색 합니다. 

요청 헤더를 설정 **api 키** 는 관리자 또는 쿼리 키 및 **Content-type** application/json으로 합니다. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

다음 응답을 반환 하는 확인 기 (같은 hotelName 및 범주)에 포함 된 모든 필드가 요청 검색:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

예상된 결과 전달할 클라이언트 코드는 검색 표시줄 아래 드롭다운 목록으로 결과 렌더링 합니다.

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1-suggester 구문을 사용 하 여 인덱스 합니다.

.NET SDK에서 사용 된 [Suggester 클래스](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)합니다. 확인 기 컬렉션 이지만 하나씩만 사용할 수 있습니다.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2-제안 메서드를 사용 하 여 쿼리 합니다.

합니다 [DocumentsOperationsExtensions.Suggest 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) 제안 된 쿼리 문자열을 반환 하는 데 사용 됩니다.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>참고 항목

+ [Postman을 사용 하 여 REST API 탐색](search-fiddler.md)
+ [예제: Autocomplete](search-autocomplete-tutorial.md)
+ [확인 기를 인덱스에 추가](index-add-suggesters.md)
+ [.NET을 사용 하는 확인 기 클래스를 추가 합니다.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [GET 또는 POST (REST API)를 사용 하 여 제안을 호출합니다](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [SuggestWithHttpMessagesAsync (.NET)를 사용 하 여 제안을 호출](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) 또는 