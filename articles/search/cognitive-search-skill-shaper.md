---
title: 쉐이퍼 인식 기술
titleSuffix: Azure Cognitive Search
description: 비정형 데이터에서 메타데이터 및 구조화 된 정보를 추출하고 Azure Cognitive Search의 AI 보강 파이프라인에서 복잡한 유형으로 형성합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754123"
---
# <a name="shaper-cognitive-skill"></a>쉐이퍼 인식 기술

**Shaper** 기술은 보강 파이프라인의 나중에 참조할 수 있는 [복잡한 유형으로](search-howto-complex-data-types.md) 여러 입력을 통합합니다. **Shaper** 기술을 사용하면 기본적으로 구조를 만들고, 해당 구조체의 멤버 이름을 정의하고, 각 멤버에 값을 할당할 수 있습니다. 검색 시나리오에서 유용한 통합 필드의 예로는 이름과 성을 단일 구조로 결합하거나, 도시와 상태를 단일 구조로 결합하거나, 이름과 생년월일을 단일 구조로 결합하여 고유한 ID를 설정하는 예제입니다.

또한 [시나리오 3에](#nested-complex-types) 설명된 **셰이퍼** 기술은 입력에 선택적 *sourceContext* 속성을 추가합니다. *소스* 및 *sourceContext* 속성은 상호 배타적입니다. 입력이 기술의 컨텍스트에 있는 경우 *소스를*사용하기만 하면 됩니다. 입력이 기술 컨텍스트와 *다른* 컨텍스트에 있는 경우 *sourceContext*를 사용합니다. *sourceContext를* 사용하려면 중첩된 입력을 정의해야 하며 특정 요소가 소스로 처리됩니다. 

출력 이름은 항상 "출력"입니다. 내부적으로 파이프라인은 아래 예제와 같이 "분석된Text"와 같은 다른 이름을 매핑할 수 있지만 **셰이퍼** 기술 자체는 응답에서 "출력"을 반환합니다. 이는 보강된 문서를 디버그하고 이름 불일치를 확인하거나 사용자 지정 기술을 작성하고 응답을 직접 구조화하는 경우에 중요합니다.

> [!NOTE]
> **셰이퍼** 기술은 코그너티브 서비스 API에 바인딩되지 않으며 사용 요금이 부과되지 않습니다. 그렇지만 **무료** 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>시나리오 1: 복잡한 형식

각각 두 개의 멤버(*text* 및 *sentiment*)가 있는 *analyzedText*라는 구조를 만드는 시나리오를 가정해 보세요. 인덱스에서 다중 부분 검색 가능한 필드를 *복합 형식이라고* 하며 원본 데이터에 매핑되는 해당 복잡한 구조가 있을 때 종종 생성됩니다.

그러나 복잡한 형식을 만드는 또 다른 방법은 **Shaper** 기술을 통해서입니다. 기술 집합에 이 기술을 포함하면 기술 집합 처리 중 메모리 내 작업은 중첩된 구조로 데이터 셰이프를 출력할 수 있으며, 이 작업은 인덱스의 복잡한 형식에 매핑될 수 있습니다. 

다음 예제 기술 정의는 멤버 이름을 입력으로 제공합니다. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>샘플 인덱스

기술 집합은 인덱서에 의해 호출되고 인덱서에는 인덱스가 필요합니다. 인덱스의 복잡한 필드 표현은 다음 예제와 같을 수 있습니다. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>기술 입력

이 **셰이퍼** 기술에 사용할 수 있는 입력을 제공하는 들어오는 JSON 문서는 다음과 같은 것일 수 있습니다.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>기술 출력

**셰이퍼** 기술은 분석이라는 새로운 요소를 *생성텍스트와* *감정의*결합 된 요소 . *text* 이 출력은 인덱스 스키마를 준수합니다. Azure 인지 검색 인덱스에서 가져오고 인덱싱됩니다.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>시나리오 2: 입력 통합

다른 예제에서는 파이프라인 처리의 각기 다른 단계에서 책 제목과 책의 여러 페이지에 있는 장 제목을 추출했다고 가정합니다. 이제 다양한 입력으로 구성된 단일 구조를 만들 수 있습니다.

이 시나리오의 **셰이퍼** 기술 정의는 다음과 같습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>기술 출력
이 경우 **Shaper는** 모든 장 제목을 평평하게 하여 단일 배열을 만듭니다. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>시나리오 3: 중첩된 컨텍스트의 입력 통합

책의 제목, 장 및 내용을 가지고 있고 내용에 대한 엔터티 인식 및 핵심 구를 실행한 후 다른 기술의 결과를 장 이름, 엔터티 및 핵심 구와 함께 단일 모양으로 집계해야 한다고 가정해 보겠습니다.

이 시나리오의 **셰이퍼** 기술 정의는 다음과 같습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>기술 출력
이 경우 **셰이퍼는** 복잡한 형식을 만듭니다. 이 구조는 메모리에 존재합니다. [지식 저장소에](knowledge-store-concept-intro.md)저장하려면 저장소 특성을 정의하는 기술 집합에 프로젝션을 만들어야 합니다.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [복잡한 형식을 사용하는 방법](search-howto-complex-data-types.md)
+ [지식 저장소(미리 보기)](knowledge-store-concept-intro.md)
+ [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)