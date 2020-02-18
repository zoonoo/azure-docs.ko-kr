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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 84ef01b5e7fc3f628b1cdf7a1f13175604ebcdd4
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137953"
---
# <a name="how-to-detect-sentiment-using-the-text-analytics-api"></a>방법: Text Analytics API를 사용하여 감정 감지

Text Analytics API의 감정 분석 기능은 텍스트를 평가하여 각 문장의 감정 점수와 레이블을 반환합니다. 이 기능은 소셜 미디어, 고객 리뷰, 토론 포럼 등에서 긍정적이거나 부정적인 감정을 감지하는 데 유용합니다. API에서 사용하는 AI 모델은 서비스를 통해 제공되며, 분석 용도로만 콘텐츠를 전송해야 합니다.

> [!TIP]
> Text Analytics는 언어 감지를 위한 Linux 기반 Docker 컨테이너 이미지도 제공하므로 데이터와 가까이 [Text Analytics 컨테이너를 설치하고 실행](text-analytics-how-to-install-containers.md)할 수 있습니다.

감정 분석은 다양한 언어를 지원하며 미리 보기로 제공됩니다. 자세한 내용은 [지원되는 언어](../text-analytics-supported-languages.md)를 참조하세요.

## <a name="concepts"></a>개념

Text Analytics API는 기계 학습 분류 알고리즘을 사용하여 0과 1 사이의 감정 점수를 생성합니다. 점수가 1에 가까울수록 긍정적인 감정을 나타내는 반면, 0에 가까울수록 부정적인 감정을 나타냅니다. 감정 분석은 텍스트의 개별 엔터티가 아닌 전체 문서에 대해 수행됩니다. 즉, 감정 점수는 문서 또는 문장 수준에서 반환됩니다. 

사용되는 모델은 방대한 텍스트 및 감정 연결을 사용하여 미리 학습되어 있습니다. 이 모델은 텍스트 처리, 음성 부분 분석, 단어 배치, 단어 연결 등을 비롯한 분석 기술을 조합하여 사용합니다. 알고리즘에 대한 자세한 내용은 [Text Analytics 소개](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)를 참조하세요. 현재는 사용자 고유의 학습 데이터를 제공할 수 없습니다. 

문서에 큰 텍스트 블록 대신 몇 개 안 되는 문장이 들어 있을 때 채점 정확도가 향상되는 경향이 있습니다. 객관성 평가 단계에서는 모델에서 문서 전체가 객관적인지, 아니면 감정이 포함되어 있는지를 결정합니다. 대부분 객관적인 문서는 감정 감지 구로 진행되지 않으므로 추가 처리 없이 0.50점이 됩니다. 파이프라인에서 계속되는 문서인 경우 다음 단계에서는 0.50보다 높거나 낮은 점수를 생성합니다. 점수는 문서에서 검색된 감정의 정도에 따라 달라집니다.

## <a name="sentiment-analysis-versions-and-features"></a>감정 분석 버전 및 기능

Text Analytics API는 v2 및 v3의 두 가지 감정 분석 버전을 제공합니다. 감정 분석 v3(공개 미리 보기)는 API의 텍스트 분류 및 채점 정확성과 세부 정보를 대폭 향상합니다.

> [!NOTE]
> * 감정 분석 v3 요청 형식 및 [데이터 제한](../overview.md#data-limits)은 이전 버전과 동일합니다.
> * 감정 분석 v3는 `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe` 및 `West US 2` 영역에서 사용할 수 있습니다.

| 기능                                   | 감정 분석 v2 | 감정 분석 v3 |
|-------------------------------------------|-----------------------|-----------------------|
| 단일 및 일괄 처리 요청을 위한 메서드    | X                     | X                     |
| 전체 문서의 감정 점수  | X                     | X                     |
| 개별 문장의 감정 점수 |                       | X                     |
| 감정 레이블 지정                        |                       | X                     |
| 모델 버전 관리                   |                       | X                     |

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

### <a name="sentiment-scoring"></a>감정 채점

감정 분석 v3는 감정 레이블을 사용하여 텍스트를 분류합니다(아래 설명 참조). 반환된 점수는 텍스트가 긍정적인지, 부정적인지 아니면 중립적인지를 나타내는 모델의 신뢰도를 나타냅니다. 값이 높을수록 신뢰도가 높아집니다. 

### <a name="sentiment-labeling"></a>감정 레이블 지정

감정 분석 v3은 문장 및 문서 수준에서 점수와 레이블을 반환할 수 있습니다. 점수와 레이블은 `positive`, `negative` 및 `neutral`입니다. 문서 수준에서 점수 없이 `mixed` 감정 레이블을 반환할 수도 있습니다. 문서의 감정은 다음과 같이 결정됩니다.

| 문장 감정                                                                            | 반환된 문서 레이블 |
|-----------------------------------------------------------------------------------------------|-------------------------|
| 문서에 `positive` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `positive`              |
| 문서에 `negative` 문장이 하나 이상 있습니다. 나머지 문장은 `neutral`입니다. | `negative`              |
| 문서에 `negative` 문장과 `positive` 문장이 각각 하나 이상 있습니다.    | `mixed`                 |
| 문서의 모든 문장이 `neutral`입니다.                                                  | `neutral`               |

### <a name="model-versioning"></a>모델 버전 관리

> [!NOTE]
> 감정 분석을 위한 모델 버전 관리는 버전 `v3.0-preview.1`부터 사용할 수 있습니다.

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

### <a name="example-c-code"></a>C# 코드 예제

이 버전의 감정 분석을 호출하는 C# 애플리케이션 예제는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs)에서 찾을 수 있습니다.


#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

### <a name="sentiment-scoring"></a>감정 채점

감정 분석기에서는 텍스트를 주로 긍정 또는 부정으로 분류하며, 0에서 1 사이의 점수를 할당합니다. 0\.5에 가까운 값은 중립적이거나 불확정인 상태이며, 0\.5점은 중립성을 나타냅니다. 문자열에서 감정을 분석할 수 없거나 감정이 없는 경우 점수는 항상 정확히 0.5입니다. 예를 들어 영어 언어 코드가 있는 스페인어 문자열을 전달하면 점수는 0.5점이 됩니다.

---

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기 

### <a name="preparation"></a>준비

감정 분석에서는 사용할 텍스트를 더 작은 크기로 제공하면 더 높은 품질의 결과를 얻을 수 있습니다. 이는 구 블록이 클수록 더 잘 수행되는 핵심 구 추출과는 반대입니다. 두 작업에서 최상의 결과를 얻으려면 이에 따라 입력을 다시 구성하는 것이 좋습니다.

다음 형식의 JSON 문서가 있어야 합니다. ID, 텍스트 및 언어.

문서 크기는 문서당 5,120자 미만이어야 합니다. 컬렉션당 최대 1,000개 항목(ID)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

## <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. [Postman](text-analytics-how-to-call-api.md) 또는 다음 참조 링크의 **API 테스트 콘솔**을 사용하여 신속하게 요청을 만들어서 보낼 수 있습니다. 

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

[감정 분석 v3 참조](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment)

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

[감정 분석 v2 참조](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

---

Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용하여 감정 분석을 위한 HTTPS 엔드포인트를 설정합니다. 사용하려는 버전의 올바른 URL을 포함해야 합니다. 다음은 그 예입니다.

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/sentiment`

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/sentiment`

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

### <a name="example-sentiment-analysis-request"></a>감정 분석 요청 예제 

감정 분석을 위해 제출할 수 있는 콘텐츠의 예제는 다음과 같습니다. 두 API 버전의 요청 형식은 동일합니다.
    
```json
{
    "documents": [
    {
        "language": "en",
        "id": "1",
        "text": "Hello world. This is some input text that I love."
    },
    {
        "language": "en",
        "id": "2",
        "text": "It's incredibly sunny outside! I'm so happy."
    }
    ],
}
```

### <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.


### <a name="view-the-results"></a>결과 보기

감정 분석기에서는 텍스트를 주로 긍정 또는 부정으로 분류하며, 0에서 1 사이의 점수를 할당합니다. 0\.5에 가까운 값은 중립적이거나 불확정인 상태이며, 0\.5점은 중립성을 나타냅니다. 문자열에서 감정을 분석할 수 없거나 감정이 없는 경우 점수는 항상 정확히 0.5입니다. 예를 들어 영어 언어 코드가 있는 스페인어 문자열을 전달하면 점수는 0.5점이 됩니다.

출력은 즉시 반환됩니다. JSON을 수락하거나 로컬 시스템의 파일에 출력을 저장하는 애플리케이션에 결과를 스트리밍할 수 있습니다. 그런 다음, 데이터를 정렬, 검색 및 조작하는 데 사용할 수 있는 애플리케이션으로 출력을 가져옵니다.

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

### <a name="sentiment-analysis-v3-example-response"></a>감정 분석 v3 응답 예제

감정 분석 v3의 응답에는 분석한 각 문장과 문서의 감정 레이블 및 점수가 포함됩니다. 문서 감정 레이블이 `mixed`이면 `documentScores`가 반환되지 않습니다.

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

### <a name="sentiment-analysis-v2-example-response"></a>감정 분석 v2 응답 예제

감정 분석 v2의 응답에는 보낸 각 문서의 감정 점수가 포함됩니다.

```json
{
  "documents": [{
    "id": "1",
    "score": 0.98690706491470337
  }, {
    "id": "2",
    "score": 0.95202046632766724
  }],
  "errors": []
}
```

---

## <a name="summary"></a>요약

이 문서에서는 Text Analytics API를 사용하는 감정 분석의 개념과 워크플로에 대해 알아보았습니다. 요약하면 다음과 같습니다.

+ 감정 분석은 선택한 언어의 두 가지 버전으로 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
+ POST 요청은 개인 설정된 [액세스 키와 구독에 유효한 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 `/sentiment` 엔드포인트에 대해 수행됩니다.
+ 각 문서 ID에 대한 감정 점수로 구성된 응답 출력은 JSON을 허용하는 모든 앱으로 스트리밍할 수 있습니다. Excel과 Power BI를 예로 들 수 있습니다.

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
