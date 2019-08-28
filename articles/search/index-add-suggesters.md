---
title: 인덱스에 대 한 형식 미리 쿼리 추가-Azure Search
description: 자동 완성 또는 자동 제안 쿼리 용어를 호출 하는 확인 기 및 공식화 요청을 만들어 Azure Search에서 미리 입력 쿼리 작업을 사용 하도록 설정 합니다.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648848"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search 앞에 있는 typeahead.js 인덱스에 확인 기를 추가 합니다.

**확인 기** 는 "검색 형식" 환경을 지 원하는 [Azure Search 인덱스](search-what-is-an-index.md) 의 구문입니다. 여기에는 typeahead.js 쿼리 입력을 사용 하도록 설정할 필드 목록이 포함 되어 있습니다. 인덱스 내에서 동일한 확인 기는 두 가지 형식 미리 변형 중 하나 또는 둘 모두를 지원 합니다. 즉, *자동 완성* 은 입력 하는 용어 또는 구를 완성 하 고 *제안* 에는 간단한 결과 목록이 제공 됩니다. 

다음 스크린샷은 형식 미리 기능을 보여 줍니다. 이 Xbox 검색 페이지에서 자동 완성 항목을 사용 하면 해당 쿼리에 대 한 새 검색 결과 페이지로 이동 하는 반면, 해당 특정 게임의 페이지로 이동 하는 실제 결과가 제안 됩니다. 자동 완성은 검색 표시줄의 한 항목으로 제한 하거나 여기에 표시 된 것과 같은 목록을 제공할 수 있습니다. 제안에 대 한 결과를 가장 잘 설명 하는 문서의 모든 부분을 표시할 수 있습니다.

![자동 완성 및 제안 된 쿼리를 시각적으로 비교](./media/index-add-suggesters/visual-comparison-suggest-complete.png "자동 완성 및 제안 된 쿼리를 시각적으로 비교")

Azure Search에서 이러한 동작을 구현 하기 위해 인덱스 및 쿼리 구성 요소가 있습니다. 

+ 인덱스 구성 요소는 확인 기입니다. Portal, REST API 또는 .NET SDK를 사용 하 여 확인 기를 만들 수 있습니다. 

+ 쿼리 구성 요소는 쿼리 요청 (제안 또는 자동 완성 작업)에 지정 된 작업입니다. 

유형별 검색 지원은 필드별으로 사용 하도록 설정 됩니다. 스크린샷에 표시 된 것과 비슷한 환경을 원할 경우 동일한 검색 솔루션 내에서 형식 미리 동작을 구현할 수 있습니다. 두 요청은 특정 인덱스의 *문서* 컬렉션을 대상으로 하 고 사용자가 적어도 3 개의 문자 입력 문자열을 제공한 후 응답이 반환 됩니다.

## <a name="create-a-suggester"></a>제안기 만들기

확인 기에는 몇 가지 속성이 있지만이 속성은 기본적으로 미리 사용자가 사용할 수 있는 필드의 컬렉션입니다. 예를 들어 여행 앱은 목적지, 도시 및 명소에 대한 자동 완성(typeahead) 검색을 사용하도록 설정할 수도 있습니다. 따라서 세 필드는 모두 fields 컬렉션으로 이동 합니다.

확인 기를 만들려면 인덱스 스키마에 하나를 추가 합니다. 인덱스에 하나의 확인 기를 포함할 수 있습니다 (특히, 확인 기 collection의 한 확인 기). 

### <a name="use-the-rest-api"></a>REST API 사용

REST API에서 [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index)를 통해 확인 기를 추가할 수 있습니다. 

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
확인 기을 만든 후 쿼리 논리에 [제안 api](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 api](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 를 추가 하 여 기능을 호출 합니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

에서 C# [확인 기 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)를 정의 합니다. `Suggesters`는 컬렉션 이지만 하나의 항목만 사용할 수 있습니다. 

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

## <a name="property-reference"></a>속성 참조

확인 기에 대 한 주의 사항은 이름 (확인 기은 요청 시 이름으로 참조 됨), searchMode (현재는 한 가지, "analyzingInfixMatching"), 그리고 typeahead.js를 사용할 수 있는 필드 목록입니다. 

확인기를 정의하는 속성은 다음과 같습니다.

|속성      |Description      |
|--------------|-----------------|
|`name`        |확인 기의 이름입니다. [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete)를 호출할 때 확인 기의 이름을 사용 합니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 또한 사용자 지정 언어 분석기가 설정되지 않은 필드만 사용할 수 있습니다.<p/>검색 표시줄이 나 드롭다운 목록에서 완성 된 문자열 인지 여부에 관계 없이 예상 및 적절 한 응답에 대해 자신을 지 원하는 필드만 지정 합니다.<p/>호텔 이름은 전체 자릿수가 있으므로 좋은 후보입니다. 설명 및 주석과 같은 자세한 정보 필드에는 너무 조밀 하 게 표시 됩니다. 마찬가지로 범주 및 태그와 같은 반복적인 필드도 효과적이 지 않습니다. 예제에는 여러 필드를 포함할 수 있음을 보여 주는 "category"가 포함 되어 있습니다. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>확인 기의 SourceFields 분석

Azure Search은 필드 내용을 분석 하 여 개별 용어에 대 한 쿼리를 사용할 수 있도록 합니다. 확인 기는 전체 용어 외에도 접두사를 인덱싱해야 하므로 원본 필드에 대 한 추가 분석이 필요 합니다. 사용자 지정 분석기 구성은 다양 한 토크 나이저 및 필터를 결합할 수 있습니다. 일반적으로 제안에 필요한 접두사를 생성 하는 방식으로는 불가능 합니다. 따라서 **Azure Search는 사용자 지정 분석기가 포함 된 필드가 확인 기에 포함 되지 않도록**합니다.

> [!NOTE] 
>  위의 제한 사항을 해결 하는 권장 방법은 동일한 콘텐츠에 대해 2 개의 개별 필드를 사용 하는 것입니다. 이렇게 하면 필드 중 하나에 확인 기를 사용 하 고 다른 하나는 사용자 지정 분석기 구성을 사용 하 여 설정할 수 있습니다.

## <a name="when-to-create-a-suggester"></a>확인 기를 만들어야 하는 경우

인덱스 다시 작성을 방지 하려면에 `sourceFields` 지정 된 확인 기 및 필드를 동시에 만들어야 합니다.

기존 필드가에 `sourceFields`포함 된 기존 인덱스에 확인 기를 추가 하는 경우 필드 정의는 근본적으로 변경 되며 다시 작성 해야 합니다. 자세한 내용은 [Azure Search 인덱스를 다시 작성 하는 방법](search-howto-reindex.md)을 참조 하세요.

## <a name="how-to-use-a-suggester"></a>확인 기를 사용 하는 방법

앞에서 설명한 것 처럼 제안 된 쿼리, 자동 완성 또는 둘 다에 대해 확인 기를 사용할 수 있습니다. 

확인 기는 작업과 함께 요청에서 참조 됩니다. 예를 들어 REST 가져오기 호출에서 documents 컬렉션에 `suggest` 또는 `autocomplete` 를 지정 합니다. REST의 경우 확인 기를 만든 후 쿼리 논리에서 [제안 api](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 api](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 를 사용 합니다.

.NET의 경우 [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) 또는 [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)를 사용 합니다.

두 요청을 보여 주는 예제는 [Azure Search의 자동 완성 및 제안 추가 예제](search-autocomplete-tutorial.md)를 참조 하세요.

## <a name="sample-code"></a>샘플 코드

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 샘플은 및 Java C# 코드를 모두 포함 하며 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 

샌드박스 Azure Search 서비스 및 미리 로드 된 인덱스를 사용 하므로 F5 키를 눌러 실행 해야 합니다. 구독 또는 로그인이 필요 하지 않습니다.

## <a name="next-steps"></a>다음 단계

요청을 작성 하는 방법을 보려면 다음 예제를 따르는 것이 좋습니다.

> [!div class="nextstepaction"]
> [제안 및 자동 완성 예제](search-autocomplete-tutorial.md) 
