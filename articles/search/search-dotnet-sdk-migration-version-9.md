---
title: Azure Search.NET SDK 버전 9-Azure Search로 업그레이드
description: 이전 버전에서 Azure Search.NET SDK 버전 9로 코드를 마이그레이션하십시오. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: a59deed4ac0cec669ddc5e0335f7274586c702e8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65541758"
---
# <a name="upgrade-to-the-azure-search-net-sdk-version-9"></a>Azure Search.NET SDK 버전 9로 업그레이드

7.0-preview 또는 이전 버전의 버전을 사용 하는 경우는 [Azure Search.NET SDK](https://aka.ms/search-sdk),이 문서에서는 버전 9를 사용 하도록 응용 프로그램을 업그레이드 합니다.

> [!NOTE]
> 버전 8.0-미리 보기를 사용 하 여 아직 일반 공급 되지 않는 기능을 평가 하려는 경우에 8.0-preview 이전 버전에서 업그레이드 하려면이 문서의 지침을 따라 수 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search.NET SDK의 버전 9에는 이전 버전에서 많은 변경이 포함 되어 있습니다. 그 중 일부는 주요 변경 사항, 하지만 코드를 비교적 사소한 변경만 있어야 합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 버전 4.0-preview 또는 이전 버전을 사용 하는 경우 먼저 버전 5로 업그레이드 및 다음 버전 9로 업그레이드 됩니다. 참조 [Azure Search.NET SDK 버전 5로 업그레이드](search-dotnet-sdk-migration-version-5.md) 지침에 대 한 합니다.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>버전 9의에서 새로운 기능
Azure Search.NET SDK의 버전 9 최신 대상으로 Azure Search REST API, 특히: 2019-05-06의 일반 공급 버전입니다. 이 버전이 있으면 다음을 비롯한 Azure Search의 새 기능을 .NET 애플리케이션에서 사용할 수 있습니다.

* [Cognitive search](cognitive-search-concept-intro.md) AI 기능은 Azure search에서 이미지, blob 및 Azure Search 인덱스에 더 검색할 수 있도록 콘텐츠 보강-기타 구조화 되지 않은 데이터 원본에서 텍스트를 추출 하는 데 사용 합니다.
* 에 대 한 지원 [복합 형식](search-howto-complex-data-types.md) Azure Search 인덱스에 거의 모든 중첩 된 JSON 구조를 모델링할 수 있습니다.
* [자동 완성](search-autocomplete-tutorial.md) 대안을 제공 합니다 **제안** 검색---입력할 때 동작을 구현 하기 위한 API입니다. 자동 완성 "완료" 단어 또는 문구를 사용자가 현재 입력 합니다.
* [구문 분석 모드 JsonLines](search-howto-index-json-blobs.md)일부인 인덱싱, Azure Blob의 줄 바꿈으로 구분 된 JSON 엔터티 당 검색 문서를 하나 만듭니다.

### <a name="new-preview-features-in-version-80-preview"></a>버전 8.0-preview의 새로운 미리 보기 기능
버전 8.0-preview Azure Search.NET SDK의 API 버전 2017-11-11-미리 보기를 대상 으로합니다. 이 버전은 모두 동일한 포함 버전 9의 기능 및:

* [고객이 관리 하는 암호화 키](search-security-manage-encryption-keys.md) 서비스 쪽-미사용 암호화는 새로운 미리 보기 기능에 대 한 합니다. 기본 제공 암호화 미사용 시 Microsoft에서 관리 되는, 하는 것 외에도 암호화 키의 유일한 소유자는 강화를 적용할 수 있습니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드가 실패 하는 경우에 각 빌드 오류를 해결 해야 합니다. 참조 [버전 9의에서 주요 변경 내용](#ListOfChanges) 각 가능성을 해결 하는 방법에 대 한 내용은 빌드 오류에 대 한 합니다.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어, 응용 프로그램에서 사용 하는 경우는 `DataSourceType.DocumentDb` 속성을 가져와야 경고가 표시 "이이 멤버는 사용 되지 않습니다. 대신 CosmosDb "합니다.

모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 자세히 나와 [버전 9의에서 새로운 기능](#WhatsNew)합니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>버전 9의에서 주요 변경 내용

버전 9 응용 프로그램을 다시 작성 하는 것 외에도 코드 변경이 필요할 수 있는 몇 가지 주요 변경 내용이 있습니다.

> [!NOTE]
> 아래 변경 내용 목록은 철저 없습니다. 일부 변경 내용은 빌드 오류에서 가능성이 발생 하지는 있지만 이전 버전의 Azure Search.NET SDK 어셈블리에 종속 된 어셈블리와 이진 호환성 중단은 이후의 주요 기술적으로. 이러한 변경 내용은 아래 나열 되지 않습니다. 이진 호환성 문제를 방지 하려면 9 버전으로 업그레이드 하는 경우 응용을 프로그램을 빌드하십시오.

### <a name="immutable-properties"></a>변경할 수 없는 속성

이제 여러 모델 클래스의 공용 속성은 변경할 수 없습니다. 테스트를 위해 이러한 클래스의 사용자 지정 인스턴스를 만들어야 하는 경우에 새 매개 변수가 있는 생성자를 사용할 수 있습니다.

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>필드 변경

`Field` 복잡 한 필드를 나타낼 수도 있습니다 했으므로 클래스가 변경 되었습니다.

다음 `bool` 속성이 null이 허용 됩니다.

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

이러한 속성은 이제 이어야 하므로 이것이 `null` 복잡 한 필드의 경우. 이러한 속성을 읽는 코드를 경우이를 처리 하도록 준비 `null`합니다. 다른 모든 속성 `Field` 항상 null을 허용 하지 계속 및 하며 그 중 일부 `null` 다음 특히 복잡 한 필드의 경우:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

매개 변수가 없는 생성자 `Field` 이루어졌습니다 `internal`합니다. 이제 모든 `Field` 명시적 이름과 데이터 형식을 생성 시 필요 합니다.

### <a name="simplified-batch-and-results-types"></a>간소화 된 일괄 처리 및 결과 형식입니다.

7.0-preview와 이전 버전에서는 문서 그룹을 캡슐화 하는 다양 한 클래스는 병렬 클래스 계층 구조에 구성 되었습니다.

  -  `DocumentSearchResult` 및 `DocumentSearchResult<T>` 에서 상속 되며, `DocumentSearchResultBase`
  -  `DocumentSuggestResult` 및 `DocumentSuggestResult<T>` 에서 상속 되며, `DocumentSuggestResultBase`
  -  `IndexAction` 및 `IndexAction<T>` 에서 상속 되며, `IndexActionBase`
  -  `IndexBatch` 및 `IndexBatch<T>` 에서 상속 되며, `IndexBatchBase`
  -  `SearchResult` 및 `SearchResult<T>` 에서 상속 되며, `SearchResultBase`
  -  `SuggestResult` 및 `SuggestResult<T>` 에서 상속 되며, `SuggestResultBase`

제네릭 형식 매개 변수 없이 파생된 형식 "동적으로 형식화" 시나리오에서 사용할 수 있었으며의 사용을 가정 합니다 `Document` 형식입니다.

8.0-preview 버전부터, 기본 클래스 및 제네릭이 아닌 파생된 클래스를 모두 제거 되었습니다. 동적으로 형식화 된 시나리오를 사용할 수 있습니다 `IndexBatch<Document>`, `DocumentSearchResult<Document>`등입니다.
 
### <a name="removed-extensibleenum"></a>제거 ExtensibleEnum

`ExtensibleEnum` 기본 클래스가 제거 되었습니다. 파생 된 모든 클래스는 같은 구조체를 이제 `AnalyzerName`, `DataType`, 및 `DataSourceType` 예를 들어 있습니다. 해당 `Create` 메서드 또한 제거 되었습니다. 에 대 한 호출을 제거할 수 `Create` 되므로 이러한 형식 문자열에서 암시적으로 변환할 수 있습니다. 컴파일러 오류를 발생 하는 경우 형식을 명확 하 게 캐스팅을 통해 변환 연산자를 명시적으로 호출할 수 있습니다. 예를 들어, 다음과 같은 코드를 변경할 수 있습니다.

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

이러한 형식의 선택적 값을 보유 하는 속성은 이제 명시적으로 형식화 null 허용으로 계속 선택적 수 있도록 합니다.

### <a name="removed-facetresults-and-hithighlights"></a>제거 FacetResults 및 HitHighlights

합니다 `FacetResults` 고 `HitHighlights` 클래스가 제거 되었습니다. 패싯 결과 이제 형식으로 지정 `IDictionary<string, IList<FacetResult>>` 적중으로 강조 표시 및 `IDictionary<string, IList<string>>`합니다. 추가 하는 것이 변경으로 인해 빌드 오류를 해결 하는 빠른 방법은 `using` 제거 형식을 사용 하는 각 파일의 맨 위에 있는 별칭입니다. 예를 들면 다음과 같습니다.

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>SynonymMap 변경 

`SynonymMap` 생성자에는 더 이상 `SynonymMapFormat`에 대한 `enum` 매개 변수가 없습니다. 이 열거형에는 하나의 값만 있으므로 중복되었습니다. 이로 인해 빌드 오류가 표시되면 `SynonymMapFormat` 매개 변수에 대한 참조만 제거하면 됩니다.

### <a name="miscellaneous-model-class-changes"></a>기타 모델 클래스 변경

합니다 `AutocompleteMode` 속성의 `AutocompleteParameters` null을 허용 하지 않습니다. 이 속성을 할당 하는 코드가 있다면 `null`, 간단히 제거할 수 있습니다 및 속성을 기본값으로 자동으로 초기화 됩니다.

매개 변수의 순서는 `IndexAction` 생성자는이 생성자는 자동으로 생성 했으므로 변경 되었습니다. 생성자를 사용 하는 대신 팩터리 메서드를 사용 하 여 권장 `IndexAction.Upload`, `IndexAction.Merge`등입니다.

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 9로 8.0-preview 버전에서에서 업그레이드 하는 경우이 기능은 미리 보기 상태에서 이므로 고객 관리 키를 사용 하 여 해당 암호화 제거 되었습니다. 특히 합니다 `EncryptionKey` 속성을 `Index` 및 `SynonymMap` 제거 되었습니다.

응용 프로그램에 대 한 강한 종속성이이 기능에 Azure Search.NET SDK의 9 버전으로 업그레이드할 수 없습니다. 버전 8.0-미리 보기를 사용 하 여 할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

> [!NOTE]
> 만든 암호화 된 경우 인덱스 또는 동의어 맵 8.0-preview 버전의 SDK 사용 하 여 계속 수 있습니다 사용 하 고 부정적인 암호화 상태의 영향을 주지 않고 SDK의 버전 9를 사용 하 여 해당 정의 수정 합니다. SDK의 버전 9 보내지 않습니다는 `encryptionKey` REST API를 REST API를 결과로 속성 리소스의 암호화 상태를 변경 되지 것입니다. 

### <a name="behavioral-change-in-data-retrieval"></a>데이터 검색의 동작 변경

"동적으로 형식화 된" 사용 중인 경우 `Search`, `Suggest`, 또는 `Get` 형식의 인스턴스를 반환 하는 Api `Document`에 빈 JSON 배열에 이제 역직렬화에 유의 `object[]` 대신 `string[]`합니다.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 우리에 게 도움 요청에 문제가 있는 경우 자유롭게 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)합니다. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!
