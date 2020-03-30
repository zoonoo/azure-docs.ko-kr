---
title: 기술 집합 개념 및 워크플로우
titleSuffix: Azure Cognitive Search
description: 기술 집합은 Azure 인지 검색에서 AI 보강 파이프라인을 작성하는 곳입니다. 기술 집합 구성에 대한 중요한 개념과 세부 정보를 알아봅니다.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8b45840215092281c7fbc8d499e26b095b374dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191037"
---
# <a name="skillset-concepts-and-composition-in-azure-cognitive-search"></a>Azure 인지 검색의 기술 집합 개념 및 구성

이 문서는 보강 파이프라인의 작동 방식에 대한 심층적인 이해가 필요한 개발자를 위한 것이며 AI 보강 프로세스에 대한 개념적 이해가 있다고 가정합니다. 이 개념을 새로 신는 경우 다음부터 시작하십시오.
+ [Azure 인지 검색의 AI 강화](cognitive-search-concept-intro.md)
+ [지식 저장소(미리 보기)](knowledge-store-concept-intro.md)

## <a name="specify-the-skillset"></a>기술 집합 지정
기술 집합은 Azure Cognitive Search에서 인덱싱 중에 텍스트 또는 이미지 콘텐츠를 분석, 변환 및 보강하는 데 사용되는 인지 기술 모음을 지정하는 재사용 가능한 리소스입니다. 기술 집합을 만들면 데이터 수집 단계에서 텍스트 및 이미지 보강을 첨부하여 원시 콘텐츠에서 새 정보와 구조를 추출하고 만들 수 있습니다.

기술 집합에는 세 가지 속성이 있습니다.

+   ```skills```, 플랫폼이 각 기술에 필요한 입력에 따라 실행 순서를 결정하는 기술의 순서가 지정되지 않은 컬렉션입니다.
+   ```cognitiveServices```, 호출 된 인지 능력을 청구하는 데 필요한 인지 서비스 키
+   ```knowledgeStore```, 풍부한 문서가 투영되는 저장소 계정



기술 집합은 JSON에서 작성되었습니다. [표현식 언어를](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional)사용하여 반복 및 분기를 사용하여 복잡한 기술 [집합을](https://docs.microsoft.com/azure/search/cognitive-search-skill-conditional) 빌드할 수 있습니다. 식 언어는 보강 트리의 노드를 식별하기 위해 몇 가지 수정 사항과 함께 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 경로 표기술을 사용합니다. A는 ```"/"``` 트리에서 낮은 레벨을 ```"*"``` 통과하고 컨텍스트에서 각 연산자 역할을 합니다. 이러한 개념은 예제와 함께 가장 잘 설명됩니다. 몇 가지 개념과 기능을 설명하기 위해 호텔 리뷰 샘플 기술 [집합을 살펴보겠습니다.](knowledge-store-connect-powerbi.md) 데이터 가져오기 워크플로를 수행한 후 기술 집합을 보려면 REST API 클라이언트를 사용하여 [기술 집합을 가져와야](https://docs.microsoft.com/rest/api/searchservice/get-skillset)합니다.

### <a name="enrichment-tree"></a>농축 트리

기술 집합이 문서를 점진적으로 보강하는 방법을 구상하려면 보강 하기 전에 문서의 모양부터 시작해 보겠습니다. 문서 크래킹의 출력은 선택한 데이터 원본및 특정 구문 분석 모드에 따라 달라집니다. 또한 [필드 매핑이](search-indexer-field-mappings.md) 검색 인덱스에 데이터를 추가할 때 콘텐츠를 소스화할 수 있는 문서의 상태입니다.
![파이프라인의 지식 저장소 다이어그램](./media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png "파이프라인의 지식 저장소 다이어그램")

문서가 보강 파이프라인에 있으면 콘텐츠 트리 및 관련 보강으로 표시됩니다. 이 트리는 문서 크래킹의 출력으로 인스턴스화됩니다. 보강 트리 형식을 사용하면 보강 파이프라인이 기본 데이터 형식에도 메타데이터를 연결할 수 있으며 유효한 JSON 개체는 아니지만 유효한 JSON 형식으로 프로젝트할 수 있습니다. 다음 표에서는 보강 파이프라인에 입력하는 문서의 상태를 보여 주십을 보여 주십습니다.

|데이터 소스\구문 분석 모드|기본값|JSON, JSON 라인 & CSV|
|---|---|---|
|Blob Storage|/문서/콘텐츠<br>/문서/normalized_images/*<br>…|/문서/{key1}<br>/문서/{key2}<br>…|
|SQL|/문서/{열1}<br>/문서/{열2}<br>…|해당 없음 |
|Cosmos DB|/문서/{key1}<br>/문서/{key2}<br>…|해당 없음|

 기술이 실행되면 보강 트리에 새 노드를 추가합니다. 그런 다음 이러한 새 노드를 다운스트림 기술, 지식 저장소에 투영또는 인덱스 필드에 매핑하는 입력으로 사용할 수 있습니다. 보강은 변경할 수 없습니다: 일단 생성되면 노드를 편집할 수 없습니다. 기술 집합이 더 복잡해짐에 따라 보강 트리가 복잡해지지만 보강 트리의 모든 노드가 인덱스 나 지식 저장소로 만들어야 하는 것은 아닙니다. 

인덱스 또는 기술 저장소에 보강의 하위 집합만 선택적으로 유지시킬 수 있습니다.
이 문서의 나머지 부분에서는 호텔 리뷰 [예제로](https://docs.microsoft.com/azure/search/knowledge-store-connect-powerbi)작업하고 있다고 가정하지만 다른 모든 데이터 원본의 문서를 보강하는 데 동일한 개념이 적용됩니다.

### <a name="context"></a>Context
각 기술에는 컨텍스트가 필요합니다. 컨텍스트에 따라 다음이 결정됩니다.
+   선택한 노드에 따라 기술이 실행되는 횟수입니다. 형식 컬렉션의 컨텍스트 값의 ```/*``` 경우 끝에 를 추가하면 컬렉션의 각 인스턴스에 대해 기술이 한 번 호출됩니다. 
+   농축 트리에서 기술 출력이 추가되는 위치입니다. 출력은 항상 컨텍스트 노드의 자식으로 트리에 추가됩니다. 
+   입력의 모양입니다. 다중 레벨 컬렉션의 경우 컨텍스트를 부모 컬렉션으로 설정하면 기술에 대한 입력 의 모양에 영향을 미칩니다. 예를 들어 국가 목록이 있는 보강 트리가 있는 경우 각 국가 번호가 포함된 상태 목록이 보강됩니다.

|Context|입력|입력의 모양|기술 호출|
|---|---|---|---|
|```/document/countries/*``` |```/document/countries/*/states/*/zipcodes/*``` |국가의 모든 우편 번호 목록 |국가당 1회 |
|```/document/countries/*/states/*``` |```/document/countries/*/states/*/zipcodes/*``` |주의 우편 번호 목록 | 국가 및 주 조합당 1회|

### <a name="sourcecontext"></a>소스 컨텍스트

이는 `sourceContext` 기술 입력 및 [프로젝션에만 사용됩니다.](knowledge-store-projection-overview.md) 다중 레벨, 중첩 된 개체를 구성 하는 데 사용 됩니다. 기술 또는 프로젝트에 대한 입력으로 지식 저장소에 전달하기 위해 새 개체를 만들어야 할 수 있습니다. 보강 노드는 보강 트리에서 유효한 JSON 개체가 아닐 수 있으며 트리의 노드를 참조하면 노드가 생성될 때 해당 노드 상태만 반환하므로 보강을 기술 입력 또는 프로젝션으로 사용하려면 잘 구성된 JSON 개체를 만들어야 합니다. 을 `sourceContext` 사용하면 컨텍스트만 사용하는 경우 여러 기술이 필요한 계층적이고 익명 형식의 개체를 생성할 수 있습니다. 사용은 `sourceContext` 다음 섹션에 표시됩니다. 보강을 생성한 기술 출력을 확인하여 기본 형식이 아닌 유효한 JSON 개체인지 확인합니다.

### <a name="projections"></a>프로젝션

프로젝션은 지식 저장소에 저장할 보강 트리에서 노드를 선택하는 프로세스입니다. 프로젝션은 테이블 또는 개체 프로젝션으로 출력할 수 있는 문서(콘텐츠 및 보강)의 사용자 지정 셰이프입니다. 프로젝션 작업에 대해 자세히 알아보려면 [프로젝션 작업을](knowledge-store-projection-overview.md)참조하십시오.

![필드 매핑 옵션](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "보강 파이프라인을 위한 필드 매핑 옵션")

위의 다이어그램은 보강 파이프라인의 위치를 기반으로 작업하는 선택기에 대해 설명합니다.

## <a name="generate-enriched-data"></a>보강된 데이터 생성 

이제 호텔 리뷰 기술 집합을 단계별로 살펴보고 [자습서를](knowledge-store-connect-powerbi.md) 따라 기술 집합을 만들거나 기술 집합을 [볼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/samples/skillset.json) 수 있습니다. 우리는 살펴 볼 것입니다 :

* 각 기술의 실행과 함께 보강 트리가 어떻게 진화하는지 
* 컨텍스트 및 입력이 작동하는 방식에 따라 기술이 실행되는 횟수를 결정합니다. 
* 입력의 모양이 컨텍스트를 기반으로 하는 정보입니다. 

인덱서에 대해 구분된 텍스트 구문 분석 모드를 사용하기 때문에 보강 프로세스 내의 문서는 CSV 파일 내의 단일 행을 나타냅니다.

### <a name="skill-1-split-skill"></a>스킬 #1: 스킬 분할 

![문서 균열 후 농축 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "문서 크래킹 후 및 기술 실행 전 보강 트리")

의 기술 컨텍스트를 ```"/document/reviews_text"```사용하면 이 기술은 `reviews_text`에 대해 한 번 실행됩니다. 스킬 출력은 5000자 세그먼트로 청크되는 목록입니다. `reviews_text` 분할 기술의 출력이름이 지정되고 `pages` 보강 트리에 추가됩니다. 이 `targetName` 기능을 사용하면 보강 트리에 추가되기 전에 기술 출력의 이름을 바꿀 수 있습니다.

보강 트리에 기술의 컨텍스트 아래에 새 노드가 배치되었습니다. 이 노드는 모든 기술, 프로젝션 또는 출력 필드 매핑에서 사용할 수 있습니다.


모든 보강에 대한 루트 `"/document"`노드는 . Blob 인덱서로 작업할 때 노드에는 `"/document"` `"/document/content"` 의 `"/document/normalized_images"`자식 노드가 있습니다. CSV 데이터로 작업할 때 이 예제에서와 같이 열 이름은 `"/document"`아래의 노드에 매핑됩니다. 기술에 의해 노드에 추가된 보강에 액세스하려면 보강을 위한 전체 경로가 필요합니다. 예를 들어 ```pages``` 노드의 텍스트를 다른 기술에 대한 입력으로 사용하려면 을 로 ```"/document/reviews_text/pages/*"```지정해야 합니다.
 
 ![스킬 #1 후 농축 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "스킬 #1 실행된 후 보강 트리")

### <a name="skill-2-language-detection"></a>기술 #2 언어 감지
 언어 탐지 기술은 스킬세트에 정의된 세 번째(스킬 #3) 스킬이지만, 다음 스킬을 실행하는 기술입니다. 입력을 요구하여 차단되지 않으므로 이전 기술과 병렬로 실행됩니다. 앞에 놓인 분할 기술과 마찬가지로 언어 검색 기술도 각 문서에 대해 한 번 호출됩니다. 보강 트리에 언어에 대한 새 노드가 생겼습니다.
 ![스킬 #2 후 농축 트리](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "스킬 #2 실행된 후 보강 트리")
 
 ### <a name="skill-3-key-phrases-skill"></a>스킬 #3: 핵심 구문 기술 

키 구의 ```/document/reviews_text/pages/*``` 컨텍스트를 감안할 때 스킬은 `pages` 컬렉션의 각 항목에 대해 한 번 호출됩니다. 기술의 출력은 연결된 페이지 요소 아래의 노드가 됩니다. 

 이제 기술 집합의 나머지 기술을 살펴보고 각 기술의 실행과 함께 농축 나무가 어떻게 계속 성장할지 시각화할 수 있습니다. 병합 기술 및 셰이퍼 기술과 같은 일부 기술도 새 노드를 만들지만 기존 노드의 데이터만 사용하고 순 새 보강을 만들지 않습니다.

![모든 기술 후 농축 나무](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "모든 기술 후 농축 트리")

위의 트리에 있는 연결선의 색상은 보강이 서로 다른 기술에 의해 만들어졌음을 나타내며 노드는 개별적으로 해결해야 하며 상위 노드를 선택할 때 반환되는 개체의 일부가 아님을 나타냅니다.

## <a name="save-enrichments-in-a-knowledge-store"></a>지식 저장소에 농축 물 저장 

또한 기술 집합은 풍부한 문서를 테이블이나 개체로 투영할 수 있는 지식 저장소를 정의합니다. 기술 저장소에 보강된 데이터를 저장하려면 보강된 문서에 대한 투영 집합을 정의합니다. 기술 저장소에 대한 자세한 내용은 [지식 저장소 개요를](knowledge-store-concept-intro.md) 참조하십시오.

### <a name="slicing-projections"></a>슬라이스 프로젝션

테이블 프로젝션 그룹을 정의할 때 보강 트리의 단일 노드를 여러 관련 테이블로 분할할 수 있습니다. 기존 테이블 프로젝션의 자식인 소스 경로가 있는 테이블을 추가하는 경우 결과 자식 노드는 기존 테이블 프로젝션의 자식이 아니라 대신 관련된 새 테이블에 투영됩니다. 이 슬라이싱 기술을 사용하면 모든 테이블 프로젝션의 소스가 될 수 있는 셰이퍼 기술의 단일 노드를 정의할 수 있습니다. 

### <a name="shaping-projections"></a>프로젝션 쉐이핑

투영을 정의하는 방법에는 두 가지가 있습니다. 셰이퍼 기술을 사용하여 프로젝션하는 모든 보강에 대한 루트 노드인 새 노드를 만들 수 있습니다. 그런 다음 프로젝션에서 셰이퍼 기술의 출력만 참조합니다. 투영 정의 자체 내에서 투영을 인라인으로 정렬할 수도 있습니다.

셰이퍼 접근 방식은 인라인 셰이핑보다 더 자세한 내용이지만 보강 트리의 모든 돌연변이가 기술 내에 포함되어 있고 출력이 재사용할 수 있는 개체임을 보장합니다. 인라인 셰이핑을 사용하면 필요한 셰이프를 만들 수 있지만 익명 객체이며 정의된 투영에서만 사용할 수 있습니다. 이 방법은 함께 또는 별도로 사용할 수 있습니다. 포털 워크플로에서 만든 기술 집합에는 둘 다 포함됩니다. 테이블 프로젝션에 셰이퍼 기술을 사용하지만 인라인 셰이핑을 사용하여 키 구 테이블을 투영합니다.

예제를 확장하려면 인라인 셰이핑을 제거하고 셰이퍼 기술을 사용하여 키 구에 대한 새 노드를 만들 수 있습니다. 세 개의 테이블, 즉 `hotelReviewsDocument`, 및 `hotelReviewsPages` `hotelReviewsKeyPhrases`, 두 가지 옵션에 투영된 셰이프를 만들려면 다음 섹션에 설명되어 있습니다.


#### <a name="shaper-skill-and-projection"></a>셰이퍼 기술 및 프로젝션 

> [!Note]
> 간결성을 위해 이 예제에서 문서 테이블의 일부 열이 제거되었습니다.
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

위의 `tableprojection` 섹션에 정의된 `outputs` 노드를 사용하여 슬라이스 기능을 사용하여 `tableprojection` 노드의 일부를 다른 테이블로 투영할 수 있습니다.

> [!Note]
> 이는 지식 저장소 구성 내에서 프로젝션의 스니펫일 뿐입니다.
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

#### <a name="inline-shaping-projections"></a>인라인 쉐이핑 프로젝션

인라인 셰이핑 접근 방식은 필요한 시점에 프로젝션에 필요한 모든 셰이프가 만들어지기 때문에 셰이퍼 기술이 필요하지 않습니다. 이전 예제와 동일한 데이터를 프로젝션하려면 인라인 프로젝션 옵션이 다음과 같습니다.

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
  
두 방법 모두에서 한 가지 관찰은 `"Keyphrases"` `"sourceContext"`의 값을 사용하여 투영하는 방법입니다. 문자열 `"Keyphrases"` 컬렉션을 포함하는 노드는 그 자체로 페이지 텍스트의 자식입니다. 그러나 프로젝션에는 JSON 개체가 필요하고 페이지가 기본(문자열)이기 때문에 키 구를 명명된 속성이 있는 개체로 래핑하는 데 `"sourceContext"` 사용됩니다. 이 기술을 사용하면 프리미티브도 독립적으로 투영할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계로, 인지 능력으로 첫 번째 기술을 만들 수 있습니다.

> [!div class="nextstepaction"]
> [첫 번째 기술 집합을 만듭니다.](cognitive-search-defining-skillset.md)
