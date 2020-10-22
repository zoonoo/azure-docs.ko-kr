---
title: Text Analytics REST API를 사용하여 감정 분석 수행
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics REST API를 사용하여 텍스트에서 감정를 검색하는 방법을 보여 줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 10/16/2020
ms.author: aahi
ms.openlocfilehash: 2b13ac02a8a276b2ef46c8ffd5f13846e394d938
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166933"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>방법: Text Analytics API를 사용하여 감정 감지

Text Analytics API의 감정 분석 기능은 텍스트를 평가하여 각 문장의 감정 점수와 레이블을 반환합니다. 이 기능은 소셜 미디어, 고객 리뷰, 토론 포럼 등에서 긍정적이거나 부정적인 감정을 감지하는 데 유용합니다. API에서 사용하는 AI 모델은 서비스를 통해 제공되며, 분석 용도로만 콘텐츠를 전송해야 합니다.

감정 분석 요청을 보내면 API가 감정 레이블(예: "부정적", "중립적" 및 "긍정적")과 문장 및 문서 수준의 신뢰도 점수를 반환합니다.

감정 분석은 다양한 언어를 지원하며 미리 보기로 제공됩니다. 자세한 내용은 [지원되는 언어](../text-analytics-supported-languages.md)를 참조하세요.

## <a name="sentiment-analysis-versions-and-features"></a>감정 분석 버전 및 기능

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 기능                                   | 감정 분석 v3 | 감정 분석 v3.1(미리 보기) |
|-------------------------------------------|-----------------------|-----------------------------------|
| 단일 및 일괄 처리 요청을 위한 메서드    | X                     | X                                 |
| 감정 점수 및 레이블 지정             | X                     | X                                 |
| Linux 기반 [Docker 컨테이너](text-analytics-how-to-install-containers.md) | X  |  |
| 의견 마이닝                            |                       | X                                 |

## <a name="sentiment-scoring-and-labeling"></a>감정 채점 및 레이블 지정

감정 분석 v3는 텍스트에 감정 레이블을 적용합니다. 이는 문장과 문서 각각에 대한 신뢰도 점수와 함께 문장과 문서 수준에서 반환됩니다. 

레이블은 *긍정*, *부정* 및 *중립*입니다. 문서 수준에서 *혼합된* 감정 레이블을 반환할 수도 있습니다. 문서의 감정은 다음과 같이 결정됩니다.

| 문장 감정                                                                            | 반환된 문서 레이블 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 문서에 `positive` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `positive`              |
| 문서에 `negative` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `negative`              |
| 문서에 `negative` 문장과 `positive` 문장이 각각 하나 이상 있습니다.    | `mixed`                 |
| 문서의 모든 문장이 `neutral`입니다.                                                  | `neutral`               |

신뢰도 점수의 범위는 1에서 0까지입니다. 점수가 1에 가까울수록 레이블 분류에 대한 높은 신뢰도를 의미하며, 점수가 낮을수록 낮은 신뢰도를 의미합니다. 각 문서 또는 각 문장에 대해 레이블(긍정, 부정 및 중립)과 관련된 예측 점수는 최대 1까지 추가됩니다.

## <a name="opinion-mining"></a>의견 마이닝

의견 마이닝은 버전 3.1-preview.1부터 감정 분석에 도입된 기능입니다. NLP(자연어 처리)의 양상 기반 감정 분석이라고도 하는 이 기능은 양상과 관련된 의견에 대한 더 세부적인 정보(예: 제품 또는 서비스의 특성)를 텍스트 형식으로 제공합니다.

예를 들어 고객이 호텔에 대한 피드백을 "객실은 훌륭했어요. 그런데 직원이 불친절했어요"라고 남기면 의견 마이닝이 텍스트의 양상과 관련 의견 및 감정을 찾습니다.

| 양상 | 의견    | 감정 |
|--------|------------|-----------|
| 객실   | 훌륭      | 긍정적  |
| 직원  | 불친절 | 부정적  |

결과에서 의견 마이닝을 가져오려면 감정 분석 요청에 `opinionMining=true` 플래그를 포함해야 합니다. 의견 마이닝 결과는 감정 분석 응답에 포함됩니다.

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기 

### <a name="preparation"></a>준비

감정 분석에서는 사용할 텍스트를 더 작은 크기로 제공하면 더 높은 품질의 결과를 얻을 수 있습니다. 이는 구 블록이 클수록 더 잘 수행되는 핵심 구 추출과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

다음 형식의 JSON 문서가 있어야 합니다. ID, 텍스트 및 언어.

문서 크기는 문서당 5,120자 미만이어야 합니다. 컬렉션당 최대 1,000개 항목(ID)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

## <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. [Postman](text-analytics-how-to-call-api.md) 또는 다음 참조 링크의 **API 테스트 콘솔**을 사용하여 신속하게 요청을 만들어서 보낼 수 있습니다. 

#### <a name="version-31-preview1"></a>[버전 3.1-preview.1](#tab/version-3-1)

[감정 분석 v3.1 참조](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Sentiment)

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

[감정 분석 v3 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment)

---

### <a name="request-endpoints"></a>요청 엔드포인트

Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용하여 감정 분석을 위한 HTTPS 엔드포인트를 설정합니다. 사용하려는 버전의 올바른 URL을 포함해야 합니다. 예를 들면 다음과 같습니다.

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 

#### <a name="version-31-preview1"></a>[버전 3.1-preview.1](#tab/version-3-1)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment`

의견 마이닝 결과를 얻으려면 `opinionMining=true` 매개 변수를 포함해야 합니다. 예를 들면 다음과 같습니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/sentiment?opinionMining=true`

기본적으로 이 매개 변수는 `false`로 설정됩니다. 

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment`

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

### <a name="example-sentiment-analysis-request"></a>감정 분석 요청 예제 

감정 분석을 위해 제출할 수 있는 콘텐츠의 예제는 다음과 같습니다. 두 버전의 요청 형식은 동일합니다.
    
```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "The restaurant had great food and our waiter was friendly."
    }
  ]
}
```

### <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.


### <a name="view-the-results"></a>결과 보기

감정 분석은 전체 문서와 그 안에 포함된 각 문장의 감정 레이블과 신뢰도 점수를 반환합니다. 점수가 1에 가까울수록 레이블 분류에 대한 높은 신뢰도를 의미하며, 점수가 낮을수록 낮은 신뢰도를 의미합니다. 문서에는 여러 문장이 있을 수 있으며, 각 문서 또는 문장 내 신뢰도 점수의 합계는 1입니다.

출력은 즉시 반환됩니다. JSON을 수락하거나 로컬 시스템의 파일에 출력을 저장하는 애플리케이션에 결과를 스트리밍할 수 있습니다. 그런 다음, 데이터를 정렬, 검색 및 조작하는 데 사용할 수 있는 애플리케이션으로 출력을 가져옵니다. 다국어 지원 및 emoji 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 내용은 [오프셋 처리 방법](../concepts/text-offsets.md)을 참조하세요.

#### <a name="version-31-preview1"></a>[버전 3.1-preview.1](#tab/version-3-1)

### <a name="sentiment-analysis-v31-example-response"></a>감정 분석 v3.1 응답 예제

감정 분석 v3.1은 **버전 3.0** 탭의 응답 개체 외에 의견 마이닝도 제공합니다. 아래 응답에서 *식당의 음식은 훌륭했고 웨이터도 친절했어요*라는 문장에는 *음식* 및 *웨이터*라는 두 가지 양상이 있습니다. 각 양상의 `relations` 속성에는 연결된 `documents`, `sentences` 및 `opinions` 개체에 대한 URI 참조를 포함하는 `ref` 값이 포함되어 있습니다.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly.",
                    "aspects": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 25,
                            "length": 4,
                            "text": "food",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/0"
                                }
                            ]
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 38,
                            "length": 6,
                            "text": "waiter",
                            "relations": [
                                {
                                    "relationType": "opinion",
                                    "ref": "#/documents/0/sentences/0/opinions/1"
                                }
                            ]
                        }
                    ],
                    "opinions": [
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 19,
                            "length": 5,
                            "text": "great",
                            "isNegated": false
                        },
                        {
                            "sentiment": "positive",
                            "confidenceScores": {
                                "positive": 1.0,
                                "negative": 0.0
                            },
                            "offset": 49,
                            "length": 8,
                            "text": "friendly",
                            "isNegated": false
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

### <a name="sentiment-analysis-v30-example-response"></a>감정 분석 v3.0 응답 예제

감정 분석 v3의 응답에는 분석한 각 문장과 문서의 감정 레이블 및 점수가 포함됩니다.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "confidenceScores": {
                "positive": 1.0,
                "neutral": 0.0,
                "negative": 0.0
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "confidenceScores": {
                        "positive": 1.0,
                        "neutral": 0.0,
                        "negative": 0.0
                    },
                    "offset": 0,
                    "length": 58,
                    "text": "The restaurant had great food and our waiter was friendly."
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2020-04-01"
}
```

---

## <a name="summary"></a>요약

이 문서에서는 Text Analytics API를 사용하는 감정 분석의 개념과 워크플로에 대해 알아보았습니다. 요약하면 다음과 같습니다.

+ 감정 분석은 선택한 언어로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
+ POST 요청은 개인 설정된 [액세스 키와 구독에 유효한 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 `/sentiment` 엔드포인트에 대해 수행됩니다.
+ 각 문서 ID에 대한 감정 점수로 구성된 응답 출력은 JSON을 허용하는 모든 앱으로 스트리밍할 수 있습니다. Excel과 Power BI를 예로 들 수 있습니다.

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
