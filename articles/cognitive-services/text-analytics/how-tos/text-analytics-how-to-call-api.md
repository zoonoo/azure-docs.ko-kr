---
title: 텍스트 분석 API 호출
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Cognitive Services Text Analytics REST API 및 Postman을 호출 하는 방법을 설명 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.openlocfilehash: 2977946b2e1f37aa356ee075d2caac237170df0f
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95993350"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>텍스트 분석 REST API를 호출하는 방법

이 문서에서는 Text Analytics REST API 및 [Postman](https://www.postman.com/downloads/) 을 사용 하 여 주요 개념을 보여 줍니다. API는 서비스의 기능을 사용 하기 위한 여러 동기 및 비동기 끝점을 제공 합니다. 

## <a name="using-the-api-asynchronously"></a>비동기식으로 API 사용

V 3.1-preview. 3부터 텍스트 분석 API는 두 개의 비동기 끝점을 제공 합니다. 

* `/analyze`Text Analytics 끝점을 사용 하면 하나의 API 호출에서 여러 텍스트 분석 기능이 있는 동일한 텍스트 문서 집합을 분석할 수 있습니다. 이전에는 여러 기능을 사용 하기 위해 각 작업에 대해 별도의 API 호출을 수행 해야 합니다. 둘 이상의 Text Analytics 기능이 포함 된 많은 문서 집합을 분석 해야 하는 경우이 기능을 고려해 야 합니다.

* `/health`상태에 대 한 Text Analytics 끝점으로, 임상 문서에서 관련 의료 정보를 추출 하 고 레이블을 지정할 수 있습니다.  

비동기 방식으로 사용할 수 있는 기능을 확인 하려면 아래 표를 참조 하세요. 끝점에서 몇 가지 기능만 호출할 수 있습니다 `/analyze` . 

| 기능 | 동기 | 비동기 |
|--|--|--|
| 언어 검색 | ✔ |  |
| 정서 분석 | ✔ |  |
| 의견 마이닝 | ✔ |  |
| 핵심 문구 추출 | ✔ | ✔* |
| 명명 된 엔터티 인식 (PII 및 no 포함) | ✔ | ✔* |
| 상태 (컨테이너)에 대 한 Text Analytics | ✔ |  |
| 상태에 대 한 Text Analytics (API) |  | ✔  |

`*` -끝점을 통해 비동기적으로 호출 `/analyze` 됩니다.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

## <a name="prerequisites"></a>필수 조건


> [!NOTE]
> 또는 끝점을 사용 하려는 경우 표준 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) 을 사용 하 여 Text Analytics 리소스가 필요 합니다 `/analyze` `/health` .

1.  먼저 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) 로 이동 하 여 새 Text Analytics 리소스를 만듭니다 (아직 없는 경우). 또는 끝점을 사용 하려는 경우 표준 가격 책정 계층을 선택 합니다 `/analyze` `/health` .

2.  사용할 끝점을 사용 하려는 지역을 선택 합니다.

3.  Text Analytics 리소스를 만들고 페이지 왼쪽의 "키 및 끝점 블레이드"로 이동 합니다. 나중에 Api를 호출할 때 사용할 키를 복사 합니다. 나중에 헤더에 대 한 값으로이를 추가 `Ocp-Apim-Subscription-Key` 합니다.


<a name="json-schema"></a>

## <a name="api-request-format"></a>API 요청 형식

#### <a name="synchronous"></a>[동기](#tab/synchronous)

API 요청 형식은 모든 동기 작업에 대해 동일 합니다. 문서는 JSON 개체에서 원시 비구조적 텍스트로 전송 됩니다. XML은 지원되지 않습니다. JSON 스키마는 아래에 설명 된 요소로 구성 됩니다.

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`id` |데이터 형식은 문자열이지만 실제로 문서 ID는 정수인 경우가 많습니다. | 필수 | 시스템은 사용자가 제공하는 ID를 사용하여 출력을 구성합니다. 언어 코드, 핵심 구 및 감정 점수가 요청의 각 ID에 대해 생성됩니다.|
|`text` | 최대 5120 자의 비구조적 원시 텍스트입니다. | 필수 | 언어 감지의 경우 텍스트를 어떤 언어로도 나타낼 수 있습니다. 감정 분석, 핵심 구 추출 및 엔터티 식별의 경우 텍스트는 [지원되는 언어](../language-support.md)로 작성되어야 합니다. |
|`language` | [지원되는 언어](../language-support.md)의 2자리 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 코드 | 상황에 따라 다름 | 감정 분석, 핵심 구 추출 및 엔터티 연결의 경우 필수. 언어 감지의 경우 옵션 제외해도 오류는 발생하지 않지만 분석 효과가 약해집니다. 언어 코드는 사용자가 제공한 `text`와 일치해야 합니다. |

다음은 동기 Text Analytics 끝점에 대 한 API 요청의 예입니다. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="analyze"></a>[분석](#tab/analyze)

> [!NOTE]
> Text Analytics 클라이언트 라이브러리의 최신 시험판을 사용 하면 클라이언트 개체를 사용 하 여 비동기 분석 작업을 호출할 수 있습니다. GitHub에 대 한 예제를 찾을 수 있습니다.
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

끝점을 사용 하 여 `/analyze` 단일 API 호출에서 사용 하려는 지원 되는 Text Analytics 기능을 선택할 수 있습니다. 이 끝점은 현재 다음을 지원 합니다.

* 핵심 구 추출 
* 명명 된 엔터티 인식 (PII 및 no 포함)

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`displayName` | String | 선택 사항 | 작업에 대 한 고유 식별자의 표시 이름으로 사용 됩니다.|
|`analysisInput` | 아래 필드가 포함 됩니다. `documents` | 필수 | 보내려는 문서에 대 한 정보를 포함 합니다. |
|`documents` | `id`아래와 필드를 포함 합니다. `text` | 필수 | 전송 중인 각 문서와 문서의 원시 텍스트에 대 한 정보를 포함 합니다. |
|`id` | String | 필수 | 제공 하는 Id는 출력을 구조화 하는 데 사용 됩니다. |
|`text` | 최대 125000 자의 비구조적 원시 텍스트입니다. | 필수 | 는 현재 지원 되는 유일한 언어인 영어 언어 여야 합니다. |
|`tasks` | 에는, 또는와 같은 Text Analytics 기능이 포함 되어 있습니다 `entityRecognitionTasks` `keyPhraseExtractionTasks` `entityRecognitionPiiTasks` . | 필수 | 사용 하려는 Text Analytics 기능 중 하나 이상입니다. 에는 `entityRecognitionPiiTasks` `domain` 또는로 설정할 수 있는 선택적 매개 변수가 있습니다 `pii` `phi` . 지정 하지 않으면 시스템은 기본적으로로 설정 `pii` 됩니다. |
|`parameters` | `model-version`아래와 필드를 포함 합니다. `stringIndexType` | 필수 | 이 필드는 사용자가 선택한 위의 기능 작업 내에 포함 되어 있습니다. 여기에는 사용 하려는 모델 버전과 인덱스 형식에 대 한 정보가 포함 됩니다. |
|`model-version` | String | 필수 | 사용 하려는 모델의 버전을 지정 합니다.  |
|`stringIndexType` | String | 필수 | 프로그래밍 환경에 맞는 텍스트 디코더를 지정 합니다.  지원 되는 형식은 `textElement_v8` (기본값), `unicodeCodePoint` , `utf16CodeUnit` 입니다. 자세한 내용은 [텍스트 오프셋 문서](../concepts/text-offsets.md#offsets-in-api-version-31-preview) 를 참조 하세요.  |
|`domain` | String | 선택 사항 | 는 작업에 매개 변수로만 적용 `entityRecognitionPiiTasks` 되며 또는로 설정할 수 있습니다 `pii` `phi` . `pii`지정 되지 않은 경우 기본적으로로 설정 됩니다.  |

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
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }]
    }
}

```

#### <a name="text-analytics-for-health"></a>[의료 분야 Text Analytics](#tab/health)

상태 호스팅 API에 대 한 Text Analytics에 대 한 API 요청 형식은 해당 컨테이너의 경우와 동일 합니다. 문서는 JSON 개체에서 원시 비구조적 텍스트로 전송 됩니다. XML은 지원되지 않습니다. JSON 스키마는 아래에 설명 된 요소로 구성 됩니다.  상태 공개 미리 보기에 대 한 Text Analytics에 대 한 액세스를 요청 하려면 [Cognitive Services 요청 양식을](https://aka.ms/csgate) 작성 하 고 제출 하세요. 상태 사용에 대 한 Text Analytics 요금은 청구 되지 않습니다. 

| 요소 | 유효한 값 | 필수 여부 | 사용량 |
|---------|--------------|-----------|-------|
|`id` |데이터 형식은 문자열이지만 실제로 문서 ID는 정수인 경우가 많습니다. | 필수 | 시스템은 사용자가 제공하는 ID를 사용하여 출력을 구성합니다. |
|`text` | 최대 5120 자의 비구조적 원시 텍스트입니다. | 필수 | 영어 텍스트만 현재 지원 됩니다. |
|`language` | [지원되는 언어](../language-support.md)의 2자리 [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) 코드 | 필수 | `en`현재만 지원 됩니다. |

다음은 상태 끝점의 Text Analytics에 대 한 API 요청의 예입니다. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> 텍스트 분석 API 데이터를 전송 하는 속도 및 크기 제한에 대 한 자세한 내용은 [데이터 및 속도 제한](../concepts/data-limits.md) 문서를 참조 하세요.


## <a name="set-up-a-request"></a>요청 설정 

Postman (또는 다른 web API 테스트 도구)에서 사용 하려는 기능에 대 한 끝점을 추가 합니다. 아래 표를 사용 하 여 적절 한 끝점 형식을 찾고를 `<your-text-analytics-resource>` 리소스 끝점으로 바꿉니다. 예들 들어 다음과 같습니다.

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[동기](#tab/synchronous)

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 언어 검색 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| 정서 분석 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| 의견 마이닝 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| 핵심 문구 추출 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| 명명 된 엔터티 인식-일반 | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| 명명 된 엔터티 인식-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| 명명 된 엔터티 인식-화 | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="analyze"></a>[분석](#tab/analyze)

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 분석 작업 제출 | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze` |
| 분석 상태 및 결과 가져오기 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>` |

#### <a name="text-analytics-for-health"></a>[의료 분야 Text Analytics](#tab/health)

| 기능 | 요청 유형 | 리소스 엔드포인트 |
|--|--|--|
| 상태 작업을 위한 Text Analytics 제출  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs` |
| 작업 상태 및 결과 가져오기 | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |
| 작업 취소 | Delete | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.3/entities/health/jobs/<Operation-Location>` |

--- 

끝점을 만든 후 Postman (또는 다른 web API 테스트 도구)에서 다음을 수행 합니다.

1. 사용 하려는 기능에 대 한 요청 형식을 선택 합니다.
2. 위의 표에서 원하는 적절 한 작업의 끝점에 붙여넣습니다.
3. 다음 세 개의 요청 헤더를 설정합니다.

   + `Ocp-Apim-Subscription-Key`: Azure Portal에서 가져온 액세스 키
   + `Content-Type`: application/json
   + `Accept`: application/json

    Postman을 사용 하는 경우 요청은 끝점으로 간주 되는 다음 스크린샷 처럼 표시 됩니다 `/keyPhrases` .
    
    ![엔드포인트 및 헤더를 포함하는 요청 스크린샷](../media/postman-request-keyphrase-1.png)
    
4. **본문** 형식에 대해 **raw** 를 선택 합니다.
    
    ![본문 설정을 포함하는 요청 스크린샷](../media/postman-request-body-raw.png)

5. 일부 JSON 문서를 올바른 형식으로 붙여넣습니다. 위의 **API 요청 형식** 섹션에서 예제를 사용 하 고 자세한 내용과 예제는 아래 항목을 참조 하세요.

      + [언어 감지](text-analytics-how-to-language-detection.md)
      + [핵심 구 추출](text-analytics-how-to-keyword-extraction.md)
      + [감정 분석](text-analytics-how-to-sentiment-analysis.md)
      + [엔터티 인식](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>요청 보내기

API 요청을 제출 합니다. 동기 끝점에 대 한 호출을 수행한 경우 응답은 요청에 제공 된 각 문서 ID에 대 한 항목을 포함 하는 단일 JSON 문서로 즉시 표시 됩니다.

비동기 또는 끝점에 대 한 호출을 수행한 경우 `/analyze` `/health` 202 응답 코드를 받았는지 확인 합니다. 결과를 보려면 응답을 받아야 합니다.

1. API 응답에서, `Operation-Location` api로 보낸 작업을 식별 하는 헤더에서를 찾습니다. 
2. 사용한 끝점에 대 한 GET 요청을 만듭니다. 끝점 형식에 대 한 [위의 표](#set-up-a-request) 를 참조 하 고 [API 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus)를 검토 하세요. 예들 들어 다음과 같습니다.

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/analyze/jobs/<Operation-Location>`

3. 요청에를 추가 합니다 `Operation-Location` .

4. 응답은 요청에 제공 된 각 문서 ID에 대 한 항목을 포함 하는 단일 JSON 문서가 됩니다.

## <a name="example-api-responses"></a>API 응답 예제
 
# <a name="synchronous"></a>[동기](#tab/synchronous)

동기 끝점 응답은 사용 하는 끝점에 따라 달라 집니다. 예제 응답은 다음 문서를 참조 하세요.

+ [언어 감지](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [핵심 구 추출](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [감정 분석](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [엔터티 인식](text-analytics-how-to-entity-linking.md#view-results)

# <a name="analyze"></a>[분석](#tab/analyze)

성공 하는 경우 끝점에 대 한 GET 요청은 `/analyze` 할당 된 작업을 포함 하는 개체를 반환 합니다. 예: `keyPhraseExtractionTasks`. 이러한 작업은 적절 한 Text Analytics 기능의 응답 개체를 포함 합니다. 자세한 내용은 다음 문서를 참조하세요.

+ [핵심 구 추출](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [엔터티 인식](text-analytics-how-to-entity-linking.md#view-results)


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

# <a name="text-analytics-for-health"></a>[의료 분야 Text Analytics](#tab/health)

상태 비동기 API 응답의 Text Analytics에 대 한 자세한 내용은 다음 문서를 참조 하세요.

+ [의료 분야 Text Analytics](text-analytics-for-health.md#hosted-asynchronous-web-api-response)


--- 

## <a name="see-also"></a>참고 항목

* [Text Analytics 개요](../overview.md)
* [FAQ(질문과 대답)](../text-analytics-resource-faq.md)</br>
* [Text Analytics 제품 페이지](//go.microsoft.com/fwlink/?LinkID=759712)
* [Text Analytics 클라이언트 라이브러리 사용](../quickstarts/text-analytics-sdk.md)
* [새로운 기능](../whats-new.md)
