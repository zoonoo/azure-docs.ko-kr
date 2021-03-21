---
title: 언어 감지 인식 기술
titleSuffix: Azure Cognitive Search
description: 비구조적 텍스트를 평가 하 고 각 레코드에 대해 Azure Cognitive Search AI 보강 파이프라인의 분석 강도를 나타내는 점수가 포함 된 언어 식별자를 반환 합니다.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 078a9312a7ee1b3b0eafd000928ed74348a540c3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548056"
---
#   <a name="language-detection-cognitive-skill"></a>언어 감지 인식 기술

**언어 감지** 기술은 입력 텍스트의 언어를 검색 하 고 요청에 제출 된 모든 문서에 대해 단일 언어 코드를 보고 합니다. 언어 코드는 분석의 강도를 나타내는 점수와 쌍을 이룹니다. 이 기술은 Cognitive Services의 [Text Analytics](../cognitive-services/text-analytics/overview.md)에서 제공하는 기계 학습 모델을 사용합니다.

이 기능은 텍스트 언어를 다른 기술에 대한 입력으로 제공해야 할 경우 특히 유용합니다(예를 들어 [감정 분석 기술](cognitive-search-skill-sentiment.md) 또는 [텍스트 분할 기술](cognitive-search-skill-textsplit.md)).

언어 검색은 Text Analytics에 대해 나열 된 [지원 되는 언어 및 지역](../cognitive-services/text-analytics/language-support.md) 수를 초과 하는 Bing의 자연어 처리 라이브러리를 활용 합니다. 언어에 대 한 정확한 목록은 게시 되지 않지만 널리 쓰이는 모든 언어, 변형, 언어 및 일부 지역 및 문화 언어를 포함 합니다. 자주 사용 하지 않는 언어로 표현 된 콘텐츠가 있는 경우 [언어 감지 API를 사용해](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 서 코드를 반환 하는지 확인할 수 있습니다. 감지할 수 없는 언어에 대한 응답은 `(Unknown)`입니다.

> [!NOTE]
> 처리 빈도를 늘리거나 문서를 추가하거나 AI 알고리즘을 추가하여 범위를 확장할 때 [청구 가능한 Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)해야 합니다. Cognitive Services에서 API를 호출하는 경우와 Azure Cognitiv Search에서 문서 크래킹 단계의 일부로 이미지를 추출하는 경우에는 요금이 부과됩니다. 문서에서 텍스트 추출할 때는 요금이 발생하지 않습니다.
>
> 기본 제공 기술을 실행하는 요금은 기존 [Cognitive Services 종량제 가격](https://azure.microsoft.com/pricing/details/cognitive-services/)으로 청구됩니다. 이미지 추출 가격 책정은 [Azure Cognitiv Search 가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에 설명되어 있습니다.


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>데이터 제한
레코드의 최대 크기는 [`String.Length`](/dotnet/api/system.string.length)에 의해 측정된 대로 50,000자여야 합니다. 언어 검색 기술에 전송 하기 전에 데이터를 분할 해야 하는 경우 [텍스트 분할 기술을](cognitive-search-skill-textsplit.md)사용할 수 있습니다.

## <a name="skill-parameters"></a>기술 매개 변수

매개 변수는 대/소문자를 구분합니다.

| 입력 | Description |
|---------------------|-------------|
| `defaultCountryHint` | 필드 언어를 구분할 수 없는 경우 언어 검색 모델에 대 한 힌트로 사용할 ISO 3166-1 알파-2 2 letter 국가 코드를 제공할 수 있습니다. 자세한 내용은이 항목에 대 한 [Text Analytics 설명서를](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) 참조 하세요. 특히 `defaultCountryHint` 매개 변수는 입력을 명시적으로 지정 하지 않는 문서와 함께 사용 됩니다 `countryHint` .  |
| `modelVersion`   | 필드 Text Analytics 서비스를 호출할 때 사용할 모델의 버전입니다. 지정 되지 않은 경우 기본적으로 사용 가능한 최신 버전으로 지정 됩니다. 반드시 필요한 경우가 아니면이 값을 지정 하지 않는 것이 좋습니다. 자세한 내용은 [텍스트 분석 API의 모델 버전 관리](../cognitive-services/text-analytics/concepts/model-versioning.md) 를 참조 하세요. |

## <a name="skill-inputs"></a>기술 입력

매개 변수는 대/소문자를 구분합니다.

| 입력     | Description |
|--------------------|-------------|
| `text` | 분석할 텍스트입니다.|
| `countryHint` | 언어를 구분할 수 없는 경우 언어 검색 모델에 대 한 힌트로 사용할 ISO 3166-1 알파-2 2 letter 국가 코드입니다. 자세한 내용은이 항목에 대 한 [Text Analytics 설명서를](../cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection.md#ambiguous-content) 참조 하세요. |

## <a name="skill-outputs"></a>기술 출력

| 출력 이름    | 설명 |
|--------------------|-------------|
| `languageCode` | 식별된 언어에 대한 ISO 6391 언어 코드입니다. 예: "en". |
| `languageName` | 언어의 이름입니다. 예: "영어". |
| `score` | 0과 1 사이의 값입니다. 언어를 올바르게 식별하는 가능성입니다. 문장에 언어를 혼합하는 경우 점수는 1보다 작을 수 있습니다.  |

##  <a name="sample-definition"></a>샘플 정의

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      },
      {
        "name": "countryHint",
        "source": "/document/countryHint"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>샘플 입력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "impossible",
             "countryHint": "fr"
           }
      }
    ]
```


##  <a name="sample-output"></a>샘플 출력

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      },
      {
        "recordId": "3",
        "data":
            {
              "languageCode": "fr",
              "languageName": "French",
              "score": 1,
            }
      }
    ]
}
```

## <a name="see-also"></a>참고 항목

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
+ [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)