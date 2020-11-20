---
title: 텍스트 분석 API에서 엔터티 인식 사용
titleSuffix: Azure Cognitive Services
description: Text Analytics REST API를 사용 하 여 텍스트에 있는 엔터티의 id를 식별 하 고 명확 하 게 구분 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: ef06faa17739153b2a04e777498e1de6e97c0646
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957098"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Text Analytics에서 명명 된 엔터티 인식을 사용 하는 방법

텍스트 분석 API를 사용 하면 구조화 되지 않은 텍스트를 가져와 웹에 대 한 자세한 정보에 대 한 링크가 포함 된 명확 하지 않은 엔터티 목록을 반환할 수 있습니다. API는 여러 엔터티 범주에 대해 명명 된 엔터티 인식 (NER) 및 엔터티 연결을 모두 지원 합니다.

## <a name="entity-linking"></a>엔터티 연결

엔터티 연결은 텍스트에 있는 엔터티의 id를 식별 하 고 구분 하는 기능입니다. 예를 들어 "Mars" 라는 단어의 발생이 행성을 참조 하는지 또는 전쟁의 Roman 신의 것을 확인 하는 것입니다. 이 프로세스에서는 인식 된 엔터티를 텍스트에 연결 하기 위해 기술 자료가 적절 한 언어로 존재 해야 합니다. 엔터티 링크는이 기술 자료로 [위키백과](https://www.wikipedia.org/) 를 사용 합니다.

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

NER (명명 된 엔터티 인식)는 텍스트에서 다양 한 엔터티를 식별 하 여 사용자, 위치, 이벤트, 제품, 조직 등의 미리 정의 된 클래스 또는 형식으로 분류 하는 기능입니다.  

## <a name="personally-identifiable-information-pii"></a>PII(개인 식별 정보)

PII 기능은 NER의 일부 이며, 전화 번호, 전자 메일 주소, 우편 주소, 여권 번호 등의 개별 사람과 연결 된 텍스트에서 중요 한 엔터티를 식별 하 고 교정 할 수 있습니다.  

## <a name="named-entity-recognition-features-and-versions"></a>명명 된 엔터티 인식 기능 및 버전

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

| 기능                                                         | NER v 3.0 | NER v 3.1-preview. 3 |
|-----------------------------------------------------------------|--------|----------|
| 단일 및 일괄 처리 요청을 위한 메서드                          | X      | X        |
| 여러 범주에서 확장 된 엔터티 인식           | X      | X        |
| 엔터티 연결 및 NER 요청을 보내기 위한 별도의 끝점입니다. | X      | X        |
| 개인 ( `PII` ) 및 상태 ( `PHI` ) 정보 엔터티 인식        |        | X        |
| 교정 `PII`        |        | X        |

자세한 내용은 [언어 지원](../language-support.md) 을 참조 하세요.

명명 된 엔터티 인식 v3은 여러 형식에서 확장 된 검색을 제공 합니다. 현재 NER v 3.0은 [일반 엔터티 범주의](../named-entity-types.md)엔터티를 인식할 수 있습니다.

명명 된 엔터티 인식 v 3.1-preview. 3에는 v 3.0의 검색 기능이 포함 되어 있습니다. 
* 끝점을 사용 하 여 개인 정보 ()를 검색할 수 있는 기능 `PII` `v3.1-preview.3/entities/recognition/pii` 입니다. 
* `domain=phi`기밀 상태 정보를 검색 하는 선택적 매개 변수 ( `PHI` )입니다.
* 끝점을 사용 하는 [비동기 작업](text-analytics-how-to-call-api.md) `/analyze` 입니다.

자세한 내용은 아래의 [엔터티 범주](../named-entity-types.md) 문서 및 [요청 끝점](#request-endpoints) 섹션을 참조 하세요. 

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기

### <a name="preparation"></a>준비

ID, 텍스트, 언어 형식의 JSON 문서가 있어야 합니다.

각 문서는 5120 자 미만 이어야 하 고 컬렉션 당 최대 1000 항목 (Id)을 사용할 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. 다음 링크에서 [Postman](text-analytics-how-to-call-api.md) 또는 **API 테스트 콘솔** 을 사용 하 여 신속 하 게 구조를 구성 하 고 보낼 수 있습니다. 

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 


### <a name="request-endpoints"></a>요청 엔드포인트

#### <a name="version-31-preview3"></a>[버전 3.1-preview. 3](#tab/version-3-preview)

명명 된 엔터티 인식은 `v3.1-preview.3` NER, PII 및 엔터티 연결 요청에 대해 별도의 끝점을 사용 합니다. 요청에 따라 아래 URL 형식을 사용 합니다.

**엔터티 연결**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/linking`

[명명 된 엔터티 인식 버전 3.1-에 대 한 미리 보기 참조 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesLinking)

**명명된 엔터티 인식**
* 일반 엔터티- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/general`

[명명 된 엔터티 인식 버전 3.1-에 대 한 미리 보기 참조 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionGeneral)

**PII(개인 식별 정보)**
* 개인 ( `PII` ) 정보- `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii`

선택적 `domain=phi` 매개 변수를 사용 하 여 텍스트에서 상태 ( `PHI` ) 정보를 검색할 수도 있습니다. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi`

부터 `v3.1-preview.3` JSON 응답은 `redactedText` 검색 된 PII 엔터티가 `*` 엔터티의 각 문자에 대해로 대체 되는 수정 된 입력 텍스트를 포함 하는 속성을 포함 합니다.

[명명 된 엔터티 인식 버전 3.1-에 대 한 미리 보기 참조 `PII`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-3/operations/EntitiesRecognitionPii)

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

명명 된 엔터티 인식 v3은 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용 합니다. 요청에 따라 아래 URL 형식을 사용 합니다.

**엔터티 연결**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

[에 대 한 명명 된 엔터티 인식 버전 3.0 참조 `Linking`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

**명명된 엔터티 인식**
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

[에 대 한 명명 된 엔터티 인식 버전 3.0 참조 `General`](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral)

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 요청 본문에서 준비한 JSON 문서를 제공 합니다.

### <a name="example-ner-request"></a>예제 NER 요청 

다음 JSON은 API에 보낼 수 있는 콘텐츠의 예입니다. 두 API 버전의 요청 형식은 동일합니다.

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

## <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

## <a name="view-results"></a>결과 보기

모든 POST 요청에서는 Id와 검색 된 엔터티 속성을 사용 하 여 JSON 형식의 응답을 반환 합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다. 다국어 지원 및 emoji 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 내용은 [텍스트 오프셋을 처리 하는 방법](../concepts/text-offsets.md)을 참조 하세요.

### <a name="example-responses"></a>예제 응답

버전 3은 일반 NER, PII 및 엔터티 링크를 위한 별도의 끝점을 제공 합니다. 두 작업 모두에 대 한 응답은 아래와 같습니다. 

#### <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-preview)

PII 응답의 예:
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

엔터티 연결 응답의 예:

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


#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

일반적인 NER 응답의 예는 다음과 같습니다.
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
* POST 요청은 개인 설정 된 [액세스 키와](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 구독에 유효한 끝점을 사용 하 여 하나 이상의 끝점으로 전송 됩니다.
* 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 애플리케이션에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
