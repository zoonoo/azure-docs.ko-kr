---
title: 데이터 추출
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding(LUIS)에서 추출할 수 있는 데이터 형식 알아보기
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 3d9c2a694562977c2a012d9faa9c282c6b8fff9d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099834"
---
# <a name="data-extraction-from-intents-and-entities"></a>의도 및 엔터티에서 데이터 추출
LUIS는 사용자의 자연어 발화에서 정보를 가져오는 기능을 제공합니다. 정보는 프로그램, 애플리케이션 또는 챗봇에서 작업을 수행하는 데 사용할 수 있는 방법으로 추출됩니다. 다음 섹션에서는 JSON 예제와 함께 의도 및 엔터티에서 반환되는 데이터에 대해 알아봅니다.

정확히 일치하는 텍스트가 아니므로 추출하기 가장 어려운 데이터는 기계 학습 데이터입니다. 기계 학습 [엔터티](luis-concept-entity-types.md)의 데이터 추출은 사용자가 예상한 데이터를 받는다고 확신할 때까지 [작성 주기](luis-concept-app-iteration.md)에 포함되어야 합니다.

## <a name="data-location-and-key-usage"></a>데이터 위치 및 키 사용
LUIS는 게시된 [엔드포인트](luis-glossary.md#endpoint)의 데이터를 제공합니다. **HTTPS 요청**(POST 또는 GET)에는 스테이징 또는 프로덕션 환경과 같은 일부 선택적 구성과 발화가 포함됩니다.

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID`는 LUIS 앱을 편집하는 동안 URL의 일부(`/apps/` 뒤) 및 LUIS 앱의 **설정** 페이지에서 사용할 수 있습니다. `subscription-key`는 앱을 쿼리하는 데 사용되는 엔드포인트 키입니다. LUIS를 학습시키는 동안 체험 작성/시작 키를 사용할 수 있지만, 엔드포인트 키를 [필요한 LUIS 사용](luis-boundaries.md#key-limits)을 지원하는 키로 변경해야 합니다. `timezoneOffset` 단위는 분입니다.

**HTTPS 응답**에는 스테이징 또는 프로덕션 엔드포인트의 현재 게시된 모델을 기반으로 LUIS가 확인할 수 있는 모든 의도 및 엔터티 정보가 포함됩니다. 엔드포인트 URL은 [LUIS](luis-reference-regions.md) 웹 사이트의 **관리** 섹션에 있는 **키 및 엔드포인트** 페이지에서 찾을 수 있습니다.

## <a name="data-from-intents"></a>의도의 데이터
기본 데이터는 상위 점수 **의도 이름**입니다. `MyStore`[빠른 시작](luis-quickstart-intents-only.md)을 사용할 경우 엔드포인트 응답은 다음과 같습니다.

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|데이터 개체|데이터 형식|데이터 위치|값|
|--|--|--|--|
|의도|문자열|topScoringIntent.intent|“GetStoreInfo”|

챗봇 또는 LUIS 호출 앱이 둘 이상의 의도 점수를 기반으로 결정을 내리는 경우 쿼리 문자열 매개 변수, `verbose=true`를 설정하여 모든 의도의 점수를 반환합니다. 엔드포인트 응답은 다음과 같습니다.

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

의도는 최고 점수에서 최하 점수 순으로 정렬됩니다.

|데이터 개체|데이터 형식|데이터 위치|값|Score|
|--|--|--|--|:--|
|의도|문자열|intents[0].intent|“GetStoreInfo”|0.984749258|
|의도|문자열|intents[1].intent|“None”|0.0168218873|

미리 빌드된 도메인을 추가하는 경우, 의도 이름은 의도뿐 아니라 `Utilties` 또는 `Communication`와 같은 도메인을 나타냅니다.

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

|도메인|데이터 개체|데이터 형식|데이터 위치|값|
|--|--|--|--|--|
|공공 시설|의도|문자열|intents[0].intent|“<b>Utilities</b>.ShowNext”|
|통신|의도|문자열|intents[1].intent|<b>Communication</b>.StartOver”|
||의도|문자열|intents[2].intent|“None”|


## <a name="data-from-entities"></a>엔터티의 데이터
대부분의 챗봇 및 애플리케이션에는 의도 이름 외에 더 많은 것이 필요합니다. 이 추가적이고 선택적인 데이터는 발화에서 검색되는 엔터티에서 가져옵니다. 각 엔터티 형식은 서로 다른 일치 정보를 반환합니다.

발화의 단일 단어 또는 구문이 둘 이상의 엔터티와 일치할 수 있습니다. 이 경우, 각 일치하는 엔터티는 점수와 함께 반환됩니다.

모든 엔터티는 엔드포인트에서 응답의 **엔터티** 배열로 반환됩니다.

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>토큰화된 엔터티가 반환됨
여러 [문화권](luis-language-support.md#tokenization)은 [토큰화된](luis-glossary.md#token) `entity` 값과 함께 엔터티 개체를 반환합니다. LUIS에서 엔터티 개체로 반환한 startIndex 및 endIndex는 새 토큰화된 값에 매핑되지 않고, 원시 엔터티를 프로그래밍 방식으로 추출하기 위해 원래 쿼리에 매핑됩니다. 

예를 들어, 독일어에서 `das Bauernbrot` 단어는 `das bauern brot`로 토큰화됩니다. 토큰화된 값 `das bauern brot`가 반환되고 원래 값은 원본 쿼리의 startIndex 및 endIndex에서 프로그래밍 방식으로 되어 `das Bauernbrot`가 제공될 수 있습니다.

## <a name="simple-entity-data"></a>단순 엔터티 데이터

[단순 엔터티](luis-concept-entity-types.md)는 기계 학습 값입니다. 이 값은 단어 또는 구문일 수 있습니다.

`Bob Jones wants 3 meatball pho`

이전 발화에서 `Bob Jones`는 단순 `Customer` 엔터티로 레이블이 지정됩니다.

엔드포인트에서 반환된 데이터에는 엔터티 이름, 발화에서 검색된 텍스트, 검색된 텍스트의 위치 및 점수가 포함됩니다.

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|데이터 개체|엔터티 이름|값|
|--|--|--|
|단순 엔터티|“Customer”|“bob jones”|

## <a name="hierarchical-entity-data"></a>계층적 엔터티 데이터

[계층적](luis-concept-entity-types.md) 엔터티는 기계 학습 엔터티이며 단어나 구문을 포함할 수 있습니다. 자식은 컨텍스트로 식별됩니다. 텍스트가 정확히 일치하는 부모-자식 관계를 검색하려면 [목록](#list-entity-data) 엔터티를 사용합니다.

`book 2 tickets to paris`

이전 발화에서 `paris`는 `Location` 계층적 엔터티의 `Location::ToLocation` 자식에 레이블로 지정됩니다.

엔드포인트에서 반환된 데이터에는 엔터티 이름 및 자식 이름, 발화에서 검색된 텍스트, 검색된 텍스트의 위치 및 점수가 포함됩니다.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|데이터 개체|부모|자식|값|
|--|--|--|--|
|계층적 엔터티|위치|ToLocation|“paris”|

## <a name="composite-entity-data"></a>복합 엔터티 데이터
[복합](luis-concept-entity-types.md) 엔터티는 기계 학습 엔터티이며 단어나 구문을 포함할 수 있습니다. 예를 들어, 다음 발화를 사용하여 미리 빌드된 `number` 및 `Location::ToLocation`의 복합 엔터티를 고려해 보세요.

`book 2 tickets to paris`

`2`(number)와 `paris`(ToLocation) 사이에 엔터티에 속하지 않는 단어가 있음을 알 수 있습니다. [LUIS](luis-reference-regions.md) 웹 사이트의 레이블이 지정된 발언에서 사용된 녹색 밑줄은 복합 엔터티를 나타냅니다.

![복합 엔터티](./media/luis-concept-data-extraction/composite-entity.png)

복합 엔터티는 `compositeEntities` 배열로 반환되고 복합 내의 모든 엔터티도 `entities` 배열로 반환됩니다.

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|데이터 개체|엔터티 이름|값|
|--|--|--|
|미리 빌드된 엔터티 - number|“builtin.number”|“2”|
|계층적 엔터티 - Location|“Location::ToLocation”|“paris”|

## <a name="list-entity-data"></a>목록 엔터티 데이터

[목록](luis-concept-entity-types.md) 엔터티는 기계 학습 엔터티가 아닙니다. 정확히 일치하는 텍스트입니다. 목록은 목록에 있는 항목의 동의어와 함께 해당 항목을 나타냅니다. LUIS는 모든 목록의 항목과 일치하는 항목을 응답의 엔터티로 표시합니다. 동의어는 둘 이상의 목록에 있을 수 있습니다.

앱에 공항(Sea-tac), 공항 코드(SEA), 우편 번호(98101) 및 전화 지역 코드(206)를 사용하는 도시를 포함하여 도시 이름의 변형을 허용하는 `Cities` 목록이 있다고 가정합니다.

|목록 항목|항목 동의어|
|---|---|
|시애틀|sea-tac, sea, 98101, 206, +1 |
|파리|cdg, roissy, ory, 75001, 1, +33|

`book 2 tickets to paris`

이전 발화에서 `paris` 단어는 `Cities` 목록 엔터티의 일부로 paris 항목에 매핑됩니다. 목록 엔터티는 항목 동의어뿐 아니라 항목의 정규화된 이름과도 일치합니다.

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

파리의 동의어를 사용하는 다른 예제 발화:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>미리 빌드된 엔터티 데이터
[미리 빌드된](luis-concept-entity-types.md) 엔터티는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트를 사용하여 정규식 일치를 기반으로 검색됩니다. 미리 빌드된 엔터티는 엔터티 배열로 반환되며 `builtin::` 접두사가 추가된 형식 이름을 사용합니다. 다음 텍스트는 반환된 미리 빌드된 엔터티가 포함된 예제 발화입니다.

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
```

## <a name="regular-expression-entity-data"></a>정규식 엔터티 데이터
[정규식](luis-concept-entity-types.md) 엔터티는 엔터티를 만들 때 제공하는 식을 사용하여 정규식 일치를 기반으로 검색됩니다. `kb[0-9]{6}`를 정규식 엔터티 정의로 사용하는 경우, 다음 JSON 응답은 쿼리 `When was kb123456 published?`에 대해 반환된 정규식 엔터티가 포함된 예제 음성입니다.

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>이름 추출
이름은 거의 문자와 단어의 조합일 수 있기 때문에 발화에서 이름을 가져오는 것은 어렵습니다. 추출할 이름의 유형에 따라 몇 가지 옵션이 있습니다. 다음 제안 사항을 규칙이 아니라 자세한 지침입니다.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>미리 빌드된 PersonName 및 GeographyV2 엔터티 추가

[PersonName](luis-reference-prebuilt-person.md) 및 [GeographyV2](luis-reference-prebuilt-geographyV2.md) 엔터티는 일부 [언어 문화권](luis-reference-prebuilt-entities.md)에서 사용할 수 있습니다. 

### <a name="names-of-people"></a>사용자의 이름
사용자의 이름에는 언어 및 문화권에 따라 일부 약한 형식이 적용될 수 있습니다. 이름 및 성과 함께 계층적 엔터티를 사용하거나 이름 및 성의 역할과 함께 단순 엔터티를 사용합니다. 발화의 다양한 부분, 다양한 길이의 발화 및 None 의도를 포함하는 모든 의도에 걸쳐 있는 발화에서 이름과 성을 사용하는 예제를 제공해야 합니다. 정기적으로 엔드포인트 발화를 [검토](luis-how-to-review-endoint-utt.md)하여 올바르게 예측되지 않은 이름에 레이블을 지정합니다.

### <a name="names-of-places"></a>위치 이름
위치 이름은 구/군/시, 지방, 시/도 및 국가 등으로 설정되고 인식됩니다. 앱이 알려진 위치 집합을 사용할 경우, 목록 엔터티를 사용하는 것이 좋습니다. 모든 위치 이름을 찾아야 하는 경우, 단순 엔터티를 만들고 다양한 예제를 제공합니다. 위치 이름의 구문 목록을 추가하여 앱에서 위치 이름이 표시되는 모양을 보완합니다. 정기적으로 엔드포인트 발화를 [검토](luis-how-to-review-endoint-utt.md)하여 올바르게 예측되지 않은 이름에 레이블을 지정합니다.

### <a name="new-and-emerging-names"></a>새롭게 떠오르는 이름
일부 앱은 제품이나 회사와 같이 새롭게 떠오르는 이름을 찾을 수 있어야 합니다. 이러한 유형의 이름을 가진 데이터를 추출하기가 가장 어렵습니다. 단순 엔터티로 시작하고 구문 목록을 추가합니다. 정기적으로 엔드포인트 발화를 [검토](luis-how-to-review-endoint-utt.md)하여 올바르게 예측되지 않은 이름에 레이블을 지정합니다.

## <a name="pattern-roles-data"></a>패턴 역할 데이터
역할은 엔터티의 컨텍스트 차이입니다.

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Pattern.any 엔터티 데이터
Pattern.any 엔터티는 [패턴](luis-concept-patterns.md)의 템플릿 발화에서 사용되는 가변 길이 엔터티입니다.

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>정서 분석
감정 분석이 구성된 경우 LUIS json 응답에는 감정 분석이 포함됩니다. [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) 설명서에서 감정 분석에 대해 자세히 알아봅니다.

### <a name="sentiment-data"></a>감정 데이터
감정 데이터는 데이터의 긍정적(1에 가까움) 또는 부정적(0에 가까움) 감정을 나타내는 1과 0 사이의 점수입니다.

문화권이 `en-us`인 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

다른 모든 문화권의 경우 응답은 다음과 같습니다.

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>키 구문 추출 엔터티 데이터
키 구문 추출 엔터티는 [Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)에서 제공하는 발화의 키 구문을 반환합니다.

```JSON
{
  "query": "Is there a map of places with beautiful views on a favorite trail?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.764368951
  },
  "intents": [
    ...
  ],
  "entities": [
    {
      "entity": "beautiful views",
      "type": "builtin.keyPhrase",
      "startIndex": 30,
      "endIndex": 44
    },
    {
      "entity": "map of places",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 23
    },
    {
      "entity": "favorite trail",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 64
    }
  ]
}
```

## <a name="data-matching-multiple-entities"></a>여러 엔터티와 일치하는 데이터

LUIS는 발화에서 검색된 모든 엔터티를 반환합니다. 결과적으로 챗봇은 결과를 기반으로 결정해야 할 수 있습니다. 발화에는 많은 엔터티가 포함될 수 있습니다.

`book me 2 adult business tickets to paris tomorrow on air france`

LUIS 엔드포인트는 여러 엔터티에서 동일한 데이터를 검색할 수 있습니다.

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>여러 목록 엔터티와 일치하는 데이터

단어나 구가 둘 이상의 목록 엔터티와 일치하는 경우, 엔드포인트 쿼리는 각 목록 엔터티를 반환합니다.

`when is the best time to go to red rock?` 쿼리에서 앱이 둘 이상의 목록에 `red`라는 단어를 갖는 경우 LUIS는 모든 엔터티를 인식하고 엔터티의 배열을 JSON 엔드포인트 응답의 일부로 반환합니다. 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
