---
title: 기술 세트 개념 및 워크플로
titleSuffix: Azure Cognitive Search
description: 기술 세트는 Azure Cognitive Search에서 AI 보강 파이프라인을 작성하는 위치입니다. 기술 세트 작성에 대한 중요한 개념과 세부 정보를 알아보세요.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: f1d8715fcadeda5ccd1a98192a70939b0c359c88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84976679"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure Cognitive Search에서 개념 기술

이 문서는 기술 개념 및 컴퍼지션을 심층적으로 이해 해야 하는 개발자를 위한 것 이며 AI 보강 프로세스에 대해 잘 알고 있는 것으로 가정 합니다. 이러한 개념을 처음 접하는 경우 [Azure Cognitive Search의 AI 보강](cognitive-search-concept-intro.md)부터 시작 하세요.

## <a name="introducing-skillsets"></a>기술력과 소개

기술는 인덱서에 연결 된 Azure Cognitive Search의 재사용 가능한 리소스 이며 인덱싱 중에 텍스트 또는 이미지 콘텐츠를 분석, 변환 및 보강 하는 데 사용 되는 기술의 컬렉션을 지정 합니다. 스킬은 입/출력을 가지 며, 한 기술의 출력이 체인 또는 프로세스 시퀀스에서 다른 기술에 대 한 입력이 되는 경우가 많습니다.

기술에는 세 가지 주요 속성이 있습니다.

+ `skills`플랫폼에서 각 기술에 필요한 입력을 기준으로 실행 순서를 결정 하는 순서가 지정 되지 않은 기술 컬렉션입니다.
+ `cognitiveServices`-기본 제공 기술이 포함 된 기술력과에 대해 이미지 및 텍스트 처리를 수행 하는 Cognitive Services 리소스의 키입니다.
+ `knowledgeStore`, (선택 사항) 보강 문서가 프로젝션 되는 Azure Storage 계정 보강 문서는 검색 인덱스 에서도 사용 됩니다.

기술 세트는 JSON으로 작성됩니다. 다음 예제는이 문서의 개념을 설명 하는 데 사용 되는 [기술 리뷰](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)의 약간 단순화 된 버전입니다. 

처음 두 기술은 아래와 같습니다.

+ 기술 #1 "reviews_text" 필드의 내용을 입력으로 수락 하 고 해당 콘텐츠를 5000 문자를 출력으로 "pages"로 분할 하는 [텍스트 분할 기술](cognitive-search-skill-textsplit.md) 입니다.
+ 기술 #2는 [감정 검색 기술](cognitive-search-skill-sentiment.md) 이며, "페이지"를 입력으로 수락 하 고 감정 분석의 결과를 포함 하는 출력으로 "감정" 라는 새 필드를 생성 합니다.


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> [조건부 기술을](cognitive-search-skill-conditional.md) 사용 하 여 식을 만들면 루프와 분기를 사용 하 여 복잡 한 기술력과을 빌드할 수 있습니다. 구문은 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 경로 표기법을 기반으로 하며 보강 트리의 노드를 식별 하는 몇 가지 수정 사항이 있습니다. `"/"`는 트리에서 더 낮은 수준으로 트래버스하고, `"*"`는 컨텍스트에서 for-each 연산자 역할을 합니다. 이 문서의 다양 한 예제에서는 구문을 보여 줍니다. 

### <a name="enrichment-tree"></a>보강 트리

[보강 파이프라인의 단계](cognitive-search-concept-intro.md#enrichment-steps)를 진행 하는 동안 콘텐츠 처리는 원본에서 텍스트 및 이미지를 추출 하는 *문서 크랙* 단계를 따릅니다. 그러면 텍스트 콘텐츠가 텍스트 처리를 위해 큐에 대기 하는 동안 이미지 콘텐츠를 이미지 처리를 지정 하는 기술로 라우팅할 수 있습니다. 많은 양의 텍스트를 포함 하는 원본 문서의 경우 더 최적의 처리를 위해 텍스트를 더 작은 청크로 분할 하도록 인덱서에 *구문 분석 모드* 를 설정할 수 있습니다. 

![파이프라인의 지식 저장소 다이어그램](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "파이프라인의 지식 저장소 다이어그램")

보강 파이프라인에 있는 문서는 콘텐츠 및 관련 보강 트리로 표시됩니다. 이 트리는 문서 크래킹의 출력으로 인스턴스화됩니다.  보강 트리 형식을 사용하면 보강 파이프라인이 메타데이터를 기본 데이터 형식에도 연결할 수 있으며, 유효한 JSON 개체는 아니지만 유효한 JSON 형식으로 프로젝션할 수 있습니다. 다음 표는 보강 파이프라인으로 들어가는 문서의 상태를 보여 줍니다.

|데이터 원본\구문 분석 모드|기본값|JSON, JSON 줄 및 CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|해당 없음 |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|해당 없음|

 기술이 실행되면 기술이 보강 트리에 새 노드를 추가합니다. 그러면 이러한 새 노드는 다운스트림 기술의 입력으로 사용되어 지식 저장소로 프로젝션되거나 인덱스 필드에 매핑될 수 있습니다. 보강은 변경할 수 없습니다. 노드를 만든 후에는 편집할 수 없습니다. 기술 세트가 복잡해질수록 보강 트리 역시 복잡해지겠지만, 보강 트리의 모든 노드를 인덱스나 지식 저장소로 만들어야 하는 것은 아닙니다. 

보강 중 일부만 선별적으로 인덱스 또는 지식 저장소에 보관할 수 있습니다.

### <a name="context"></a>Context

각 기술에는 컨텍스트가 필요합니다. 컨텍스트는 다음을 결정합니다.

+ 선택한 노드에 따라 기술이 실행되는 횟수. 형식 컬렉션의 컨텍스트 값은 끝에 `/*`를 추가하면 컬렉션의 인스턴스마다 기술이 한 번씩 호출됩니다. 

+ 보강 트리에서 기술 출력이 추가되는 위치. 출력은 항상 트리에 컨텍스트 노드의 자식으로 추가됩니다. 

+ 입력 셰이프. 다중 수준 컬렉션의 경우 컨텍스트를 부모 컬렉션으로 설정하면 기술의 입력 셰이프에 영향을 줍니다. 예를 들어 국가/지역 목록이 포함 된 보강 트리가 있는 경우 각 보강는 우편 번호 목록을 포함 하는 상태 목록을 포함 합니다.

|Context|입력|입력 셰이프|기술 호출|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |해당 국가/지역에 있는 모든 우편 번호 목록 |국가/지역당 한 번 |
|`/document/countries/*/states/*` |'/document/countries/*/states/*/zipcodes/* ' ' |상태의 우편 번호 목록입니다. | 국가/지역 및 시/도 조합당 한 번|

## <a name="generate-enriched-data"></a>보강 데이터 생성 

[호텔 리뷰](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) 를 참조 지점으로 사용 하 여 다음을 기술.

+ 보강 트리가 각 기술 실행과 어떻게 진화 합니까?
+ 기술이 실행 되는 횟수를 결정 하는 데 컨텍스트와 입력이 작동 하는 방식
+ 컨텍스트를 기반으로 하는 입력의 모양

보강 프로세스 내에서 "문서"는 hotel_reviews.csv 원본 파일 내의 단일 행 (호텔 리뷰)을 나타냅니다.

### <a name="skill-1-split-skill"></a>기술 #1: 분할 기술

원본 콘텐츠가 많은 텍스트 청크로 구성 된 경우 언어, 감정 및 키 구 검색의 정확도를 높이기 위해 작은 구성 요소로 분할 하는 것이 유용 합니다. 사용할 수 있는 두 가지 조직는 페이지와 문장입니다. 페이지는 약 5000 자로 구성 됩니다.

텍스트 분할 기술은 일반적으로 기술에서 가장 먼저입니다.

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

의 기술 컨텍스트에서 `"/document/reviews_text"` 분할 기술은에 대해 한 번 실행 됩니다 `reviews_text` . 기술 출력은 `reviews_text`가 5000개 문자 세그먼트로 청크 분할된 목록입니다. 분할 된 기술 출력의 이름은로 지정 되 `pages` 고 보강 트리에 추가 됩니다. `targetName` 기능을 사용하면 기술 출력이 보강 트리에 추가되기 전에 이름을 바꿀 수 있습니다.

이제 보강 트리의 기술 컨텍스트 아래에 새 노드가 배치되었습니다. 이 노드는 모든 기술, 프로젝션 또는 출력 필드 매핑에 사용할 수 있습니다. 개념적으로 트리는 다음과 같습니다.

![문서 크래킹 후 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "문서 크래킹 후 기술 실행 전의 보강 트리")

모든 보강의 루트 노드는 `"/document"`입니다. BLOB 인덱서를 사용하면 `"/document"` 노드에는 `"/document/content"` 및 `"/document/normalized_images"`의 자식 노드가 있습니다. 이 예제처럼 CSV 데이터를 사용하면 열 이름이 `"/document"` 아래의 노드에 매핑됩니다. 

기술에 의해 노드에 추가된 보강에 액세스하려면 보강의 전체 경로가 필요합니다. 예를 들어 ```pages``` 노드의 텍스트를 다른 기술의 입력으로 사용하려면 ```"/document/reviews_text/pages/*"```으로 지정해야 합니다.
 
 ![기술 #1 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "기술 #1 실행 후의 보강 트리")

### <a name="skill-2-language-detection"></a>기술 #2 언어 감지

호텔 리뷰 문서는 여러 언어로 표현 된 고객 의견을 포함 합니다. 언어 검색 기술은 사용 되는 언어를 결정 합니다. 그런 다음 결과를 키 구 추출 및 감정 검색으로 전달 하 여 감정와 구를 검색할 때 언어를 고려 합니다.

언어 감지 기술은 기술 세트에 정의된 세 번째(기술 #3) 기술이지만, 바로 다음으로 실행할 기술입니다. 입력을 요구하여 차단되지 않으므로 이전 기술과 병렬로 실행됩니다. 앞에 있는 분할 기술과 마찬가지로 언어 감지 기술도 문서마다 한 번씩 호출됩니다. 이제 보강 트리에는 언어에 대한 새 노드가 있습니다.

 ![기술 #2 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "기술 #2 실행 후의 보강 트리")
 
 ### <a name="skill-3-key-phrases-skill"></a>기술 #3: 핵심 문구 기술 

`/document/reviews_text/pages/*` 컨텍스트가 제공되면 `pages` 컬렉션의 항목마다 핵심 문구 기술이 한 번씩 호출됩니다. 기술의 출력은 연결된 페이지 요소 아래의 노드가 됩니다. 

 이제 기술 세트의 나머지 기술을 살펴보고 각 기술을 실행할 때마다 보강 트리가 어떻게 발전하는지 시각화할 수 있습니다. 병합 기술 및 쉐이퍼 기술 같은 일부 기술은 마찬가지로 새 노드를 만들지만, 기존 노드의 데이터만 사용하고 완전히 새로운 보강을 만들지는 않습니다.

![모든 기술 이후의 보강 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "모든 기술 후의 보강 트리")

위의 트리에서 커넥터 색은 여러 기술이 보강을 만들었으며 노드를 개별적으로 처리해야 하고 부모 노드를 선택할 때 반환되는 개체에 포함되지 않는다는 것을 나타냅니다.

## <a name="save-enrichments"></a>강화 저장

Azure Cognitive Search에서 인덱서는 생성 하는 출력을 저장 합니다. 출력 중 하나가 항상 [검색 가능한 인덱스](search-what-is-an-index.md)입니다. 인덱스를 지정 하는 것은 요구 사항이 며 기술를 연결할 때 인덱스에 의해 수집 데이터에는 강화의 내용이 포함 됩니다. 일반적으로 키 구 또는 감정 점수와 같은 특정 기술에 대 한 출력은 해당 목적으로 만든 필드의 인덱스로 수집 됩니다.

필요에 따라 인덱서는 다른 도구나 프로세스에서 사용 하기 위해 출력을 [기술 자료 저장소](knowledge-store-concept-intro.md) 로 보낼 수도 있습니다. 기술 자료 저장소는 기술의 일부로 정의 됩니다. 이 정의는 보강 문서가 테이블이 나 개체 (파일 또는 blob)로 프로젝션 되는지 여부를 결정 합니다. 테이블 형식 프로젝션은 Power BI와 같은 도구의 대화형 분석에 적합 하지만 파일 및 blob은 일반적으로 데이터 과학 또는 유사한 프로세스에서 사용 됩니다. 이 섹션에서는 기술 컴퍼지션이 프로젝트 하려는 테이블 또는 개체를 어떻게 만들 수 있는지 알아봅니다.

### <a name="projections"></a>프로젝션

기술 자료 저장소를 대상으로 하는 콘텐츠의 경우 콘텐츠를 구조화 하는 방법을 고려해 야 합니다. *프로젝션* 은 보강 트리에서 노드를 선택 하 고 기술 자료 저장소에 실제 식을 만드는 프로세스입니다. 프로젝션은 문서의 사용자 지정 셰이프(콘텐츠 및 보강)이며 테이블 또는 개체 프로젝션 출력일 수 있습니다. 프로젝션 작업에 대한 자세한 내용은 [프로젝션 작업](knowledge-store-projection-overview.md)을 참조하세요.

![필드 매핑 옵션](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "보강 파이프라인의 필드 매핑 옵션")

### <a name="sourcecontext"></a>SourceContext

`sourceContext`요소는 기술 입력 및 프로젝션 에서만 사용 됩니다. 다중 수준의 중첩된 개체를 만드는 데 사용됩니다. 기술의 입력으로 전달할 또는 지식 저장소에 프로젝션할 새 개체를 만들어야 할 수도 있습니다. 보강 노드가 보강 트리의 유효한 JSON 개체가 아닐 수도 있고 트리의 노드를 참조하면 생성 당시의 노드 상태만을 반환하므로, 보강을 기술 입력 또는 프로젝션으로 사용하려면 올바른 형식의 JSON 개체를 만들어야 합니다. `sourceContext`로 계층적 익명 형식 개체를 작성할 수 있으며, 컨텍스트만 사용하는 경우 여러 기술이 필요합니다. 

`sourceContext`다음 예제에서는를 사용 하는 방법을 보여 줍니다. 보강을 생성한 기술 출력을 살펴보고 기본 형식이 아닌 유효한 JSON 개체인지 확인하세요.

### <a name="slicing-projections"></a>프로젝션 분할

테이블 프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 여러 개의 관련 테이블로 분할할 수 있습니다. 원본 경로를 사용하여 기존 테이블 프로젝션의 자식인 테이블을 추가하면 그로 인해 생성되는 자식 노드는 기존 테이블 프로젝션의 자식이 아니라 새로운 관련 테이블에 프로젝션됩니다. 이러한 분할 기법을 사용하면 모든 테이블 프로젝션의 원본이 될 수 있는 단일 노드를 쉐이퍼 기술에 정의할 수 있습니다. 

### <a name="shaping-projections"></a>프로젝션 셰이핑

프로젝션을 정의 하는 방법에는 두 가지가 있습니다.

+ Text Shaper 기술을 사용 하 여 프로젝션 하는 모든 강화에 대 한 루트 노드인 새 노드를 만듭니다. 그런 다음, 프로젝션에서 쉐이퍼 기술의 출력만 참조할 수 있습니다.

+ 인라인 셰이프를 프로젝션 정의 자체 내에서 프로젝션으로 사용 합니다.

쉐이퍼 접근 방식은 인라인 셰이핑보다 장황하지만, 보강 트리의 모든 변화가 기술 내에 포함되고 출력은 다시 사용할 수 있는 개체입니다. 반대로 인라인 셰이핑을 사용 하면 필요한 셰이프를 만들 수 있지만 익명 개체 이며 정의 된 프로젝션 에서만 사용할 수 있습니다. 두 방식을 함께 사용할 수도 있고 따로 사용할 수도 있습니다. 포털 워크플로에서 자동으로 생성된 기술 세트에는 두 방법이 모두 포함되어 있습니다. 테이블 프로젝션에 쉐이퍼 기술을 사용하지만, 인라인 셰이핑도 사용하여 핵심 문구 테이블을 프로젝션합니다.

예제를 확장하려면 인라인 셰이핑을 제거하고 쉐이퍼 기술을 사용하여 핵심 문구에 대한 새 노드를 만들면 됩니다. `hotelReviewsDocument`, `hotelReviewsPages`, `hotelReviewsKeyPhrases`라는 세 개의 테이블에 프로젝션되는 셰이프를 만들기 위해, 다음 섹션에서 두 가지 옵션에 대해 설명합니다.

#### <a name="shaper-skill-and-projection"></a>쉐이퍼 기술 및 프로젝션

이번 

> [!Note]
> 이 예제에서는 간단한 설명을 위해 문서 테이블의 일부 열을 제거했습니다.
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

위의 `outputs` 섹션에 정의된 `tableprojection` 노드를 사용하면 이제 다음과 같이 분할 기능을 사용하여 `tableprojection` 노드의 일부를 여러 테이블로 프로젝션할 수 있습니다.

> [!Note]
> 아래는 지식 저장소 구성 내부의 프로젝션 코드 조각일 뿐입니다.
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>프로젝션 인라인 셰이핑

인라인 셰이핑 방법은 프로젝션에 필요한 모든 셰이프가 필요할 때 생성되므로 쉐이퍼 기술이 필요 없습니다. 이전 예제와 동일한 데이터를 프로젝션하기 위해, 인라인 프로젝션 옵션은 다음과 같습니다.

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
두 방법에서 잘 관찰해야 하는 것 중 하나는 `"sourceContext"`를 사용하여 `"Keyphrases"` 값을 프로젝션하는 방식입니다. 문자열 컬렉션을 포함하는 `"Keyphrases"` 노드는 그 자체가 페이지 텍스트의 자식입니다. 그러나 프로젝션에 JSON 개체가 필요하고 페이지가 기본 형식(문자열)이므로, `"sourceContext"`를 사용하여 명명된 속성이 있는 개체에 핵심 문구를 래핑합니다. 이 방법은 심지어 기본 형식도 독립적으로 프로젝션할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 인식 기술을 사용하여 여러분의 첫 번째 기술 세트를 만드는 것입니다.

> [!div class="nextstepaction"]
> [여러분의 첫 번째 기술 세트를 만들어 보세요](cognitive-search-defining-skillset.md).
