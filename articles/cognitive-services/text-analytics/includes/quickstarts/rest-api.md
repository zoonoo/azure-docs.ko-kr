---
title: '빠른 시작: cURL을 사용하여 Text Analytics REST API 호출'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Cognitive Services에서 Text Analytics API 사용을 빠르게 시작하는 방법을 보여줍니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/01/2020
ms.author: aahi
ms.openlocfilehash: 17c24e19068c3d084da8a3c888729c2f78185c17
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445330"
---
# <a name="version-31-preview"></a>[버전 3.1 미리 보기](#tab/version-3-1)

[v3.1 참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/)

# <a name="version-30"></a>[버전 3.0](#tab/version-3)

[v3 참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0) 

---

## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/).
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics 리소스를 만들어"  target="_blank">Text Analytics 리소스를 만들어</a> 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Text Analytics API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!NOTE]
> * 다음 BASH 예제에서는 `\` 줄 연속 문자를 사용합니다. 콘솔 또는 터미널에서 다른 줄 연속 문자를 사용하는 경우 해당 문자를 사용하세요.
> * [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code)에서 언어별 샘플을 찾을 수 있습니다.
> * Azure Portal로 이동하여 필수 구성 요소에서 만든 Text Analytics 리소스의 키와 엔드포인트를 찾습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 그런 다음, 아래 코드의 문자열을 키와 엔드포인트로 바꿉니다.
Text Analytics API를 호출하려면 다음 정보가 필요합니다.


|매개 변수  |Description  |
|---------|---------|
|`-X POST <endpoint>`     | API에 액세스하기 위한 엔드포인트를 지정합니다.        |
|`-H Content-Type: application/json`     | JSON 데이터를 보내기 위한 콘텐츠 형식.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | API에 액세스하기 위한 키를 지정합니다.        |
|`-d <documents>`     | 보내려는 문서가 포함된 JSON.         |

다음 cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 JSON 값을 사용하여 이 명령을 편집합니다.

## <a name="sentiment-analysis"></a>감정 분석

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[버전 3.1-preview](#tab/version-3-1)

> [!NOTE]
> 아래 예제는 `opinionMining=true` 매개 변수를 사용하는 감정 분석에 대한 오피니언 마이닝 요청을 포함하며, 양상과 관련된 의견에 대한 더 세부적인 정보(예: 제품 또는 서비스의 특성)를 텍스트 형식으로 제공합니다.

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/sentiment?opinionMining=true \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "The customer service here is really good."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "sentiment":"positive",
         "confidenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
         },
         "sentences":[
            {
               "sentiment":"positive",
               "confidenceScores":{
                  "positive":1.0,
                  "neutral":0.0,
                  "negative":0.0
               },
               "offset":0,
               "length":41,
               "text":"The customer service here is really good.",
               "aspects":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":4,
                     "length":16,
                     "text":"customer service",
                     "relations":[
                        {
                           "relationType":"opinion",
                           "ref":"#/documents/0/sentences/0/opinions/0"
                        }
                     ]
                  }
               ],
               "opinions":[
                  {
                     "sentiment":"positive",
                     "confidenceScores":{
                        "positive":1.0,
                        "negative":0.0
                     },
                     "offset":36,
                     "length":4,
                     "text":"good",
                     "isNegated":false
                  }
               ]
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
``` 

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/sentiment/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "I had the best day of my life. I wish you were there with me."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
  "documents":[
    {
      "id":"1",
      "sentiment":"positive",
      "documentScores":{
        "positive":1.0,
        "neutral":0.0,
        "negative":0.0
      },
      "sentences":[
        {
          "sentiment":"positive",
          "sentenceScores":{
            "positive":1.0,
            "neutral":0.0,
            "negative":0.0
          },
          "offset":0,
          "length":30
        }
      ]
    }
  ],
  "errors":[
  ],
  "modelVersion":"2019-10-01"
}
```

---


## <a name="language-detection"></a>언어 검색

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[버전 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":0.99
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-09-01"
}
```

---


## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[버전 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}

```

### <a name="detecting-personally-identifying-information"></a>개인 식별 정보 검색

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

```bash
curl -X POST https://your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/recognition/pii \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Call our office at 312-555-1234, or send an email to support@contoso.com"}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "redactedText":"Insurance policy for *** on file 123-12-1234 is here by approved.",
         "id":"1",
         "entities":[
            {
               "text":"SSN",
               "category":"Organization",
               "offset":21,
               "length":3,
               "confidenceScore":0.45
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/recognition/general \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```


### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "text":"trip",
               "category":"Event",
               "offset":18,
               "length":4,
               "confidenceScore":0.61
            },
            {
               "text":"Seattle",
               "category":"Location",
               "subcategory":"GPE",
               "offset":26,
               "length":7,
               "confidenceScore":0.82
            },
            {
               "text":"last week",
               "category":"DateTime",
               "subcategory":"DateRange",
               "offset":34,
               "length":9,
               "confidenceScore":0.8
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-04-01"
}
```

---

## <a name="entity-linking"></a>엔터티 연결

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]

#### <a name="version-31-preview"></a>[버전 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "bingId":"a093e9b9-90f5-a3d5-c4b8-5855e1b01f85",
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"0d47c987-0042-5576-15e8-97af601614fa",
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"df2c4376-9923-6a54-893f-2ee5a5badbc7",
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "bingId":"52535f87-235e-b513-54fe-c03e4233ac6e",
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/entities/linking \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975."}]}'
```


### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "entities":[
            {
               "name":"Microsoft",
               "matches":[
                  {
                     "text":"Microsoft",
                     "offset":0,
                     "length":9,
                     "confidenceScore":0.48
                  }
               ],
               "language":"en",
               "id":"Microsoft",
               "url":"https://en.wikipedia.org/wiki/Microsoft",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Bill Gates",
               "matches":[
                  {
                     "text":"Bill Gates",
                     "offset":25,
                     "length":10,
                     "confidenceScore":0.52
                  }
               ],
               "language":"en",
               "id":"Bill Gates",
               "url":"https://en.wikipedia.org/wiki/Bill_Gates",
               "dataSource":"Wikipedia"
            },
            {
               "name":"Paul Allen",
               "matches":[
                  {
                     "text":"Paul Allen",
                     "offset":40,
                     "length":10,
                     "confidenceScore":0.54
                  }
               ],
               "language":"en",
               "id":"Paul Allen",
               "url":"https://en.wikipedia.org/wiki/Paul_Allen",
               "dataSource":"Wikipedia"
            },
            {
               "name":"April 4",
               "matches":[
                  {
                     "text":"April 4",
                     "offset":54,
                     "length":7,
                     "confidenceScore":0.38
                  }
               ],
               "language":"en",
               "id":"April 4",
               "url":"https://en.wikipedia.org/wiki/April_4",
               "dataSource":"Wikipedia"
            }
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-02-01"
}
```


---


## <a name="key-phrase-extraction"></a>핵심 문구 추출

[!INCLUDE [REST API quickstart instructions](rest-api-instructions.md)]


#### <a name="version-31-preview"></a>[버전 3.1-preview](#tab/version-3-1)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1-preview.3/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "Hello world. This is some input text that I love."}]}'
```

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

#### <a name="version-30"></a>[버전 3.0](#tab/version-3)

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.0/keyPhrases \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", language:"en", text: "I had a wonderful trip to Seattle last week."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "keyPhrases":[
            "wonderful trip",
            "Seattle",
            "week"
         ],
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2020-07-01"
}
```

---
