---
title: 쉐이퍼 인식 검색 기술 - Azure Search
description: 구조화되지 않은 데이터에서 메타데이터 및 구조화된 정보를 추출하고 Azure Search 보강 파이프라인에서 복합 형식으로 구성합니다.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 058b6c979346d9dcce36940432d0e222e919dba9
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540823"
---
#   <a name="shaper-cognitive-skill"></a>쉐이퍼 인식 기술

합니다 **쉐이 퍼** 기술 통합에 대 한 여러 입력을 [복합 형식](search-howto-complex-data-types.md) 보강 파이프라인에서 나중에 참조할 수 있습니다. **쉐이퍼** 기술을 사용하면 기본적으로 구조를 만들고, 해당 구조의 멤버 이름을 정의하고, 각 멤버에게 값을 할당할 수 있습니다. 검색 시나리오에서 유용한 통합된 필드의 예로 단일 구조, 도시 및 단일 구조 또는 이름에는 상태 및 고유 id를 설정 하려면 단일 구조에는 birthdate 이름과 성을 결합 합니다.

API 버전에 따라 결정 있습니다 셰이핑의 깊이 달성할 수 있습니다. 

| API 버전 | 동작을 셰이핑 | 
|-------------|-------------------|
| 2019-05-06-미리 보기 버전의 REST API (.NET SDK 지원 되지 않습니다.) | 복잡 한 개체에 여러 수준이 심층적인 하나 **쉐이 퍼** 기술 정의 합니다. |
| 2019-05-06 * * (일반 공급), 2017-11-11-미리 보기| 한 수준 더 깊을 복잡 한 개체입니다. 다중 수준 셰이프 쉐이 퍼의 몇 가지 단계를 함께 연결 해야 합니다.|

제공한 `api-version=2019-05-06-Preview`의 **쉐이 퍼** 에 설명 된 기술 [시나리오 3](#nested-complex-types) 새 선택적 *sourceContext* 속성을 입력 합니다. 합니다 *소스* 하 고 *sourceContext* 속성은 함께 사용할 수 없습니다. 기술의 컨텍스트에서 입력 인 경우 사용 하면 됩니다 *원본*합니다. 입력에 있는 경우는 *서로 다른* 기술 컨텍스트를 사용 하 여 컨텍스트를 *sourceContext*합니다. 합니다 *sourceContext* 원본으로 처리 하는 특정 요소를 사용 하 여 중첩 된 입력을 정의 해야 합니다. 

모든 API 버전에 대 한 응답으로 출력은 항상 "output"입니다. 내부적으로 통해 파이프라인 아래 예제 에서처럼 "analyzedText" 등의 다른 이름을 매핑할 수 있지만 **쉐이 퍼** 자체 기술 "출력" 응답을 반환 합니다. 이는 보강된 문서를 디버그하고 이름 불일치를 확인하거나 사용자 지정 기술을 작성하고 응답을 직접 구조화하는 경우에 중요합니다.

> [!NOTE]
> 합니다 **쉐이 퍼** 기술 Cognitive Services API에 바인딩되지 않은 및 사용에 대 한 요금이 부과 되지 않습니다. 그렇지만 **무료** 리소스 옵션을 재정의하여 일별 보강 횟수를 낮게 제한하려면 계속해서 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>시나리오 1: 복합 형식

각각 두 개의 멤버(*text* 및 *sentiment*)가 있는 *analyzedText*라는 구조를 만드는 시나리오를 가정해 보세요. Azure Search 인덱스에서 다중 파트 검색 가능한 필드 라고 한 *복합 형식* 원본 데이터에 매핑되는 해당 복합 구조를 종종 만들어집니다.

다른 방법은 복합 형식을 만드는 것 인데, 통해 합니다 **쉐이 퍼** 기술 합니다. 이 기술 기술 집합에 넣어 기술 처리 하는 동안 메모리 내 작업 인덱스에서 복합 형식에 다음 매핑될 수 있는 중첩 된 구조를 사용 하 여 데이터 셰이프를 출력할 수 있습니다. 

다음 예제에서는 기술 정의 멤버 이름을 입력으로 제공합니다. 


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

기술 집합 인덱서를 호출한 하며 인덱서는 인덱스입니다. 인덱스의 복잡 한 필드 표현을 다음과 같이 보일 수 있습니다. 

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

사용 가능한 입력이 제공 하는 들어오는 JSON 문서 **쉐이 퍼** 기술 일 수 있습니다.

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

**쉐이퍼** 기술은 텍스트 및 *text* 및 *sentiment*의 결합된 요소를 사용하여 *analyzedText*라는 새 요소를 생성합니다. 이 출력은 인덱스 스키마를 따릅니다. 가져온 및 Azure Search 인덱스에서 인덱싱된 수 됩니다.

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

## <a name="scenario-2-input-consolidation"></a>시나리오 2: 입력된 통합

다른 예제에서는 파이프라인 처리의 각기 다른 단계에서 책 제목과 책의 여러 페이지에 있는 장 제목을 추출했다고 가정합니다. 이제 다양한 입력으로 구성된 단일 구조를 만들 수 있습니다.

합니다 **쉐이 퍼** 이 시나리오에 대 한 기술 정의 다음과 같이 표시 될 수 있습니다.

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
이 경우에 **쉐이 퍼** 단일 배열을 만들려면 모든 제목을 평면화 합니다. 

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

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>시나리오 3: 중첩 된 컨텍스트에서 입력된 통합

> [!NOTE]
> 중첩 된 구조에서 지원 합니다 [REST API 버전 2019-05-06-미리 보기](search-api-preview.md) 에서 사용할 수 있습니다를 [기술 저장소](knowledge-store-concept-intro.md) 또는 Azure Search 인덱스입니다.

제목, 장 및 책의 콘텐츠 및 실행 엔터티 인식 및 키 구 내용에 있고 이제 해야 결과 집계 하려면 다른 기술에서 장 이름, 엔터티 및 핵심 문구를 사용 하 여 단일 셰이프로 한다고 가정 합니다.

합니다 **쉐이 퍼** 이 시나리오에 대 한 기술 정의 다음과 같이 표시 될 수 있습니다.

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
이 경우에 **쉐이 퍼** 복잡 한 형식을 만듭니다. 이 구조는 메모리에 존재합니다. 정보 저장소에 저장 하려는 경우 저장소 특성을 정의 하는 역량에 프로젝션을 만들어야 합니다.

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

## <a name="see-also"></a>참고 항목

+ [미리 정의된 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)
+ [복합 유형을 사용 하는 방법](search-howto-complex-data-types.md)
+ [기술 스토어 개요](knowledge-store-concept-intro.md)
+ [정보 저장소를 사용 하 여 시작 하는 방법](knowledge-store-howto.md)