---
title: 텍스트 분석 API에서 엔터티 인식 사용
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API를 사용하여 텍스트에 있는 엔터티의 ID를 식별하고 명확하게 구분하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/10/2021
ms.author: aahi
ms.openlocfilehash: 3fdd5db4abc2b16153f6758827e152337887a095
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962805"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics에서 명명된 엔터티 인식을 사용하는 방법

Text Analytics API는 구조화되지 않은 텍스트를 사용하고 웹의 추가 정보 링크가 있는 명확히 구분된 엔터티 목록을 반환합니다. API는 여러 엔터티 범주에 대해 NER(명명된 엔터티 인식) 및 엔터티 링크 설정을 모두 지원합니다.

## <a name="entity-linking"></a>엔터티 연결

엔터티 링크 설정은 텍스트에서 찾은 엔터티의 ID를 식별하고 명확하게 구분하는 기능입니다(예: 단어 "Mars"가 행성 또는 로마의 전쟁의 신을 나타내는지 확인). 이 프로세스에서는 인식된 엔터티를 텍스트에 연결하기 위해 기술 자료가 적절한 언어로 존재해야 합니다. 엔터티 링크 설정은 이 기술 자료로 [Wikipedia](https://www.wikipedia.org/)를 사용합니다.

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

NER(명명된 엔터티 인식)은 텍스트에서 다양한 엔터티를 식별하고 이를 사용자, 위치, 이벤트, 제품, 조직 등의 미리 정의된 클래스 또는 형식으로 분류하는 기능입니다.  

## <a name="personally-identifiable-information-pii"></a>PII(개인 식별 정보)

PII 기능은 NER의 일부이며 전화 번호, 이메일 주소, 우편 주소, 여권 번호 등의 개별 사용자와 연결된 텍스트에서 중요한 엔터티를 식별하고 교정할 수 있습니다.

## <a name="named-entity-recognition-features-and-versions"></a>명명된 엔터티 인식 기능 및 버전

| 기능                                                         | NER v3.0 | NER v3.1-preview.5 |
|-----------------------------------------------------------------|--------|----------|
| 단일 및 일괄 처리 요청을 위한 메서드                          | X      | X        |
| 여러 범주에서 확장된 엔터티 인식           | X      | X        |
| 엔터티 링크 설정 및 NER 요청을 보내기 위한 별도의 엔드포인트입니다. | X      | X        |
| 개인(`PII`) 및 상태(`PHI`) 정보 엔터티 인식        |        | X        |
| `PII`의 교정        |        | X        |

자세한 내용은 [언어 지원](../language-support.md)을 참조하세요.

명명된 엔터티 인식 v3은 여러 형식에서 확장된 검색을 제공합니다. 현재 NER v3.0은 [일반 엔터티 범주](../named-entity-types.md)의 엔터티를 인식할 수 있습니다.

명명된 엔터티 인식 v3.1-preview.5에는 v3.0의 검색 기능이 포함되어 있습니다. 
* `v3.1-preview.5/entities/recognition/pii` 엔드포인트를 사용하여 개인 정보(`PII`)를 검색할 수 있는 기능입니다. 
* 기밀 상태 정보(`PHI`)를 검색하는 선택적 `domain=phi` 매개 변수입니다.
* `/analyze` 엔드포인트를 사용하는 [비동기 작업](text-analytics-how-to-call-api.md)입니다.

자세한 내용은 아래의 [엔터티 범주](../named-entity-types.md) 문서 및 [요청 엔드포인트](#request-endpoints) 섹션을 참조하세요. 신뢰도 점수에 대한 자세한 내용은 [Text Analytics 투명도 메모](/legal/cognitive-services/text-analytics/transparency-note?context=/azure/cognitive-services/text-analytics/context/context)를 참조하세요. 

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기

### <a name="preparation"></a>준비

ID, 텍스, 언어 형식의 JSON 문서가 있어야 합니다.

각 문서는 5,120자 미만이어야 하며, 컬렉션당 최대 1,000개의 항목(ID)을 포함할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. [Postman](text-analytics-how-to-call-api.md) 또는 다음 링크의 **API 테스트 콘솔** 을 사용하여 신속하게 요청을 만들어서 보낼 수 있습니다. 

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 


### <a name="request-endpoints"></a>요청 엔드포인트

#### <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-preview)

명명된 엔터티 인식 `v3.1-preview.5`는 NER, PII 및 엔터티 링크 설정 요청에 대해 별도의 엔드포인트를 사용합니다. 요청에 따라 아래 URL 형식을 사용합니다.

**엔터티 연결**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/linking`

[`Linking`에 대한 명명된 엔터티 인식 버전 3.1-preview 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-5/operations/EntitiesLinking)

**명명된 엔터티 인식**
* 일반 엔터티 - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/general`

[`General`에 대한 명명된 엔터티 인식 버전 3.1-preview 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-5/operations/EntitiesRecognitionGeneral)

**PII(개인 식별 정보)**
* 개인(`PII`) 정보 - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii`

선택적 `domain=phi` 매개 변수를 사용하여 텍스트에서 상태(`PHI`) 정보를 검색할 수도 있습니다. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?domain=phi`

`v3.1-preview.5`부터 JSON 응답은 검색된 PII 엔터티가 엔터티의 각 문자에 대해 `*`로 대체되는 수정된 입력 텍스트를 포함하는 `redactedText` 속성을 포함합니다.

[`PII`에 대한 명명된 엔터티 인식 버전 3.1-preview 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-5/operations/EntitiesRecognitionPii)

API는 지정된 문서 언어에 대해 [나열된 엔터티 범주](../named-entity-types.md?tabs=personal)를 검색하려고 합니다. 검색되고 반환되는 엔터티를 지정하려면 선택적 pii-categories 매개 변수를 적절한 엔터티 범주로 사용합니다. 이 매개 변수를 사용하여 문서 언어에 대해 기본적으로 사용되지 않는 엔터티를 검색할 수도 있습니다. 예를 들어 영어 텍스트에서 발생할 수 있는 프랑스어 드라이버의 라이선스 번호입니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/entities/recognition/pii?piiCategories=[FRDriversLicenseNumber]`

**비동기 작업**

`v3.1-preview.5`부터 `/analyze` 엔드포인트를 사용하여 NER 및 엔터티 링크 설정 요청을 비동기적으로 보낼 수 있습니다.

* 비동기 작업 - `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.5/analyze`

비동기 요청을 보내는 방법에 대한 자세한 내용은 [Text Analytics API를 호출하는 방법](text-analytics-how-to-call-api.md)을 참조하세요.

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

명명된 엔터티 인식 v3은 NER 및 엔터티 링크 설정 요청에 대해 별도의 엔드포인트를 사용합니다. 요청에 따라 아래 URL 형식을 사용합니다.

**엔터티 연결**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[`Linking`에 대한 명명된 엔터티 인식 버전 3.0 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**명명된 엔터티 인식**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[`General`에 대한 명명된 엔터티 인식 버전 3.0 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 요청 본문에서 준비한 JSON 문서를 제공합니다.

## <a name="example-requests"></a>요청 예제

#### <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-preview)

### <a name="example-synchronous-ner-request"></a>동기 NER 요청 예제 

다음 JSON은 API에 보낼 수 있는 콘텐츠의 예제입니다. 두 API 버전의 요청 형식은 동일합니다.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

### <a name="example-asynchronous-ner-request"></a>동기 NER 요청 예제

[비동기 작업](text-analytics-how-to-call-api.md)에 `/analyze` 엔드포인트를 사용하는 경우 API로 보낸 작업을 포함하는 응답을 받게 됩니다.

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

### <a name="example-synchronous-ner-request"></a>동기 NER 요청 예제 

버전 3.0에는 동기 작업만 포함됩니다. 다음 JSON은 API에 보낼 수 있는 콘텐츠의 예제입니다. 두 API 버전의 요청 형식은 동일합니다.

```json
{
  "documents": [
    {
        "id": "1",
        "language": "en",
        "text": "Our tour guide took us up the Space Needle during our trip to Seattle last week."
    }
  ]
}
```

---

## <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 [데이터 제한](../overview.md#data-limits) 문서를 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

## <a name="view-results"></a>결과 보기

모든 POST 요청에서는 ID 및 감지된 엔터티 속성이 있는 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다. 다국어 지원 및 emoji 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 내용은 [텍스트 오프셋을 처리하는 방법](../concepts/text-offsets.md)을 참조하세요.

### <a name="example-responses"></a>예제 응답

버전 3은 일반 NER, PII 및 엔터티 링크 설정에 대한 별도의 엔드포인트를 제공합니다. 버전 3.1-preview에는 비동기 분석 모드가 포함되어 있습니다. 이러한 작업에 대한 응답은 다음과 같습니다. 

#### <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-preview)

### <a name="synchronous-example-results"></a>동기 예제 결과

일반적인 NER 응답의 예제:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-04-01"
}
```

PII 응답의 예제:

```json
{
  "documents": [
    {
    "redactedText": "You can even pre-order from their online menu at *************************, call ************ or send email to ***************************!",
    "id": "0",
    "entities": [
        {
        "text": "www.contososteakhouse.com",
        "category": "URL",
        "offset": 49,
        "length": 25,
        "confidenceScore": 0.8
        }, 
        {
        "text": "312-555-0176",
        "category": "Phone Number",
        "offset": 81,
        "length": 12,
        "confidenceScore": 0.8
        }, 
        {
        "text": "order@contososteakhouse.com",
        "category": "Email",
        "offset": 111,
        "length": 27,
        "confidenceScore": 0.8
        }
      ],
    "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-07-01"
}
```

엔터티 링크 설정 응답의 예제:

```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e", 
          "name": "Space Needle",
          "matches": [
            {
              "text": "Space Needle",
              "offset": 30,
              "length": 12,
              "confidenceScore": 0.4
            }
          ],
          "language": "en",
          "id": "Space Needle",
          "url": "https://en.wikipedia.org/wiki/Space_Needle",
          "dataSource": "Wikipedia"
        },
        {
          "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
          "name": "Seattle",
          "matches": [
            {
              "text": "Seattle",
              "offset": 62,
              "length": 7,
              "confidenceScore": 0.25
            }
          ],
          "language": "en",
          "id": "Seattle",
          "url": "https://en.wikipedia.org/wiki/Seattle",
          "dataSource": "Wikipedia"
        }
      ],
      "warnings": []
    }
  ],
  "errors": [],
  "modelVersion": "2020-02-01"
}
```

### <a name="example-asynchronous-result"></a>비동기 결과 예제

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

일반적인 NER 응답의 예제:
```json
{
  "documents": [
    {
      "id": "1",
      "entities": [
        {
          "text": "tour guide",
          "category": "PersonType",
          "offset": 4,
          "length": 10,
          "confidenceScore": 0.45
        },
        {
          "text": "Space Needle",
          "category": "Location",
          "offset": 30,
          "length": 12,
          "confidenceScore": 0.38
        },
        {
          "text": "trip",
          "category": "Event",
          "offset": 54,
          "length": 4,
          "confidenceScore": 0.78
        },
        {
          "text": "Seattle",
          "category": "Location",
          "subcategory": "GPE",
          "offset": 62,
          "length": 7,
          "confidenceScore": 0.78
        },
        {
          "text": "last week",
          "category": "DateTime",
          "subcategory": "DateRange",
          "offset": 70,
          "length": 9,
          "confidenceScore": 0.8
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

이 문서에서는 Cognitive Services의 텍스트 분석을 사용하여 엔터티 링크 설정에 대한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
* POST 요청은 맞춤형 [액세스 키와 구독에 유효한 엔드포인트](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)를 사용하여 하나 이상의 엔드포인트로 전송됩니다.
* 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 애플리케이션에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/client-libraries-rest-api.md)
* [모델 버전](../concepts/model-versioning.md)
* [새로운 기능](../whats-new.md)
