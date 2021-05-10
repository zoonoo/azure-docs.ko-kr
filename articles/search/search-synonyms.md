---
title: 검색 인덱스에 대한 쿼리 확장을 위한 동의어
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱스에 대한 검색 쿼리 범위를 확장하기 위한 동의어 맵을 만듭니다. 범위는 목록으로 제공하는 동급 용어를 포함하도록 확장됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251627"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Cognitive Search의 동의어

동의어 맵을 사용하면 사용자가 실제로 용어를 제공할 필요 없이 동등한 용어를 연결하여 쿼리의 범위를 확장할 수 있습니다. 예를 들어, "dog", "canine" 및 "puppy"가 동의어라고 가정할 경우 "canine"에 대한 쿼리는 "dog"를 포함하는 문서에서 일치하는 것으로 검색됩니다.

## <a name="create-synonyms"></a>동의어 만들기

동의어 맵은 한 번 만든 후 여러 인덱스에서 사용할 수 있는 자산입니다. [서비스 계층](search-limits-quotas-capacity.md#synonym-limits)은 만들 수 있는 동의어 맵 수를 무료 및 기본 계층의 경우 3개, 표준 계층의 경우 최대 20개 범위 내에서 결정합니다. 

영어, 프랑스어 버전 등의 다른 언어에 대한 동의어 맵을 여러 개 만들거나 콘텐츠에 기술 또는 모호한 용어가 포함된 경우 어휘집을 여러 개 만들 수 있습니다. 검색 서비스에서 동의어 맵을 여러 개 만들 수 있지만 필드 하나에 이러한 맵 중 하나만 사용할 수 있습니다.

동의어 맵은 동의어 맵 항목으로 작동하는 이름, 형식 및 규칙으로 구성됩니다. 유일하게 지원되는 형식은 `solr`이며 `solr` 형식은 규칙 생성을 결정합니다.

```http
POST /synonymmaps?api-version=2020-06-30
{
    "name": "geo-synonyms",
    "format": "solr",
    "synonyms": "
        USA, United States, United States of America\n
        Washington, Wash., WA => WA\n"
}
```

동의어 맵을 만들려면 [동의어 맵 만들기(REST API)](/rest/api/searchservice/create-synonym-map) 또는 Azure SDK를 사용합니다. C# 개발자의 경우 [C#을 사용하여 Azure Cognitive Search에서 동의어 추가](search-synonyms-tutorial-sdk.md)로 시작하는 것이 좋습니다.

## <a name="define-rules"></a>규칙 정의

매핑 규칙은 이 문서([SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter))에 설명된 Apache Solr의 공개 소스 동의어 필터 사양을 준수합니다. `solr` 형식은 다음 두 가지 종류의 규칙을 지원합니다.

+ 동등성(용어가 쿼리에서 동일한 대체 항목임)

+ 명시적 매핑(용어가 쿼리 전에 하나의 명시적 용어로 매핑됨)

각 규칙은 줄 바꿈 문자(`\n`)로 구분해야 합니다. 무료 서비스에서는 동의어 맵당 최대 5,000개의 규칙을 정의하고 다른 계층에서는 매당 20,000개의 규칙을 정의할 수 있습니다. 각 규칙에는 최대 20개의 확장(또는 규칙의 항목)이 있을 수 있습니다. 자세한 내용은 [동의어 제한](search-limits-quotas-capacity.md#synonym-limits)을 참조하세요.

쿼리 파서는 대문자로만 구성된 용어 또는 대/소문자가 혼합된 용어를 소문자로 표시하지만, 쉼표 또는 대시와 같은 특수 문자를 문자열에 유지하려는 경우에는 동의어 맵을 만들 때 적절한 이스케이프 문자를 추가합니다.

### <a name="equivalency-rules"></a>등등성 규칙

등등성 용어에 대한 규칙은 동일한 규칙 내에서 쉼표로 구분됩니다. 첫 번째 예제에서는 `USA`에 대한 쿼리가 `USA`, `"United States"` 또는 `"United States of America"`로 확장됩니다. 구에서 일치 항목을 찾으려는 경우 쿼리 자체는 따옴표로 묶인 구 쿼리여야 합니다.

동등성의 경우 `dog`에 대한 쿼리는 `puppy` 및 `canine`도 포함하도록 쿼리를 확장합니다.

```json
{
"format": "solr",
"synonyms": "
    USA, United States, United States of America\n
    dog, puppy, canine\n
    coffee, latte, cup of joe, java\n"
}
```

### <a name="explicit-mapping"></a>명시적 매핑

명시적 매핑에 대한 규칙은 화살표 `=>`로 표시됩니다. 지정되면 `=>` 왼쪽에 일치하는 검색 쿼리의 용어 시퀀스는 쿼리 타임에 오른쪽에 대체 항목으로 바뀝니다.

명시적 경우에 `Washington`, `Wash.` 또는 `WA`에 대한 쿼리는 `WA`로 다시 작성되고, 쿼리 엔진은 용어 `WA`에 대한 일치 항목만 찾습니다. 명시적 매핑은 지정된 방향으로만 적용돠며 이 경우 `WA` 쿼리를 `Washington`으로 다시 작성하지 않습니다.

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>특수 문자 이스케이프

동의어는 쿼리 처리 중에 분석됩니다. 쉼표나 기타 특수 문자를 포함하는 동의어를 정의해야 하는 경우 다음 예제와 같이 백슬래시로 이스케이프할 수 있습니다.

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

백슬래시는 JSON 및 C# 등의 다른 언어에서 특수 문자이므로 두 번 이스케이프해야 할 수도 있습니다. 예를 들어, 위의 동의어 맵에 대해 REST API으로 전송되는 JSON은 다음과 같습니다.

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>동의어 맵 업로드 및 관리

앞에서 설명한 것처럼 쿼리 및 인덱싱 워크로드를 중단하지 않고 동의어 맵을 만들거나 업데이트할 수 있습니다. 동의어 맵은 인덱스 또는 데이터 원본과 같은 독립 실행형 개체이며 필드에서 사용되지 않는 한, 업데이트로 인해 인덱싱 또는 쿼리가 실패하지는 않습니다. 그러나 동의어 맵을 필드 정의에 추가하고 난 후 동의어 맵을 삭제할 경우 해당 필드를 포함하는 모든 쿼리는 404 오류로 인해 실패합니다.

동의어 맵 만들기, 업데이트 및 삭제는 항상 전체 문서 작업이므로 증분식으로 동의어 맵의 일부분을 업데이트하거나 삭제할 수 없습니다. 단일 규칙을 업데이트하는 경우에도 다시 로드해야 합니다.

## <a name="assign-synonyms-to-fields"></a>필드에 동의어 할당

동의어 맵을 업로드한 후에는 `"searchable":true`의 필드에 대해 `Edm.String` 또는 `Collection(Edm.String)` 형식의 필드에서 동의어를 사용하도록 설정할 수 있습니다. 설명된 대로 필드 정의는 동의어 맵을 하나만 사용할 수 있습니다.

```http
POST /indexes?api-version=2020-06-30
{
    "name":"hotels-sample-index",
    "fields":[
        {
            "name":"description",
            "type":"Edm.String",
            "searchable":true,
            "synonymMaps":[
            "en-synonyms"
            ]
        },
        {
            "name":"description_fr",
            "type":"Edm.String",
            "searchable":true,
            "analyzer":"fr.microsoft",
            "synonymMaps":[
            "fr-synonyms"
            ]
        }
    ]
}
```

## <a name="query-on-equivalent-or-mapped-fields"></a>동등 필드 또는 매핑된 필드에 대해 쿼리

동의어를 추가해도 쿼리 생성 시 새로운 요구 사항이 적용되지 않습니다. 동의어를 추가하기 전과 마찬가지로 용어 및 구 쿼리를 실행할 수 있습니다. 유일한 차이점은 쿼리 용어가 동의어 맵에 있는 경우 쿼리 엔진이 규칙에 따라 용어 또는 구를 확장하거나 다시 작성한다는 것입니다.

## <a name="how-synonyms-are-used-during-query-execution"></a>쿼리를 실행하는 동안 동의어가 사용되는 방식

동의어는 동의어 할당을 사용하는 필드에 대해서만 동일한 용어로 인덱스 내용을 보충하는 쿼리 확장 기술입니다. 필드 범위 쿼리가 동의어 사용 필드를 *제외* 하는 경우 동의어 맵에서 일치 항목을 볼 수 없습니다.

동의어 지원 필드의 경우 동의어에는 관련 필드와 동일한 텍스트 분석이 적용됩니다. 예를 들어, 표준 Lucene 분석기를 사용하여 필드를 분석하는 경우 동의어 용어는 쿼리 타임에 표준 Lucene 분석기의 영향을 받습니다. 마침표 또는 대시와 같은 문장 부호를 동의어 용어에 유지하려면 필드에 콘텐츠 유지 분석기를 적용합니다.

내부적으로 동의어 기능은 OR 연산자를 사용하여 원래 쿼리를 동의어로 다시 작성합니다. 따라서 강조 표시된 적중 항목 및 점수 매기기 프로필에서는 원래 용어 및 동의어를 같은 것으로 취급합니다.

동의어는 자유 형식 텍스트 쿼리에만 적용되며 필터, 패싯, 자동 완성 또는 제안 사항에서는 지원되지 않습니다. 자동 완성 및 제안 사항은 원래 용어만을 기준으로 하며 동의어 일치는 응답에 나타나지 않습니다.

동의어 확장은 와일드카드 검색 용어에는 적용되지 않으며 접두사, 유사 용어 및 정규식 용어는 확장되지 않습니다.

동의어 확장과 와일드카드, 정규식 또는 유사 항목 검색을 적용하는 단일 쿼리를 수행해야 하는 경우 OR 구문을 사용하여 쿼리를 결합할 수 있습니다. 예를 들어 간단한 쿼리 구문에서 동의어와 와일드카드를 결합하려면 용어는 `<query> | <query>*`가 됩니다.

개발(비프로덕션) 환경에 기존 인덱스가 있는 경우 작은 사전을 사용하여 동의어 추가를 통해 점수 매기기 프로필, 강조 표시된 적중 항목 및 제안 사항을 비롯한 검색 환경을 변경하는 방법을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [동의어 맵 만들기(REST API)](/rest/api/searchservice/create-synonym-map)