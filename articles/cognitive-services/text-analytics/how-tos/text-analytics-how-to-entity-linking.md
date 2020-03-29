---
title: 텍스트 분석 API에서 엔터티 인식 사용
titleSuffix: Azure Cognitive Services
description: 텍스트 분석 REST API를 사용하여 텍스트에 있는 엔터티의 ID를 식별하고 모호하게 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203494"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>텍스트 분석에서 명명된 엔터티 인식을 사용하는 방법

텍스트 분석 API를 사용하면 구조화되지 않은 텍스트를 가져와 웹에 대한 자세한 정보에 대한 링크와 함께 모호하지 않은 엔터티 목록을 반환할 수 있습니다. API는 명명된 엔터티 인식(NER) 및 엔터티 연결을 모두 지원합니다.

### <a name="entity-linking"></a>엔터티 연결

엔터티 연결은 텍스트에서 발견된 엔터티의 신원을 식별하고 모호하게 하는 기능입니다(예: 단어의 `Mars` 발생이 행성을 가리키는지 또는 로마전쟁의 신을 가리키는지 여부를 결정하는 기능). 이 프로세스에는 인식된 엔터티를 텍스트로 연결하기 위해 적절한 언어로 기술 자료가 있어야 합니다. 엔터티 연결은 [위키백과를](https://www.wikipedia.org/) 이 기술 자료로 사용합니다.


### <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

NER(명명된 엔터티 인식)는 텍스트에서 서로 다른 엔터티를 식별하고 사람, 위치, 이벤트, 제품 및 조직과 같은 미리 정의된 클래스 또는 유형으로 분류하는 기능입니다.  

버전 3에서 시작되는 Text Analytics API의 이 기능은 전화 번호, 주민등록번호, 이메일 주소 및 은행 계좌 번호와 같은 개인 및 민감한 정보 유형을 식별할 수도 있습니다.  이러한 엔터티를 식별하면 중요한 문서를 분류하고 개인 정보를 수정하는 데 도움이 될 수 있습니다.

## <a name="named-entity-recognition-versions-and-features"></a>명명된 엔터티 인식 버전 및 기능

텍스트 분석 API는 명명된 엔터티 인식의 두 가지 버전인 v2 및 v3를 제공합니다. 버전 3(공개 미리 보기)은 감지하고 분류할 수 있는 엔터티의 세부 정보를 제공합니다.

| 기능                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| 단일 및 일괄 처리 요청을 위한 메서드                          | X      | X      |
| 여러 범주에 걸친 기본 엔터티 인식              | X      | X      |
| 인식된 엔터티에 대한 확장된 분류                 |        | X      |
| 엔터티 연결 및 NER 요청을 보내기 위한 별도의 끝점입니다. |        | X      |
| 모델 버전 관리                                                |        | X      |

자세한 [내용은 언어 지원을](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) 참조하십시오.


#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

### <a name="entity-types"></a>엔터티 형식

명명된 엔터티 인식 v3는 여러 유형에서 확장된 검색을 제공합니다. 현재 NER v3는 다음과 같은 엔터티 범주를 인식할 수 있습니다.

* 일반
* 개인 정보 

지원되는 엔터티 및 언어에 대한 자세한 목록은 [NER v3 지원되는 엔터티 형식](../named-entity-types.md) 문서를 참조하십시오.

### <a name="request-endpoints"></a>엔드포인트 요청

명명된 엔터티 인식 v3는 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용합니다. 요청에 따라 아래 URL 형식을 사용하십시오.

너 (것)너
* 일반 엔터티 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 개인정보 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>모델 버전 관리

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

### <a name="entity-types"></a>엔터티 형식

> [!NOTE]
> 명명된 엔터티 인식(NER) 버전 2는 다음 엔터티만 지원합니다. NER v3은 공개 미리 보기중이며 텍스트에서 인식되는 엔터티의 수와 깊이를 크게 확장합니다.   

| Type  | 하위 유형 | 예제 |
|:-----------   |:------------- |:---------|
| Person        | 해당 없음\*         | "Jeff", "Bill Gates"     |
| 위치      | 해당 없음\*         | "Redmond, Washington", "Paris"  |
| 조직  | 해당 없음\*         | "Microsoft"   |
| 수량      | Number        | "6", "six"     |
| 수량      | 백분율    | "50%", "fifty percent"|
| 수량      | Ordinal       | "2nd", "second"     |
| 수량      | Age           | "90 day old", "30 years old"    |
| 수량      | 통화      | "$10.99"     |
| 수량      | 차원     | "10 miles", "40 cm"     |
| 수량      | 온도   | "32 degrees"    |
| DateTime      | 해당 없음\*         | "6:30PM February 4, 2012"      |
| DateTime      | Date          | "May 2nd, 2017", "05/02/2017"   |
| DateTime      | Time          | "8am", "8:00"  |
| DateTime      | DateRange     | "May 2nd to May 5th"    |
| DateTime      | TimeRange     | "6pm to 7pm"     |
| DateTime      | Duration      | "1 minute and 45 seconds"   |
| DateTime      | 설정           | "every Tuesday"     |
| URL           | 해당 없음\*         | "https:\//www.bing.com"    |
| Email         | 해당 없음\*         | "support@contoso.com" |
| 미국 전화 번호  | 해당 없음\*         | (미국 전화 번호만) "(312) 555-0176" |
| IP 주소    | 해당 없음\*         | "10.0.0.100" |

\* 입력 및 추출된 엔터티에 따라, 특정 엔터티에 `SubType`이 없을 수 있습니다.  나열된 모든 지원되는 엔터티 유형은 영어, 중국어 간체, 프랑스어, 독일어 및 스페인어 언어에서만 사용할 수 있습니다.

### <a name="request-endpoints"></a>엔드포인트 요청

명명된 엔터티 인식 v2는 NER 및 엔터티 연결 요청에 대한 단일 끝점을 사용합니다.

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>REST API 요청 보내기

### <a name="preparation"></a>준비

ID, 텍스트, 언어 : 당신은이 형식으로 JSON 문서가 있어야합니다.

각 문서는 5,120자 미만이어야 하며 컬렉션당 최대 1,000개의 항목(아이디)을 가질 수 있습니다. 컬렉션은 요청 본문에 제출됩니다.

### <a name="structure-the-request"></a>요청 구성

POST 요청을 만듭니다. 다음 링크에서 [Postman](text-analytics-how-to-call-api.md) 또는 **API 테스트 콘솔을** 사용하여 신속하게 구조화하고 보낼 수 있습니다. 

> [!NOTE]
> Azure Portal에서 Text Analytics 리소스에 대한 키와 엔드포인트를 찾을 수 있습니다. 리소스의 **빠른 시작** 페이지의 **리소스 관리** 아래에 있습니다. 

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기](#tab/version-3)

[명명된 엔터티 인식 v3 참조](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

버전 3은 NER 및 엔터티 연결 요청에 대해 별도의 끝점을 사용합니다. 요청에 따라 아래 URL 형식을 사용하십시오.

너 (것)너
* 일반 엔터티 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* 개인 정보 주체 -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

엔터티 연결
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

[명명된 엔터티 인식(NER) v2 참조](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

버전 2는 엔터티 연결 및 NER 요청에 대해 다음 끝점을 사용합니다. 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Text Analytics API 키를 포함하도록 요청 헤더를 설정합니다. 요청 본문에 준비한 JSON 문서를 제공합니다.

### <a name="example-ner-request"></a>NER 요청 예 

다음은 API에 보낼 수 있는 콘텐츠의 예입니다. 두 API 버전의 요청 형식은 동일합니다.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>요청 게시

요청을 받으면 분석이 수행됩니다. 분당 및 초당 보낼 수 있는 요청의 크기와 수에 대한 내용은 개요의 [데이터 제한](../overview.md#data-limits) 섹션을 참조하세요.

Text Analytics API는 상태를 저장하지 않습니다. 계정에 데이터가 저장되지 않으며, 결과가 응답에서 즉시 반환됩니다.

## <a name="view-results"></a>결과 보기

모든 POST 요청은 ID 및 감지된 엔터티 속성을 사용하여 JSON 형식의 응답을 반환합니다.

출력은 즉시 반환됩니다. JSON을 승인하는 애플리케이션으로 결과를 스트림하거나 로컬 시스템의 파일에 출력을 저장하고, 데이터를 정렬, 검색 및 조작할 수 있는 애플리케이션으로 가져올 수 있습니다. 다국어 및 이모티콘 지원으로 인해 응답에 텍스트 오프셋이 포함될 수 있습니다. 자세한 [내용은 텍스트 오프셋을 처리하는 방법을 참조하세요.](../concepts/text-offsets.md)

#### <a name="version-30-preview"></a>[버전 3.0 미리 보기)](#tab/version-3)

### <a name="example-v3-responses"></a>예제 v3 응답

버전 3은 NER 및 엔터티 연결에 대해 별도의 끝점을 제공합니다. 두 작업에 대한 응답은 다음과 같습니다. 

#### <a name="example-ner-response"></a>NER 응답 예

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>응답을 연결하는 예제 엔터티

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21"></a>[버전 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>NER v2 응답 예
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>요약

이 문서에서는 Cognitive Services의 텍스트 분석을 사용하여 엔터티 링크 설정에 대한 개념과 워크플로를 배웠습니다. 요약하면 다음과 같습니다.

* 명명된 엔터티 인식은 두 가지 버전으로 선택한 언어에 사용할 수 있습니다.
* 요청 본문의 JSON 문서에는 ID, 텍스트 및 언어 코드가 포함됩니다.
* POST 요청은 개인 설정된 [액세스 키와](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) 구독에 유효한 끝점을 사용하여 하나 이상의 끝점으로 전송됩니다.
* 연결된 엔터티(각 문서 ID에 대한 신뢰도 점수, 오프셋 및 웹 링크 포함)로 구성된 응답 출력은 모든 애플리케이션에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Text Analytics 개요](../overview.md)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
