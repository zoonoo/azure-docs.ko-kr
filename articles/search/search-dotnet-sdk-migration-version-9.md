---
title: Azure 검색 .NET SDK 버전 9로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Search .NET SDK 버전 9로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793011"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Azure 검색 .NET SDK 버전 9로 업그레이드

[Azure Search .NET SDK버전](https://aka.ms/search-sdk)7.0 미리 보기를 사용하는 경우 이 문서에서는 버전 9를 사용하도록 응용 프로그램을 업그레이드하는 데 도움이 됩니다.

> [!NOTE]
> 버전 8.0 미리 보기를 사용하여 아직 일반적으로 사용할 수 없는 기능을 평가하려는 경우 이 문서의 지침에 따라 이전 버전에서 8.0 미리 보기로 업그레이드할 수도 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search .NET SDK의 버전 9에는 이전 버전의 많은 변경 내용이 포함되어 있습니다. 이들 중 일부는 주요 변경 내용이지만 코드에 대해 비교적 사소한 변경만 있으면 됩니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 버전 4.0 미리 보기를 사용하는 경우 먼저 버전 5로 업그레이드한 다음 버전 9로 업그레이드해야 합니다. 지침은 [Azure 검색 .NET SDK 버전 5로 업그레이드를](search-dotnet-sdk-migration-version-5.md) 참조하십시오.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>버전 9의 새로운 내용
Azure Search .NET SDK의 버전 9는 Azure 검색 REST API의 최신 일반적으로 사용 가능한 버전, 특히 2019-05-06을 대상으로 합니다. 이 버전이 있으면 다음을 비롯한 Azure Search의 새 기능을 .NET 애플리케이션에서 사용할 수 있습니다.

* [AI 보강은](cognitive-search-concept-intro.md) 이미지, Blob 및 기타 구조화되지 않은 데이터 원본에서 텍스트를 추출하여 콘텐츠를 보강하여 Azure Search 인덱스에서 더 쉽게 검색할 수 있도록 하는 기능입니다.
* 복잡한 [형식에](search-howto-complex-data-types.md) 대한 지원을 사용하면 Azure Search 인덱스에서 중첩된 거의 모든 JSON 구조를 모델링할 수 있습니다.
* [자동 완성은](search-autocomplete-tutorial.md) 사용자 형 검색 동작을 구현하기 위한 **제안** API에 대한 대안을 제공합니다. 자동 완성은 사용자가 현재 입력하고 있는 단어 또는 구를 "완성"합니다.
* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드는](search-howto-index-json-blobs.md)줄 바이라고 구분되는 JSON 엔터티당 하나의 검색 문서를 만듭니다.

### <a name="new-preview-features-in-version-80-preview"></a>버전 8.0-미리 보기의 새로운 미리 보기 기능
Azure Search .NET SDK의 버전 8.0 미리 보기는 API 버전 2017-11-11-미리 보기를 대상으로 합니다. 이 버전은 버전 9의 모든 동일한 기능을 포함, 플러스 :

* 서비스 측 암호화-미사용 암호화에 대한 [고객 관리](search-security-manage-encryption-keys.md) 암호화 키는 새로운 미리 보기 기능입니다. Microsoft에서 관리하는 기본 제공 암호화 외에도 키의 유일한 소유자인 추가 암호화 계층을 적용할 수 있습니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드에 실패하면 각 빌드 오류를 수정해야 합니다. 각 잠재적빌드 오류를 해결하는 방법에 대한 자세한 내용은 [버전 9의 변경 사항 만들기를](#ListOfChanges) 참조하십시오.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어 응용 프로그램에서 `DataSourceType.DocumentDb` 속성을 사용하는 경우 "이 멤버가 더 이상 사용되지 않습니다. 대신 코스모스DB를 사용하십시오.".

모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능은 [버전 9의 새로운 기능에](#WhatsNew)자세히 설명되어 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>버전 9의 주요 변경 사항

버전 9에는 응용 프로그램을 다시 빌드하는 것 외에도 코드를 변경해야 하는 몇 가지 주요 변경 사항이 있습니다.

> [!NOTE]
> 아래 변경 사항은 완전하지 않습니다. 일부 변경 내용으로 인해 빌드 오류가 발생하지는 않지만 이전 버전의 Azure Search .NET SDK 어셈블리에 종속된 어셈블리와의 이진 호환성이 중단되므로 기술적으로 문제가 발생합니다. 이러한 변경 사항은 아래에 나열되어 있지 않습니다. 이진 호환성 문제를 피하기 위해 버전 9로 업그레이드할 때 응용 프로그램을 다시 빌드하십시오.

### <a name="immutable-properties"></a>변경할 수 없는 속성

이제 여러 모델 클래스의 공용 속성이 불변입니다. 테스트를 위해 이러한 클래스의 사용자 지정 인스턴스를 만들어야 하는 경우 새 매개 변수화된 생성기를 사용할 수 있습니다.

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>필드 변경 사항

클래스는 `Field` 복잡한 필드를 나타낼 수도 있으므로 클래스가 변경되었습니다.

이제 `bool` 다음 속성은 null 수 있습니다.

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

이는 이러한 속성이 이제 `null` 복잡한 필드의 경우 이어야 하기 때문입니다. 이러한 속성을 읽는 코드가 있는 경우 을 처리할 `null`준비를 해야 합니다. 다른 `Field` 모든 속성은 항상 nullable이었으며 계속 사용할 수 있으며 그 `null` 중 일부는 복잡한 필드의 경우 특히 다음과 같은 경우에도 마찬가지입니다.

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

매개 변수 없는 `Field` 생성자가 `internal`만들어졌습니다. 이제부터는 건설 `Field` 시 명시적 이름과 데이터 형식이 필요합니다.

### <a name="simplified-batch-and-results-types"></a>간소화된 배치 및 결과 유형

버전 7.0 미리 보기 및 이전 버전에서는 문서 그룹을 캡슐화하는 다양한 클래스가 병렬 클래스 계층 구조로 구성되었습니다.

  -  `DocumentSearchResult`상속 `DocumentSearchResult<T>``DocumentSearchResultBase`
  -  `DocumentSuggestResult`상속 `DocumentSuggestResult<T>``DocumentSuggestResultBase`
  -  `IndexAction`상속 `IndexAction<T>``IndexActionBase`
  -  `IndexBatch`상속 `IndexBatch<T>``IndexBatchBase`
  -  `SearchResult`상속 `SearchResult<T>``SearchResultBase`
  -  `SuggestResult`상속 `SuggestResult<T>``SuggestResultBase`

제네릭 형식 매개 변수가 없는 파생 형식은 "동적으로 형식" 시나리오에서 `Document` 사용되며 형식의 사용가상으로 가정되었습니다.

버전 8.0 미리 보기부터 기본 클래스와 비제네릭 파생 클래스가 모두 제거되었습니다. 동적으로 형식이 있는 시나리오의 `IndexBatch<Document>`경우 `DocumentSearchResult<Document>`에서 등등을 사용할 수 있습니다.
 
### <a name="removed-extensibleenum"></a>제거된 확장가능에넘

`ExtensibleEnum` 기본 클래스가 제거되었습니다. 이 클래스에서 파생된 모든 클래스는 이제 `AnalyzerName`". `DataType`및 `DataSourceType` 예 와 같은 구조체입니다. 그들의 `Create` 방법도 제거되었습니다. 이러한 형식은 문자열에서 `Create` 암시적으로 변환할 수 있으므로 호출을 제거할 수 있습니다. 컴파일러 오류가 발생하면 캐스팅을 통해 변환 연산자를 명시적으로 호출하여 형식을 모호하게 만들 수 있습니다. 예를 들어 다음과 같이 코드를 변경할 수 있습니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

다음과 같이 변경합니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

이러한 형식의 선택적 값을 보유한 속성은 이제 명시적으로 null로 입력되므로 계속 선택 사항입니다.

### <a name="removed-facetresults-and-hithighlights"></a>제거된 패싯결과 및 히트하이라이트

`FacetResults` 및 `HitHighlights` 클래스가 제거되었습니다. 패싯 결과는 이제 로 `IDictionary<string, IList<FacetResult>>` 입력되고 `IDictionary<string, IList<string>>`강조 표시됩니다. 이 변경으로 인해 도입된 빌드 오류를 `using` 빠르게 해결하는 방법은 제거된 형식을 사용하는 각 파일의 맨 위에 별칭을 추가하는 것입니다. 예를 들어:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>동의어로 변경 

`SynonymMap` 생성자에는 더 이상 `SynonymMapFormat`에 대한 `enum` 매개 변수가 없습니다. 이 열거형에는 하나의 값만 있으므로 중복되었습니다. 이로 인해 빌드 오류가 표시되면 `SynonymMapFormat` 매개 변수에 대한 참조만 제거하면 됩니다.

### <a name="miscellaneous-model-class-changes"></a>기타 모델 클래스 변경

의 `AutocompleteMode` `AutocompleteParameters` 속성은 더 이상 null 수 없습니다. `null`에 이 속성을 할당하는 코드가 있는 경우 이 속성을 제거할 수 있으며 속성이 자동으로 기본값으로 초기화됩니다.

이 생성자가 자동으로 생성되었으므로 `IndexAction` 생성자에 대한 매개 변수의 순서가 변경되었습니다. 생성자 대신 팩터리 메서드 `IndexAction.Upload`를 `IndexAction.Merge`사용하는 것이 좋습니다.

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 8.0 미리 보기에서 버전 9로 업그레이드하는 경우 이 기능이 아직 미리 보기 중이므로 고객 관리 키로 암호화가 제거되었습니다. 구체적으로, `EncryptionKey` 의 `Index` 속성및 `SynonymMap` 제거되었다.

응용 프로그램에 이 기능에 대한 종속성이 큰 경우 Azure Search .NET SDK의 버전 9로 업그레이드할 수 없습니다. 버전 8.0 미리 보기를 계속 사용할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

> [!NOTE]
> SDK의 버전 8.0 미리 보기를 사용하여 암호화된 인덱스 또는 동의어 맵을 만든 경우에도 암호화 상태에 부정적인 영향을 주지 않고 SDK 버전 9를 사용하여 해당 인덱스를 사용하고 정의를 수정할 수 있습니다. SDK의 버전 9는 `encryptionKey` REST API로 속성을 보내지 않으며 결과적으로 REST API는 리소스의 암호화 상태를 변경하지 않습니다. 

### <a name="behavioral-change-in-data-retrieval"></a>데이터 검색의 행동 변화

형식의 `Document`인스턴스를 반환하는 "동적으로 `Search` `Suggest`입력된" 또는 `Get` API를 사용하는 경우 이제 빈 JSON 배열을 대신으로 `object[]` `string[]`역직렬화한다는 점에 유의하십시오.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [스택 오버플로에](https://stackoverflow.com/questions/tagged/azure-search)대한 도움을 요청하십시오. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!
