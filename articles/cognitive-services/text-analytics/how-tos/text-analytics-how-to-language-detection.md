---
title: Text Analytics REST API를 사용하여 언어 검색
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services에서 Text Analytics REST API를 사용하여 언어를 감지합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 58f2dc39c185e158a2b4b1b5e73b6b7d589c8c03
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745581"
---
# <a name="example-detect-language-with-text-analytics"></a>예제: Text Analytics를 사용하여 언어 감지

Text Analytics REST API의 [언어 감지](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) 기능은 각 문서에 대해 텍스트 입력을 평가하고, 분석 강도를 나타내는 점수가 있는 언어 식별자를 반환합니다.

이 기능은 알 수 없는 언어로 된 임의의 텍스트를 수집하는 콘텐츠 저장소에 유용합니다. 이 분석의 결과를 구문 분석하여 입력 문서에서 사용된 언어를 판별할 수 있습니다. 또한 응답에서는 모델의 신뢰도가 반영된 점수를 반환합니다. 점수 값은 0 ~ 1 사이입니다.

언어 감지 기능은 다양한 언어, 변형, 방언 및 일부 지역 또는 문화권 언어를 감지할 수 있습니다. 이 기능에 대한 정확한 언어 목록은 게시되지 않습니다.

사용 빈도가 낮은 언어로 표현된 콘텐츠가 있는 경우, 언어 감지 기능을 사용하여 코드가 반환되는지 확인할 수 있습니다. 감지할 수 없는 언어에 대한 응답은 `unknown`입니다.

> [!TIP]
> Text Analytics는 언어 감지를 위한 Linux 기반 Docker 컨테이너 이미지도 제공하므로 데이터와 가까이 [Text Analytics 컨테이너를 설치하고 실행](text-analytics-how-to-install-containers.md)할 수 있습니다.

## <a name="preparation"></a>준비

다음 형식의 JSON 문서가 있어야 합니다. ID 및 텍스트.

문서 크기는 문서당 5,120자 미만이어야 합니다. 컬렉션당 최대 1,000개 항목(ID)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다. 다음 샘플은 언어 감지를 위해 제출할 수 있는 콘텐츠의 예제입니다.

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>1단계: 요청 구성

요청 정의에 대한 자세한 내용은 [Text Analytics API 호출](text-analytics-how-to-call-api.md)을 참조하세요. 편의상 다음 사항을 다시 설명합니다.

+ POST 요청을 만듭니다. 이 요청에 대한 API 문서를 검토하려면 [언어 감지 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)를 참조하세요.

+ 언어 감지에 대한 HTTP 엔드포인트를 설정합니다. Azure의 Text Analytics 리소스 또는 인스턴스화된 [Text Analytics 컨테이너](text-analytics-how-to-install-containers.md)를 사용합니다. URL에 `/text/analytics/v2.1/languages`를 포함해야 합니다. 예: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`

+ Text Analytics 작업에 대한 [액세스 키](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)가 포함되도록 요청 헤더를 설정합니다.

+ 이 분석을 위해 준비한 JSON 문서 컬렉션을 요청 본문에 제공합니다.

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md)을 사용하거나 [설명서](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)에서 **API 테스트 콘솔**을 열어 요청을 구성하고, 서비스에 POST합니다.

## <a name="step-2-post-the-request"></a>2단계: 요청 POST하기

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

상태 비저장 서비스임에 유의하세요. 계정에는 아무 데이터도 저장되지 않습니다. 결과는 응답으로 즉시 반환됩니다.


## <a name="step-3-view-the-results"></a>3단계: 결과 보기

모든 POST 요청에서는 ID 및 감지된 속성이 있는 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 수락하거나 로컬 시스템의 파일에 출력을 저장하는 애플리케이션에 결과를 스트리밍할 수 있습니다. 그런 다음, 데이터를 정렬, 검색 및 조작하는 데 사용할 수 있는 애플리케이션으로 출력을 가져옵니다.

예제 요청의 결과는 다음 JSON과 같습니다. 하나의 문서에 여러 항목이 포함되어 있습니다. 출력은 영어로 되어 있습니다. 언어 식별자에는 친숙한 이름과 [ISO 639-1](https://www.iso.org/standard/22109.html) 형식의 언어 코드가 포함됩니다.

긍정적인 1.0 점수는 분석에 가능한 가장 높은 신뢰도 수준을 나타냅니다.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>모호한 콘텐츠

일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가/지역 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다.

예를 들어 "Impossible"은 영어와 프랑스어 모두에 공통적이며, 제한된 컨텍스트로 제공되는 경우 응답은 "US" 국가/지역 힌트를 기반으로 합니다. 텍스트의 원본이 힌트로 제공될 수 있는 프랑스에서 온 것으로 알려진 경우입니다.

**Input**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

이제 서비스에는 더 나은 결정을 내릴 수 있는 추가 컨텍스트가 있습니다. 

**출력**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

분석기가 입력을 구문 분석할 수 없으면 `(Unknown)`을 반환합니다. 아랍어 숫자로만 구성된 텍스트 블록을 제출하는 경우를 예로 들 수 있습니다.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>혼합 언어 콘텐츠

동일한 문서 내에 혼합 언어 콘텐츠가 있으면 콘텐츠에서 가장 많은 표현이 있지만 더 낮은 긍정적 등급을 갖는 언어를 반환합니다. 등급은 해당 평가의 한계 강도를 반영합니다. 다음 예제의 입력에는 영어, 스페인어 및 프랑스어가 혼합되어 있습니다. 분석기에서 각 세그먼트의 문자를 계산하여 주요 언어를 결정합니다.

**Input**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**출력**

결과 출력은 1.0 미만의 점수를 갖는 주요 언어로 구성되어 낮은 신뢰도 수준을 나타냅니다.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>요약

이 문서에서는 Azure Cognitive Services의 Text Analytics를 사용하여 언어 감지에 대한 개념과 워크플로를 알아보았습니다. 다음 사항에 대해 설명하고 시연했습니다.

+ [언어 감지](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)는 다양한 언어, 변형, 방언 및 일부 지역 또는 문화권 언어에 사용할 수 있습니다.
+ 요청 본문의 JSON 문서에는 ID와 텍스트가 포함됩니다.
+ POST 요청은 개인 설정된 [액세스 키와 구독에 유효한 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 `/languages` 엔드포인트에 대해 수행됩니다.
+ 응답 출력은 각 문서 ID의 언어 식별자로 구성됩니다. 출력은 JSON을 허용하는 모든 앱으로 스트리밍할 수 있습니다. 몇 가지만 예를 들면, 예제 앱에는 Excel 및 Power BI가 포함됩니다.

## <a name="see-also"></a>참고 항목

 [Text Analytics 개요](../overview.md) [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [감정 분석](text-analytics-how-to-sentiment-analysis.md)
