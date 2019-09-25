---
title: 쉐이퍼 인식 검색 기술 - Azure Search
description: 구조화되지 않은 데이터에서 메타데이터 및 구조화된 정보를 추출하고 Azure Search 보강 파이프라인에서 복합 형식으로 구성합니다.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 84814c317a945fd22ada580dcc3f64ed2adcff7c
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265345"
---
#   <a name="shaper-cognitive-skill"></a>쉐이퍼 인식 기술

**Shaper** 기술은 나중에 보강 파이프라인에서 참조할 수 있는 [복합 형식](search-howto-complex-data-types.md) 으로 여러 입력을 통합 합니다. **쉐이퍼** 기술을 사용하면 기본적으로 구조를 만들고, 해당 구조의 멤버 이름을 정의하고, 각 멤버에게 값을 할당할 수 있습니다. 검색 시나리오에서 유용 하 게 사용할 수 있는 통합 필드의 예로는 이름 및 성을 단일 구조, 도시 및 주를 단일 구조로 결합 하거나 이름 및 생년월일을 단일 구조로 결합 하 여 고유한 id를 설정 하는 것이 있습니다.

또한 [시나리오 3](#nested-complex-types) 에 설명 된 **Shaper** 기술은 입력에 선택적 *sourcecontext* 속성을 추가 합니다. *원본* 및 *sourcecontext* 속성은 함께 사용할 수 없습니다. 입력이 기술 컨텍스트에 있는 경우 *소스*를 사용 하면 됩니다. 입력이 기술 컨텍스트와 *다른* 컨텍스트에 있는 경우 *sourcecontext*를 사용 합니다. *Sourcecontext* 에서는 원본으로 지정 된 특정 요소를 사용 하 여 중첩 된 입력을 정의 해야 합니다. 

출력 이름은 항상 "output"입니다. 내부적으로 파이프라인은 아래 예제와 같이 "analyzedText"와 같은 다른 이름을 매핑할 수 있지만 **Shaper** 기술 자체는 응답에서 "출력"을 반환 합니다. 이는 보강된 문서를 디버그하고 이름 불일치를 확인하거나 사용자 지정 기술을 작성하고 응답을 직접 구조화하는 경우에 중요합니다.

> [!NOTE]
> **Shaper** 기술은 Cognitive Services API에 바인딩되지 않으며 사용에 대 한 요금이 청구 되지 않습니다. 그렇지만 **무료** 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>시나리오 1: 복합 형식

각각 두 개의 멤버(*text* 및 *sentiment*)가 있는 *analyzedText*라는 구조를 만드는 시나리오를 가정해 보세요. Azure Search 인덱스에서 다중 파트 검색 가능 필드를 *복합 형식* 이라고 하며, 원본 데이터에 매핑되는 해당 하는 복잡 한 구조를 포함 하는 경우가 많습니다.

그러나 복합 형식을 만들기 위한 또 다른 방법은 **Shaper** 기술을 사용 하는 것입니다. 기술에이 기술을 포함 하 여 기술를 처리 하는 동안 메모리 내 작업은 중첩 된 구조로 데이터 셰이프를 출력할 수 있으며,이는 인덱스의 복합 형식에 매핑될 수 있습니다. 

다음 예제 기술 정의에서는 멤버 이름을 입력으로 제공 합니다. 


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

기술는 인덱서에 의해 호출 되며 인덱서는 인덱스를 필요로 합니다. 인덱스의 복합 필드 표현은 다음 예제와 같습니다. 

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

이 **Shaper** 기술에 사용할 수 있는 입력을 제공 하는 들어오는 JSON 문서는 다음과 같을 수 있습니다.

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

**쉐이퍼** 기술은 텍스트 및 *text* 및 *sentiment*의 결합된 요소를 사용하여 *analyzedText*라는 새 요소를 생성합니다. 이 출력은 인덱스 스키마를 따릅니다. Azure Search 인덱스에서 가져오고 인덱싱됩니다.

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

이 시나리오에 대 한 **Shaper** 기술 정의는 다음 예제와 같이 보일 수 있습니다.

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
이 경우 **Shaper** 는 모든 장 제목을 평면화 하 여 단일 배열을 만듭니다. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>시나리오 3: 중첩 된 컨텍스트에서 입력 통합

책의 제목, 장 및 내용이 있고 콘텐츠에 대 한 엔터티 인식과 키 구를 실행 하 고, 이제 여러 기술의 결과를 장 이름, 엔터티 및 키 구가 포함 된 단일 셰이프로 집계 해야 한다고 가정 합니다.

이 시나리오에 대 한 **Shaper** 기술 정의는 다음 예제와 같이 보일 수 있습니다.

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
이 경우 **Shaper** 는 복합 형식을 만듭니다. 이 구조는 메모리 내에 있습니다. [기술 자료 저장소](knowledge-store-concept-intro.md)에 저장 하려면 저장소 특성을 정의 하는 기술에서 프로젝션을 만들어야 합니다.

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

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [복합 형식을 사용 하는 방법](search-howto-complex-data-types.md)
+ [지식 저장소 개요](knowledge-store-concept-intro.md)
+ [기술 자료 저장소를 시작 하는 방법](knowledge-store-howto.md)