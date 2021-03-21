---
title: 검색 인덱스에 대 한 쿼리 확장의 동의어
titleSuffix: Azure Cognitive Search
description: 동의어 맵을 만들어 Azure Cognitive Search 인덱스에서 검색 쿼리의 범위를 확장 합니다. 범위는 목록으로 제공하는 동급 용어를 포함하도록 확장됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 5e608d38ff70d51b569088629a6d80cb08e74ed4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251627"
---
# <a name="synonyms-in-azure-cognitive-search"></a>Azure Cognitive Search의 동의어

동의어 맵을 사용 하면 실제로 용어를 제공 하지 않아도 동등한 용어를 연결 하 여 쿼리 범위를 확장할 수 있습니다. 예를 들어 "dog", "canine" 및 "puppy"로 가정 하는 동의어는 "dog"를 포함 하는 문서에서 "canine"에 대 한 쿼리를 찾습니다.

## <a name="create-synonyms"></a>동의어 만들기

동의어 맵은 한 번 만들고 여러 인덱스에서 사용할 수 있는 자산입니다. [서비스 계층](search-limits-quotas-capacity.md#synonym-limits) 에는 무료 및 기본 계층에 대 한 세 가지 동의어 맵, 표준 계층의 경우 최대 20 개까지 만들 수 있는 동의어 맵 수가 결정 됩니다. 

영어, 프랑스어 버전 등 다른 언어에 대 한 동의어 맵을 여러 개 만들거나 콘텐츠에 기술 또는 모호한 용어가 포함 된 경우 lexicons 수 있습니다. 검색 서비스에서 동의어 맵을 여러 개 만들 수 있지만 필드는 이러한 맵을 하나만 사용할 수 있습니다.

동의어 맵은 동의어 맵 항목으로 작동 하는 이름, 형식 및 규칙으로 구성 됩니다. 유일 하 게 지원 되는 형식은 이며 `solr` `solr` 형식은 규칙 생성을 결정 합니다.

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

동의어 맵을 만들려면 [Create 동의어 맵 (REST API)](/rest/api/searchservice/create-synonym-map) 또는 Azure SDK를 사용 합니다. C # 개발자의 경우 [c #을 사용 하 여 Azure 인식 검색에서 동의어 추가](search-synonyms-tutorial-sdk.md)로 시작 하는 것이 좋습니다.

## <a name="define-rules"></a>규칙 정의

매핑 규칙은이 문서에서 설명 하는 Apache [SynonymFilter](https://cwiki.apache.org/confluence/display/solr/Filter+Descriptions#FilterDescriptions-SynonymFilter)r의 오픈 소스 동의어 필터 사양을 따릅니다. `solr` 형식은 다음과 같은 두 가지 규칙을 지원 합니다.

+ 동등 (용어는 쿼리에서 동일 하 게 대체 됨)

+ 명시적 매핑 (용어는 쿼리 전에 하나의 명시적 용어로 매핑됨)

각 규칙은 줄 바꿈 문자 ()로 구분 되어야 합니다 `\n` . 사용 가능한 서비스에서 동의어 맵 당 최대 5000 개의 규칙을 정의 하 고 다른 계층의 map 당 2만 규칙을 정의할 수 있습니다. 각 규칙에는 최대 20 개의 확장 (또는 규칙의 항목)이 있을 수 있습니다. 자세한 내용은 [동의어 제한](search-limits-quotas-capacity.md#synonym-limits)을 참조 하세요.

쿼리 파서는 대문자 또는 혼합 case를 소문자로 표시 하지만 쉼표 또는 대시와 같이 문자열에 특수 문자를 유지 하려는 경우에는 동의어 맵을 만들 때 적절 한 이스케이프 문자를 추가 합니다.

### <a name="equivalency-rules"></a>등가 규칙

등가 용어에 대 한 규칙은 동일한 규칙 내에서 쉼표로 구분 됩니다. 첫 번째 예제에서는에 대 한 쿼리가 `USA` `USA` 또는 또는로 확장 `"United States"` 됩니다 `"United States of America"` . 구에 일치 시키려는 경우 쿼리 자체는 인용 부호로 묶인 구 쿼리 여야 합니다.

동등성의 경우에 대 한 쿼리는 `dog` 및도 포함 하도록 쿼리를 확장 `puppy` 합니다 `canine` .

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

명시적 매핑에 대 한 규칙은 화살표로 표시 됩니다 `=>` . 지정 된 경우의 왼쪽과 일치 하는 검색 쿼리의 용어 시퀀스는 `=>` 쿼리 시 오른쪽의 대체 항목으로 바뀝니다.

명시적 사례에서 또는에 대 한 쿼리 `Washington` 는 `Wash.` `WA` 로 다시 작성 되 `WA` 고 쿼리 엔진은 용어에 대 한 일치 항목만 찾습니다 `WA` . 명시적 매핑은 지정 된 방향 에서만 적용 되 고이 경우에 쿼리를 다시 작성 하지 않습니다 `WA` `Washington` .

```json
{
"format": "solr",
"synonyms": "
    Washington, Wash., WA => WA\n
    California, Calif., CA => CA\n"
}
```

### <a name="escaping-special-characters"></a>특수 문자 이스케이프

동의어는 쿼리 처리 중에 분석 됩니다. 쉼표나 기타 특수 문자를 포함 하는 동의어를 정의 해야 하는 경우 다음 예제와 같이 백슬래시로 이스케이프할 수 있습니다.

```json
{
"format": "solr",
"synonyms": "WA\, USA, WA, Washington\n"
}
```

백슬래시는 JSON 및 c #과 같은 다른 언어에서 특수 문자 이므로 두 번 이스케이프 해야 할 수도 있습니다. 예를 들어 위의 동의어 맵에 대 한 REST API으로 전송 되는 JSON은 다음과 같습니다.

```json
{
"format":"solr",
"synonyms": "WA\\, USA, WA, Washington"
}
```

## <a name="upload-and-manage-synonym-maps"></a>동의어 맵 업로드 및 관리

앞에서 설명한 것 처럼 쿼리 및 인덱싱 워크 로드를 방해 하지 않고 동의어 맵을 만들거나 업데이트할 수 있습니다. 동의어 맵은 인덱스 또는 데이터 원본과 같은 독립 실행형 개체 이며 필드를 사용 하지 않는 한 업데이트로 인해 인덱싱 또는 쿼리가 실패 하지 않습니다. 그러나 동의어 맵을 필드 정의에 추가한 후에는 동의어 맵을 삭제할 경우 해당 필드를 포함 하는 모든 쿼리는 404 오류로 인해 실패 합니다.

동의어 맵 만들기, 업데이트 및 삭제는 항상 전체 문서 작업 이므로 동의어 맵의 일부를 증분 방식으로 업데이트 하거나 삭제할 수 없습니다. 단일 규칙을 업데이트 하려면 다시 로드 해야 합니다.

## <a name="assign-synonyms-to-fields"></a>필드에 동의어 할당

동의어 맵을 업로드 한 후에는 형식의 필드 또는의 필드에서 동의어를 사용 하도록 설정할 수 있습니다 `Edm.String` `Collection(Edm.String)` `"searchable":true` . 설명 된 대로 필드 정의는 동의어 맵을 하나만 사용할 수 있습니다.

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

## <a name="query-on-equivalent-or-mapped-fields"></a>동등 하거나 매핑된 필드에 대해 쿼리

동의어를 추가 해도 쿼리 생성에는 새로운 요구 사항이 적용 되지 않습니다. 동의어를 추가할 때와 마찬가지로 용어 및 구 쿼리를 실행할 수 있습니다. 유일한 차이점은 쿼리 용어가 동의어 맵에 있는 경우 쿼리 엔진은 규칙에 따라 용어 또는 구를 확장 하거나 다시 작성 하는 것입니다.

## <a name="how-synonyms-are-used-during-query-execution"></a>쿼리를 실행 하는 동안 동의어를 사용 하는 방법

동의어는 동의어 할당을 사용 하는 필드에 대해서만 동일한 용어로 인덱스 내용을 보충 하는 쿼리 확장 기술입니다. 필드 범위 쿼리가 동의어 사용 필드를 *제외* 하는 경우 동의어 맵에서 일치 항목을 볼 수 없습니다.

동의어를 사용 하는 필드의 경우 동의어는 관련 필드와 동일한 텍스트 분석에 적용 됩니다. 예를 들어 표준 Lucene 분석기를 사용 하 여 필드를 분석 하는 경우 동의어 용어는 쿼리 시 표준 Lucene 분석기의 영향을 받습니다. 마침표 또는 대시와 같은 문장 부호를 동의어 용어로 유지 하려면 필드에 내용 유지 분석기를 적용 합니다.

내부적으로 동의어 기능은 또는 연산자를 사용 하 여 원래 쿼리를 동의어로 다시 작성 합니다. 따라서 강조 표시된 적중 항목 및 점수 매기기 프로필에서는 원래 용어 및 동의어를 같은 것으로 취급합니다.

동의어는 자유 형식 텍스트 쿼리에만 적용 되며 필터, 패싯, 자동 완성 또는 제안 사항에 대해서는 지원 되지 않습니다. 자동 완성 및 제안 사항은 원래 용어를 기준으로 합니다. 동의어 일치는 응답에 표시 되지 않습니다.

동의어 확장은 와일드카드 검색 용어에는 적용되지 않으며 접두사, 유사 용어 및 정규식 용어는 확장되지 않습니다.

동의어 확장과 와일드카드, 정규식 또는 유사 항목 검색을 적용하는 단일 쿼리를 수행해야 하는 경우 OR 구문을 사용하여 쿼리를 결합할 수 있습니다. 예를 들어 간단한 쿼리 구문에서 동의어와 와일드카드를 결합하려면 용어는 `<query> | <query>*`가 됩니다.

개발(비프로덕션) 환경에 기존 인덱스가 있는 경우 작은 사전을 사용하여 동의어 추가를 통해 점수 매기기 프로필, 강조 표시된 적중 항목 및 제안 사항을 비롯한 검색 환경을 변경하는 방법을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [동의어 맵 만들기 (REST API)](/rest/api/searchservice/create-synonym-map)