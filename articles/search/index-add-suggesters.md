---
title: -Azure Search 인덱스에 미리 입력 쿼리를 추가 합니다.
description: 확인 기를 만들고 쿼리 용어를 autosuggested 또는 자동 완성을 호출 하는 요청을 공식화 하 여 Azure Search의 미리 입력 쿼리 작업을 사용 합니다.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370655"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search의 미리 입력에 대 한 인덱스를 확인 기 추가

A **suggester** 의 구조 이며는 [Azure Search 인덱스](search-what-is-an-index.md) "검색---입력할 때" 환경을 지 원하는 합니다. 이 미리 입력 쿼리 입력을 사용 하도록 설정 하려는 필드의 목록을 포함 합니다. 두 가지 미리 입력 변형이 있습니다: [ *자동 완성* ](search-autocomplete-tutorial.md) 단어 또는 구를 입력 하는 완료 [ *autosuggest* ](search-autosuggest-example.md) 짧은 제공 용어 또는 구를 입력 쿼리로 선택할 수 있는 목록입니다. 상용 검색 엔진에서 하기 전에 이러한 동작은 틀림 살펴보았습니다.

![자동 완성 visual 비교 및 autosuggest](./media/index-add-suggesters/visual-comparison-suggest-complete.png "자동 완성 Visual 비교 autosuggest 및")

Azure Search에서 이러한 동작을 구현 하는 인덱스 및 쿼리 구성 요소입니다. 

+ 인덱스에는 확인 기를 추가 합니다. 포털, REST API 또는.NET SDK는 확인 기를 만들려면 사용할 수 있습니다. 

+ 쿼리의 autosuggest 또는 자동 완성 동작을 지정 합니다. 

> [!Important]
> 자동 완성은 현재 미리 보기 REST Api에서에서 사용할 수 있는 미리 보기 및.NET SDK 및 프로덕션 응용 프로그램에 대 한 지원 되지 않습니다. 

미리 입력 지원 필드 별로에 사용 됩니다. 원하는 환경을 스크린샷에 표시 된 것과 비슷한 경우 동일한 검색 솔루션 내에서 모두 미리 입력 동작을 구현할 수 있습니다. 두 요청 대상 합니다 *문서* 특정 인덱스 및 응답의 컬렉션을 사용자가 최소한 세 개의 입력된 문자열을 제공한 후에 반환 됩니다.

## <a name="create-a-suggester"></a>Suggester를 만들려면

Suggester에 몇 가지 속성으로 주로를 사용할 수 있도록 하는 미리 입력 환경을 필드의 컬렉션입니다. 예를 들어 여행 앱은 목적지, 도시 및 명소에 대한 자동 완성(typeahead) 검색을 사용하도록 설정할 수도 있습니다. 따라서 fields 컬렉션에는 세 개의 필드가 모두가 이동 됩니다.

Suggester를 만들려면 인덱스 스키마에 하나를 추가 합니다. 인덱스에서 하나의 확인 기를 사용할 수 있습니다 (특히, suggesters 컬렉션에 suggester). 

REST API를 통해 확인 기를 추가할 수 있습니다 [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) 하거나 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)합니다. 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

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

확인 기에 대 한 주의할 점이 점 (확인 기는 요청에 대 한 이름으로 참조 됩니다.) 이름는 searchMode (현재 하나만, "analyzingInfixMatching"), 및 미리 입력이 사용 되는 필드의 목록입니다. 

제안기가 만들어지면 쿼리 논리에 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions)를 추가하여 기능을 호출합니다.

### <a name="property-reference"></a>속성 참조

확인기를 정의하는 속성은 다음과 같습니다.

|자산      |설명      |
|--------------|-----------------|
|`name`        |확인 기의 이름입니다. 호출할 때 suggester의 이름을 사용 합니다 [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [REST API (미리 보기) 자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete)합니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 또한 사용자 지정 언어 분석기가 설정되지 않은 필드만 사용할 수 있습니다.<p/>드롭다운 목록 또는 검색 표시줄에서 완료 된 문자열 인지를 예상 하 고 적절 한 응답을 하는 필드에만 지정 합니다.<p/>호텔 이름 정밀도 있기 때문에 적합 합니다. 설명 및 설명 같은 세부 정보 표시 필드는 답답한입니다. 마찬가지로, 범주 / 태그 등의 반복 필드는 떨어집니다. 예제에서는 포함 "category" 그래도 여러 필드를 포함할 수 있는지를 보여 줍니다. |

### <a name="index-rebuilds-for-existing-fields"></a>기존 필드에 대 한 인덱스 다시 작성

필드를 포함 하는 확인 기 및 기존 인덱스에는 확인 기를 추가 하거나 해당 필드 컴퍼지션을 변경 하는 경우에 인덱스를 다시 작성 해야 가능성이 높습니다.

| 조치 | 영향 |
|--------|--------|
| 새 필드를 만들고 동일한 업데이트에서 새 suggester를 동시에 만들기 | 최소 영향입니다. 인덱스가 이전에 추가한 필드가 있는 경우 새 필드 및 새 suggester를 추가할 기존 필드에 아무런 영향이 없습니다. |
| Suggester를 기존 필드를 추가 합니다. | 높은 영향입니다. 필드 정의 변경 하는 필드 추가, 필요는 [전체를 다시 빌드해야](search-howto-reindex.md)합니다.|

## <a name="use-a-suggester"></a>Suggester를 사용 합니다.

에서 설명한 대로 autosuggest, 자동 완성, 또는 둘 다 suggester를 사용할 수 있습니다. 

Suggester 작업에서는 요청에 대해 참조 됩니다. 예를 들어 위한 GET REST 호출에서 하나를 지정 `suggest` 또는 `autocomplete` 문서 컬렉션에 있습니다. Rest의 경우 suggester를 만든 후 사용 합니다 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 API (미리 보기)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 쿼리 논리에서입니다.

For.NET을 사용 하 여 [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) 하거나 [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet)합니다.

각 요청을 보여 주는 예제:

+ [추가 드롭다운 쿼리 선택 항목에 대 한 자동 제안](search-autosuggest-example.md)

+ [Azure Search에서 부분 용어 입력에 자동 완성 기능을 추가](search-autocomplete-tutorial.md) (미리 보기)에서 

## <a name="sample-code"></a>샘플 코드

합니다 [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 샘플 모두 포함 C# 및 Java 코드를 확인 기 생성을 방법을 보여 줍니다 자동 제안, 자동 완성 및 패싯 탐색 합니다. 

샌드박스 Azure Search 서비스를 사용 하며 미리 로드 된 인덱스를 갖도록 모든 작업을 수행 하는 실행 하려면 F5 키를 누릅니다. 구독 또는 로그인 필요 없습니다.

## <a name="next-steps"></a>다음 단계

요청을 공식화 됩니다 하는 방법을 보려면 다음 예제를 검토 합니다.

+ [Autosuggested 쿼리 예제](search-autosuggest-example.md) 
+ [자동 완성 쿼리 예제 (미리 보기)](search-autocomplete-tutorial.md) 
