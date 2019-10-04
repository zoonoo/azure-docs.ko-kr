---
title: 인덱스에 대 한 형식 미리 쿼리 추가-Azure Search
description: 자동 완성 또는 자동 제안 쿼리 용어를 호출 하는 확인 기 및 공식화 요청을 만들어 Azure Search에서 미리 입력 쿼리 작업을 사용 하도록 설정 합니다.
ms.date: 09/30/2019
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
ms.openlocfilehash: d3f934bea5df821e51a4747170af4f7efd1eaacc
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828287"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search 앞에 있는 typeahead.js 인덱스에 확인 기를 추가 합니다.

Azure Search에서 "검색 형식" 또는 형식 미리 기능은 [검색 인덱스](search-what-is-an-index.md)에 추가 하는 **확인 기** 구문을 기반으로 합니다. 하나 이상의 필드 목록으로,이를 미리 사용할 수 있습니다.

확인 기은 입력 하는 용어 또는 문구를 완성 하는 *자동 완성*및 일치 하는 문서에 대 한 간단한 목록을 반환 하는 *제안을* 지원 합니다.  

샘플 [에서 C# 첫 번째 앱 만들기](tutorial-csharp-type-ahead-and-suggestions.md) 의 다음 스크린샷은 앞의 형식입니다. 자동 완성 기능을 사용 하면 사용자가 검색 상자에 입력할 수 있는 항목이 결정 됩니다. 실제 입력은 "자"로 서 "쌍"을 사용 하 여 "쌍"으로 마무리 하는 "휴먼"입니다. 제안 사항은 드롭다운 목록에서 시각화 됩니다. 제안에 대 한 결과를 가장 잘 설명 하는 문서의 모든 부분을 표시할 수 있습니다. 이 예에서는 호텔 이름이 제안 됩니다. 

![자동 완성 및 제안 된 쿼리를 시각적으로 비교](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "하 여 자동 완성 및 제안 된 쿼리를 시각적으로 비교")

Azure Search에서 이러한 동작을 구현 하기 위해 인덱스 및 쿼리 구성 요소가 있습니다. 

+ 인덱스에서 확인 기를 인덱스에 추가 합니다. 포털, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)또는 [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)를 사용할 수 있습니다. 이 문서의 나머지 부분에서는 확인 기를 만드는 방법에 대해 집중적으로 설명 합니다. 

+ 쿼리 요청에서 [아래에 나열 된 api](#how-to-use-a-suggester)중 하나를 호출 합니다.

유형별 검색 지원은 필드별으로 사용 하도록 설정 됩니다. 스크린샷에 표시 된 것과 비슷한 환경을 원할 경우 동일한 검색 솔루션 내에서 형식 미리 동작을 구현할 수 있습니다. 두 요청은 특정 인덱스의 *문서* 컬렉션을 대상으로 하 고 사용자가 적어도 3 개의 문자 입력 문자열을 제공한 후 응답이 반환 됩니다.

## <a name="create-a-suggester"></a>제안기 만들기

확인 기에는 몇 가지 속성이 있지만이 속성은 기본적으로 미리 사용자가 사용할 수 있는 필드의 컬렉션입니다. 예를 들어 여행 앱은 목적지, 도시 및 명소에 대한 자동 완성(typeahead) 검색을 사용하도록 설정할 수도 있습니다. 따라서 세 필드는 모두 fields 컬렉션으로 이동 합니다.

확인 기를 만들려면 인덱스 스키마에 하나를 추가 합니다. 인덱스에 하나의 확인 기를 포함할 수 있습니다 (특히, 확인 기 collection의 한 확인 기). 

### <a name="when-to-create-a-suggester"></a>확인 기를 만들어야 하는 경우

확인 기를 만드는 가장 좋은 시기는 필드 정의를 만드는 것입니다.

기존 필드를 사용 하 여 확인 기를 만들려고 하면 API에서이를 허용 하지 않습니다. 두 개 이상의 문자 조합에서 부분 용어를 전체 용어와 함께 토큰화 하는 경우 인덱싱 중에 형식 미리 텍스트가 생성 됩니다. 기존 필드가 이미 토큰화 된 경우 확인 기에 추가 하려면 인덱스를 다시 작성 해야 합니다. 인덱스 다시 작성에 대 한 자세한 내용은 [Azure Search 인덱스를 다시 작성 하는 방법](search-howto-reindex.md)을 참조 하세요.

### <a name="create-using-the-rest-api"></a>REST API를 사용 하 여 만들기

REST API에서 [Create index](https://docs.microsoft.com/rest/api/searchservice/create-index) 또는 [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index)를 통해 확인 기를 추가 합니다. 

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


### <a name="create-using-the-net-sdk"></a>.NET SDK를 사용 하 여 만들기

에서 C# [확인 기 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)를 정의 합니다. `Suggesters`은 컬렉션 이지만 하나의 항목만 사용할 수 있습니다. 

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

### <a name="property-reference"></a>속성 참조

|속성      |설명      |
|--------------|-----------------|
|`name`        |확인 기의 이름입니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 필드의 형식은 `Edm.String` 및 `Collection(Edm.String)` 이어야 합니다. 필드에 분석기를 지정 하는 경우 사용자 지정 분석기가 아닌 [이 목록](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 에서 명명 된 분석기 여야 합니다.<p/>검색 표시줄이 나 드롭다운 목록에서 완성 된 문자열 인지 여부에 관계 없이 필요한 적절 한 응답에 대해 자신을 지 원하는 필드만 지정 하는 것이 가장 좋습니다.<p/>호텔 이름은 전체 자릿수가 있으므로 좋은 후보입니다. 설명 및 주석과 같은 자세한 정보 필드에는 너무 조밀 하 게 표시 됩니다. 마찬가지로 범주 및 태그와 같은 반복적인 필드도 효과적이 지 않습니다. 예제에는 여러 필드를 포함할 수 있음을 보여 주는 "category"가 포함 되어 있습니다. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>확인 기의 sourceFields에 대 한 분석기 제한 사항

Azure Search은 필드 내용을 분석 하 여 개별 용어에 대 한 쿼리를 사용할 수 있도록 합니다. 확인 기는 전체 용어 외에도 접두사를 인덱싱해야 하므로 원본 필드에 대 한 추가 분석이 필요 합니다. 사용자 지정 분석기 구성은 다양 한 토크 나이저 및 필터를 결합할 수 있습니다. 일반적으로 제안에 필요한 접두사를 생성 하는 방식으로는 불가능 합니다. 따라서 Azure Search는 사용자 지정 분석기가 포함 된 필드가 확인 기에 포함 되지 않도록 합니다.

> [!NOTE] 
>  위의 제한 사항을 해결 해야 하는 경우 동일한 내용에 대해 두 개의 개별 필드를 사용 합니다. 이렇게 하면 필드 중 하나에 확인 기를 사용할 수 있고 다른 하나는 사용자 지정 분석기 구성으로 설정할 수 있습니다.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>쿼리에서 확인 기 사용

확인 기을 만든 후 쿼리 논리에서 적절 한 API를 호출 하 여 기능을 호출 합니다. 

+ [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API 사용법은 다음 REST API 자동 완성 호출에 설명 되어 있습니다. 이 예제에서는 두 가지 내용 있습니다. 첫째, 모든 쿼리와 마찬가지로 작업은 인덱스의 문서 컬렉션에 대 한 것입니다. 둘째, 쿼리 매개 변수를 추가할 수 있습니다. 많은 쿼리 매개 변수가 두 Api에 공통적으로 있지만 목록은 서로 다릅니다.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

인덱스에 확인 기가 정의 되어 있지 않으면 자동 완성 또는 제안에 대 한 호출이 실패 합니다.

## <a name="sample-code"></a>샘플 코드

+ [첫 번째 앱 만들기 샘플 C# 에서는](tutorial-csharp-type-ahead-and-suggestions.md) 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 이 코드 샘플은 샌드박스 Azure Search 서비스에서 실행 되며 미리 로드 된 호텔 인덱스를 사용 하므로 F5 키를 눌러 응용 프로그램을 실행 하면 됩니다. 구독 또는 로그인이 필요 하지 않습니다.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 는 및 Java 코드를 모두 C# 포함 하는 이전 샘플입니다. 또한 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 이 코드 샘플에서는 호스팅된 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 데이터를 사용 합니다. 


## <a name="next-steps"></a>다음 단계

요청을 작성 하는 방법을 보려면 다음 예제를 따르는 것이 좋습니다.

> [!div class="nextstepaction"]
> [제안 및 자동 완성 예제](search-autocomplete-tutorial.md) 
