---
title: Azure 인지 검색 .NET SDK 버전 10으로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure 인지 검색 .NET SDK 버전 10으로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847515"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Azure 인지 검색 .NET SDK 버전 10으로 업그레이드

[Azure Search .NET SDK의](https://aka.ms/search-sdk)버전 9.0 이상을 사용하는 경우 이 문서에서는 버전 10을 사용하도록 응용 프로그램을 업그레이드하는 데 도움이 됩니다.

Azure Search는 버전 10에서 Azure Cognitive Search로 이름이 바뀌지만 네임스페이스 및 패키지 이름은 변경되지 않습니다. 이전 버전의 SDK(9.0 이상)는 이전 이름을 계속 사용합니다. 예제를 포함하여 SDK 사용에 대한 자세한 내용은 [.NET 응용 프로그램에서 Azure 인지 검색을 사용하는 방법을](search-howto-dotnet-sdk.md)참조하십시오.

버전 10은 몇 가지 기능과 버그 수정을 추가하여 REST API 버전의 `2019-05-06`최신 릴리스와 동일한 기능 수준으로 가져옵니다. 변경으로 인해 기존 코드가 중단되는 경우 문제를 [해결하는 데 필요한 단계를](#UpgradeSteps)안내합니다.

> [!NOTE]
> 버전 8.0 미리 보기를 사용하는 경우 먼저 버전 9로 업그레이드한 다음 버전 10으로 업그레이드해야 합니다. 지침은 [Azure 검색 .NET SDK 버전 9로 업그레이드를](search-dotnet-sdk-migration-version-9.md) 참조하십시오.
>
> 검색 서비스 인스턴스는 최신 버전을 포함하여 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>버전 10의 새로운 내용
Azure Cognitive Search .NET SDK의 버전 10은 이러한 업데이트를`2019-05-06`통해 REST API()의 최신 일반적으로 사용 가능한 버전을 대상으로 합니다.

* [조건부 기술과](cognitive-search-skill-conditional.md) [텍스트 번역 기술](cognitive-search-skill-text-translation.md)- 두 가지 새로운 기술의 소개 .
* [셰이퍼 기술](cognitive-search-skill-shaper.md) 입력이 중첩된 컨텍스트의 통합을 수용하도록 재구성되었습니다. 자세한 내용은 이 [예제 JSON 정의를](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)참조하십시오.
* 두 개의 새로운 [필드 매핑 기능](search-indexer-field-mappings.md)추가 :
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* 경우에 따라 [인덱서 실행 상태에](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 표시 되는 오류 및 경고 디버깅에 도움이 되는 추가 세부 정보를 가질 수 있습니다. `IndexerExecutionResult`이 동작을 반영하도록 업데이트되었습니다.
* [기술 집합](cognitive-search-defining-skillset.md) 내에서 정의된 개별 기술은 속성을 지정하여 `name` 선택적으로 식별할 수 있습니다.
* `ServiceLimits`[에서는 복잡한 형식에](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) 대한 제한을 표시하고 `IndexerExecutionInfo` 관련 인덱서 제한/할당량을 표시합니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계

1. NuGet 패키지 관리자 `Microsoft.Azure.Search` 콘솔을 사용하거나 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리"를 선택하여 NuGet 참조를 업데이트합니다. 비주얼 스튜디오에서.

2. NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 

3. 빌드에 실패하면 각 빌드 오류를 수정해야 합니다. 각 잠재적 빌드 오류를 해결하는 방법에 대한 자세한 내용은 [버전 10의 변경 사항 만들기를](#ListOfChanges) 참조하십시오.

4. 모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능은 [버전 10의 새로운 기능에](#WhatsNew)자세히 설명되어 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>버전 10의 주요 변경 사항

버전 10에는 응용 프로그램을 다시 빌드하는 것 외에도 코드를 변경해야 하는 몇 가지 주요 변경 사항이 있습니다.

> [!NOTE]
> 아래 변경 사항은 완전하지 않습니다. 일부 변경 내용으로 인해 빌드 오류가 발생하지는 않지만 이전 버전의 Azure Cognitive Search .NET SDK 어셈블리에 종속된 어셈블리와의 이진 호환성이 중단되므로 기술적으로 문제가 발생합니다. 이 범주에 속하는 중요한 변경 사항도 권장 사항과 함께 나열됩니다. 이진 호환성 문제를 피하기 위해 버전 10으로 업그레이드할 때 응용 프로그램을 다시 빌드하십시오.

### <a name="custom-web-api-skill-definition"></a>사용자 지정 웹 API 기술 정의

사용자 지정 [웹 API 기술의](cognitive-search-custom-skill-web-api.md) 정의 버전 9 이상에서 잘못 지정 되었습니다. 

사전을 `WebApiSkill` 포함하는 `HttpHeaders` 개체 속성으로 _contains_ 지정된 모델입니다. 이러한 방식으로 구성된 `WebApiSkill` 기술 집합을 만들면 REST API에서 요청을 잘못 형성한 것으로 간주하기 때문에 예외가 발생합니다. REST API에서 유효한 `HttpHeaders` **요청으로** 간주되는 모델 자체에서 `WebApiSkill` 최상위 사전 속성을 만들어 이 문제가 수정되었습니다.

예를 들어 이전에 다음과 같이 인스턴스화하려고 `WebApiSkill` 시도한 경우 를 수행합니다.

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

REST API의 유효성 검사 오류를 방지하려면 다음으로 변경합니다.

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>셰이퍼 기술을 사용하면 중첩된 컨텍스트 통합이 가능합니다.

셰이퍼 기술은 이제 중첩된 컨텍스트에서 입력 통합을 허용할 수 있습니다. 이 변경을 활성화하기 `InputFieldMappingEntry` 위해 `Source` 속성 또는 `SourceContext` 속성과 `Inputs` 속성을 모두 지정하여 인스턴스화할 수 있도록 수정했습니다.

코드를 변경할 필요가 없습니다. 그러나 이러한 두 조합 중 하나만 허용됩니다. 이것은 다음을 의미합니다.

- 초기화된 `InputFieldMappingEntry` `Source` 위치만 만드는 것은 유효합니다.
- 초기화되고 `InputFieldMappingEntry` `SourceContext` `Inputs` 초기화되는 위치만 만드는 것은 유효합니다.
- 이러한 세 속성과 관련된 다른 모든 조합은 유효하지 않습니다.

이 새 기능을 사용하기로 결정한 경우 변경 사항을 롤아웃하기 전에 모든 클라이언트가 버전 10을 먼저 사용하도록 업데이트되었는지 확인합니다. 그렇지 않으면 클라이언트가 Shaper 기술에 대한 이전 버전의 SDK를 사용하여 유효성 검사 오류가 발생할 수 있습니다.

> [!NOTE]
> 중첩된 `InputFieldMappingEntry` 컨텍스트에서 통합을 허용하도록 기본 모델이 수정되었지만 셰이퍼 기술의 정의 내에서만 유효합니다. 컴파일 타임에 유효하지만 다른 기술에서 이 기능을 사용하면 런타임시 유효성 검사 오류가 발생합니다.

## <a name="skills-can-be-identified-by-a-name"></a>기술은 이름으로 식별할 수 있습니다.

이제 기술 집합 내의 각 `Name`기술에 새 속성이 있으며, 이 속성은 코드에서 초기화하여 기술을 식별하는 데 도움이 됩니다. 이는 선택 사항입니다 - 지정되지 않은 경우(명시적 코드 변경이 이루어지지 않은 경우 기본값인 경우) '#' 문자와 함께 접두어 있는 기술 집합의 기술의 1기반 인덱스를 사용하여 기본 이름이 할당됩니다. 예를 들어 다음 기술 집합 정의에서(대부분의 초기화는 간결하게 건너뜁니다).

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill`이름이 `#1` `WebApiSkill` 할당됩니다. `#2` `ShaperSkill` `#3`

사용자 지정 이름으로 기술을 식별하도록 선택한 경우 클라이언트의 모든 인스턴스를 먼저 SDK 버전 10으로 업데이트해야 합니다. 그렇지 않으면 이전 버전의 SDK를 사용하는 클라이언트가 `null` 기술의 `Name` 속성을 벗어날 수 있으며, 이로 인해 클라이언트가 기본 명명 체계로 대체될 수 있습니다.

## <a name="details-about-errors-and-warnings"></a>오류 및 경고에 대한 세부 정보

`ItemError`인덱서 실행 중에 발생하는 오류 및 경고(각각)의 세부 정보를 캡슐화하는 `ItemWarning` 모델은 인덱서 디버깅을 돕기 위해 세 가지 새 속성을 포함하도록 수정되었습니다. 이러한 속성은 다음과 같습니다.

- `Name`: 오류가 발생한 소스의 이름입니다. 예를 들어, 첨부된 기술 집합의 특정 기술을 참조할 수 있습니다.
- `Details`: 오류 또는 경고에 대한 자세한 세부 정보입니다.
- `DocumentationLink`: 특정 오류 또는 경고에 대한 문제 해결 가이드에 대한 링크입니다.

> [!NOTE]
> 가능한 경우 이러한 유용한 세부 정보를 포함하도록 오류 및 경고를 구성하기 시작했습니다. 모든 오류 및 경고에 대해 이러한 세부 정보가 있는지 확인하기 위해 노력하고 있지만 진행 중인 작업이며 이러한 추가 세부 정보가 항상 채워지지는 않을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Shaper 기술의 변경 사항은 새 코드 또는 기존 코드에 가장 큰 영향을 미칩니다. 다음 단계로 입력 구조를 설명하는 이 예제를 다시 방문하십시오: [셰이퍼 스킬 JSON 정의 예제](cognitive-search-skill-shaper.md)
- [AI 보강 개요를](cognitive-search-concept-intro.md)통해 이동합니다.
- SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [스택 오버플로에](https://stackoverflow.com/questions/tagged/azure-search)대한 도움을 요청하십시오. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. "[Azure 인지 검색]"로 문제 제목을 접두사해야 합니다.

