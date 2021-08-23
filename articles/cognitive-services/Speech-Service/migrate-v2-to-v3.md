---
title: v2에서 v3 REST API로 마이그레이션 - Speech Service
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 개발자가 Speech Services Speech-to-Text REST API v2에서 v3으로 코드를 마이그레이션하는 방법을 설명합니다.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e8e55202163a447ca2d7d08999c953e619bc027b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529175"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>REST API v2.0에서 v3.0으로 코드 마이그레이션

v2에 비해 음성 텍스트 변환용 Speech Service REST API의 v3 버전은 더 안정적이고 사용하기 쉬우며 유사한 서비스용 API와의 일관성이 더 뛰어납니다. 대부분의 팀은 하루나 이틀 안에 v2에서 v3으로 마이그레이션할 수 있습니다.

## <a name="forward-compatibility"></a>이후 버전과의 호환성

v2의 모든 항목은 동일한 ID의 v3 API에서도 찾을 수 있습니다. 결과 스키마가 변경된 경우(예: 전사) v3 버전 API의 GET 결과는 v3 스키마를 사용합니다. v2 버전 API의 GET 결과는 동일한 v2 스키마를 사용합니다. v3에서 새로 생성된 항목은 v2 API의 응답에서 사용할 수  **없습니다** . 

## <a name="migration-steps"></a>마이그레이션 단계

이는 마이그레이션을 준비할 때 알아야 할 항목의 요약 목록입니다. 자세한 내용은 개별 링크에서 찾을 수 있습니다. 현재 API 사용에 따라 여기에 나열된 모든 단계가 적용되는 것은 아닙니다. 일부의 경우에만 호출 코드에서 중요한 변경이 필요합니다. 대부분의 변경은 항목 이름만 변경하면 됩니다. 

일반 변경 내용: 

1. [호스트 이름 변경](#host-name-changes)

1. [클라이언트 코드에서 속성 ID의 이름을 self로 변경](#identity-of-an-entity) 

1. [엔터티 컬렉션을 반복하도록 코드 변경](#working-with-collections-of-entities)

1. [클라이언트 코드에서 속성 이름을 displayName으로 변경](#name-of-an-entity)

1. [참조된 엔터티의 메타데이터 검색 조정](#accessing-referenced-entities)

1. Batch Transcription을 사용하는 경우: 

    * [Batch Transcription 생성을 위해 코드 조정](#creating-transcriptions) 

    * [새로운 전사 결과 스키마에 맞게 코드 조정](#format-of-v3-transcription-results)

    * [결과 검색 방법에 대한 코드 조정](#getting-the-content-of-entities-and-the-results)

1. 사용자 지정 모델 학습/테스트 API를 사용하는 경우: 

    * [사용자 지정 모델 학습에 수정 내용 적용](#customizing-models)

    * [기본 및 사용자 지정 모델을 검색하는 방법 변경](#retrieving-base-and-custom-models)

    * [클라이언트 코드에서 경로 세그먼트 정확도 테스트의 이름을 evaluations로 변경](#accuracy-tests)

1. 엔드포인트 API를 사용하는 경우:

    * [엔드포인트 로그 검색 방법 변경](#retrieving-endpoint-logs)

1. 기타 사소한 변경 내용: 

    * [POST 요청의 속성 대신 모든 사용자 지정 속성을 customProperties로 전달](#using-custom-properties)

    * [Operation-Location 대신 응답 헤더 Location에서 위치 읽기](#response-headers)

## <a name="breaking-changes"></a>주요 변경 내용

### <a name="host-name-changes"></a>호스트 이름 변경 내용

엔드포인트 호스트 이름이 `{region}.cris.ai`에서 `{region}.api.cognitive.microsoft.com`으로 변경되었습니다. 새 엔드포인트에 대한 경로는 호스트 이름의 일부이기 때문에 더 이상 `api/`를 포함하지 않습니다. [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)는 유효한 지역 및 경로를 나열합니다.
>[!IMPORTANT]
>호스트 이름을 `{region}.cris.ai`에서 `{region}.api.cognitive.microsoft.com`으로 변경합니다. 여기서 region은 음성 구독의 지역입니다. 또한 클라이언트 코드의 모든 경로에서 `api/`를 제거합니다.

### <a name="identity-of-an-entity"></a>엔터티 ID

`id` 속성은 이제 `self`입니다. v2에서는 API 사용자가 API의 경로가 어떻게 생성되는지 알아야 했습니다. 따라서 확장이 불가능했고 사용자의 불필요한 작업이 필요했습니다. 속성 `id`(uuid)는 엔터티(URL)의 위치인 `self`(문자열)로 대체됩니다. 이 값은 모든 엔터티 사이에서 고유합니다. `id`가 코드에 문자열로 저장되면 이름을 바꾸는 것만으로도 새 스키마를 지원할 수 있습니다. 이제 엔터티에 대한 `GET`, `PATCH` 및 `DELETE` REST 호출에 대한 URL로 `self` 콘텐츠를 사용할 수 있습니다.

엔터티에 다른 경로를 통해 사용 가능한 추가 기능이 있는 경우 `links` 아래에 나열됩니다. 다음 전사 예는 전사의 콘텐츠를 `GET`하는 별도의 방법을 보여 줍니다.
>[!IMPORTANT]
>클라이언트 코드에서 `id` 속성의 이름을 `self`로 바꿉니다. 필요한 경우 유형을 `uuid`에서 `string`으로 변경합니다. 

**v2 전사:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**v3 전사:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

코드 구현에 따라 속성 이름을 바꾸는 것만으로는 충분하지 않을 수 있습니다. 클라이언트에서 경로를 생성하는 대신 반환된 `self` 및 `links` 값을 REST 호출의 대상 URL로 사용하는 것이 좋습니다. 반환된 URL을 사용하면 향후 경로 변경으로 인해 클라이언트 코드가 손상되지 않는지 확인할 수 있습니다.

### <a name="working-with-collections-of-entities"></a>엔터티 컬렉션 작업

이전의 v2에서는 API가 사용 가능한 모든 항목을 결과로 반환했습니다. v3에서는 예상되는 응답 크기를 보다 세밀하게 제어할 수 있도록 모든 수집 결과에 페이지가 매겨집니다. 반환되는 항목의 수와 페이지의 시작 오프셋을 제어할 수 있습니다. 이 동작을 통해 응답 프로세서의 런타임을 쉽게 예측할 수 있습니다.

응답의 기본 형태는 모든 컬렉션에서 동일합니다.

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values` 속성에는 사용 가능한 컬렉션 엔터티의 하위 집합이 포함됩니다. 개수 및 오프셋은 `skip` 및 `top` 쿼리 매개 변수를 사용하여 제어할 수 있습니다. `@nextLink`가 `null`이 아니면 더 많은 데이터를 사용할 수 있으며 `$.@nextLink`에서 GET을 수행하여 다음 데이터 배치를 검색할 수 있습니다.

이 변경은 모든 요소가 반환될 때까지 루프에서 컬렉션에 대해 `GET`을 호출해야 합니다.

>[!IMPORTANT]
>`speechtotext/v3.0/{collection}`에 대한 GET 응답에 `$.@nextLink`의 값이 포함된 경우 `$.@nextLink`가 해당 컬렉션의 모든 요소를 검색하도록 설정되지 않을 때까지 `$.@nextLink`에서 `GETs`를 계속 실행합니다.

### <a name="creating-transcriptions"></a>전사 만들기

Batch Transcription을 만드는 방법에 대한 자세한 설명은 [Batch Transcription 방법](./batch-transcription.md)을 참조하세요.

v3 전사 API를 사용하면 특정 전사 옵션을 명시적으로 설정할 수 있습니다. 이제 모든(선택 사항) 구성 속성을 `properties` 속성에서 설정할 수 있습니다.
버전 v3도 여러 입력 파일을 지원하므로 v2와 마찬가지로 단일 URL이 아닌 URL 목록이 필요합니다. v2 속성 이름 `recordingsUrl`가 v3에서 `contentUrls`로 변경되었습니다. v3에서는 전사의 감정 분석 기능이 제거되었습니다. 감정 분석 옵션에 대해서는 Microsoft Cognitive Service [텍스트 분석](https://azure.microsoft.com/services/cognitive-services/text-analytics/)을 참조하세요.

`properties`의 새 속성 `timeToLive`는 완료된 기존 엔터티를 정리하는 데 도움이 될 수 있습니다. `timeToLive`는 완료된 엔터티가 자동으로 삭제되는 기간을 지정합니다. 엔터티가 지속적으로 추적, 사용 및 삭제되어 일반적으로 12시간이 지나기 훨씬 전에 처리되는 경우에는 높은 값(예: `PT12H`)으로 설정합니다.

**v2 전사 POST 요청 본문:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**v3 전사 POST 요청 본문:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>`recordingsUrl` 속성의 이름을 `contentUrls`로 바꾸고 단일 URL 대신 URL 배열을 전달합니다. `diarizationEnabled` 또는 `wordLevelTimestampsEnabled`에 대한 설정을 `string` 대신 `bool`로 전달합니다.

### <a name="format-of-v3-transcription-results"></a>V3 전사 결과의 형식

전사 결과의 스키마가 실시간 엔드포인트에서 생성된 전사에 맞게 약간 변경되었습니다. 새 형식에 대한 자세한 설명은 [Batch Transcription 방법](./batch-transcription.md)을 참조하세요. 결과 스키마는 `samples/batch/transcriptionresult_v3.schema.json`의 [GitHub 샘플 저장소](https://aka.ms/csspeech/samples)에 게시됩니다.

속성 이름은 이제 낙타대문자(camel-case)로 표시되며 `channel` 및 `speaker`의 값은 이제 정수 유형을 사용합니다. 이제 기간 형식은 다른 Azure API에서 사용되는 기간 형식과 일치하는 ISO 8601에 설명된 구조를 사용합니다.

다음은 V3 전사 결과의 샘플입니다. 차이점은 주석에 설명되어 있습니다.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```
>[!IMPORTANT]
>위에 표시된 대로 전사 결과를 새 유형으로 역직렬화합니다. 오디오 채널당 단일 파일 대신 `recognizedPhrases`의 각 요소에 대해 `channel`의 속성 값을 확인하여 채널을 구분합니다. 이제 각 입력 파일에 대한 단일 결과 파일이 있습니다.


### <a name="getting-the-content-of-entities-and-the-results"></a>엔터티와 결과의 콘텐츠 가져오기

v2에서는 입력 또는 결과 파일에 대한 링크가 나머지 엔터티 메타데이터와 함께 인라인되었습니다. v3의 개선 사항으로 엔터티 메타데이터(`$.self`의 GET에서 반환됨)와 결과 파일에 액세스하기 위한 세부 정보 및 자격 증명이 명확하게 구분됩니다. 이러한 분리를 통해 고객 데이터를 보호하고 자격 증명의 유효 기간을 세밀하게 제어할 수 있습니다.

v3에서는 엔티티가 데이터(데이터 세트, 전사, 엔드포인트 또는 평가)를 노출하는 경우 `links`에 `files`라는 하위 속성이 포함됩니다. `$.links.files`의 GET은 파일 목록과 각 파일의 콘텐츠에 액세스하기 위한 SAS URL을 반환합니다. SAS URL의 유효 기간을 제어하기 위해 쿼리 매개 변수 `sasValidityInSeconds`를 사용하여 수명을 지정할 수 있습니다.

**v2 전사:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**v3 전사:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**`$.links.files`에 대한 GET 결과는 다음과 같습니다.**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind` 속성은 파일 콘텐츠의 형식을 나타냅니다. 전사의 경우 `TranscriptionReport` 유형의 파일은 작업의 요약이고 `Transcription` 유형의 파일은 작업 자체의 결과입니다.

>[!IMPORTANT]
>작업 결과를 가져오려면 `/speechtotext/v3.0/{collection}/{id}/files`에서 `GET`을 사용합니다. 더 이상 `/speechtotext/v3.0/{collection}/{id}` 또는 `/speechtotext/v3.0/{collection}`의 `GET` 응답에 포함되지 않습니다.

### <a name="customizing-models"></a>모델 사용자 지정

v3 이전에는 모델이 학습될 때 _음향 모델_ 과 _언어 모델_ 사이에 차이가 있었습니다. 이러한 차이로 인해 엔드포인트 또는 전사를 생성할 때 여러 모델을 지정해야 했습니다. 호출자를 위해 이 프로세스를 단순화하기 위해 차이점을 제거하고 모든 항목이 모델 학습에 사용되는 데이터 세트의 콘텐츠에 따라 달라지도록 했습니다. 이 변경으로 모델 생성은 이제 혼합된 데이터 세트(언어 데이터 및 음향 데이터)를 지원합니다. 이제 엔드포인트와 텍스트 변환에는 하나의 모델만 필요합니다.

이 변경으로 `POST` 작업에서 `kind`에 대한 필요성이 제거되었으며 이제 `datasets[]` 배열에 동일하거나 혼합된 종류의 여러 데이터 세트가 포함될 수 있습니다.

학습된 모델의 결과를 개선하기 위해 음향 데이터는 자동으로 언어 학습 중에 내부적으로 사용됩니다. 일반적으로 v3 API를 통해 생성된 모델은 v2 API로 생성된 모델보다 더 정확한 결과를 제공합니다.

>[!IMPORTANT]
>음향 및 언어 모델 부분을 모두 사용자 지정하려면 POST의 `datasets[]`에 있는 모든 필수 언어 및 음향 데이터 세트를 `/speechtotext/v3.0/models`에 전달합니다. 이렇게 하면 두 부품이 모두 사용자 지정된 단일 모델이 생성됩니다.

### <a name="retrieving-base-and-custom-models"></a>기본 모델 및 사용자 지정 모델 검색

사용 가능한 모델을 간단하게 얻기 위해 v3에서는 고객 소유의 "사용자 지정 모델"에서 "기본 모델" 컬렉션을 분리했습니다. 이제 두 경로는 `GET /speechtotext/v3.0/models/base` 및 `GET /speechtotext/v3.0/models/`입니다.

v2에서는 모든 모델이 단일 응답으로 함께 반환되었습니다.

>[!IMPORTANT]
>사용자 지정을 위해 제공된 기본 모델 목록을 가져오려면 `/speechtotext/v3.0/models/base`에서 `GET`을 사용합니다. `/speechtotext/v3.0/models`에서 `GET`을 사용하여 나만의 사용자 지정 모델을 찾을 수 있습니다.

### <a name="name-of-an-entity"></a>엔터티의 이름

`name` 속성은 이제 `displayName`입니다. 이는 ID 속성을 나타내지 않는 다른 Azure API와 일치합니다. 이 속성의 값은 고유하지 않아야 하며 `PATCH` 작업으로 엔터티를 만든 후 변경할 수 있습니다.

**v2 전사:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 전사:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>클라이언트 코드에서 `name` 속성의 이름을 `displayName`으로 바꿉니다.

### <a name="accessing-referenced-entities"></a>참조된 엔터티 액세스

v2에서는 참조된 엔터티가 항상 인라인되었습니다(예: 엔드포인트의 사용된 모델). 엔터티의 중첩으로 인해 많은 응답이 발생했으며 소비자는 중첩된 콘텐츠를 거의 사용하지 않았습니다. 응답 크기를 줄이고 성능을 개선하기 위해 참조된 엔터티가 더 이상 응답에 인라인되지 않습니다. 대신 다른 엔터티에 대한 참조가 나타나고 `self` 링크와 동일한 패턴을 따르는 후속 `GET`(URL이기도 함)에 직접 사용할 수 있습니다.

**v2 전사:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**v3 전사:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

위의 예에 표시된 대로 참조된 모델의 세부 정보를 이용해야 하는 경우 `$.model.self`에서 GET을 실행하기만 하면 됩니다.

>[!IMPORTANT]
>참조된 엔터티의 메타데이터를 검색하려면 `$.{referencedEntity}.self`에서 GET을 실행합니다. 예를 들어, 전사 모델을 검색하려면 `$.model.self`에서 `GET`을 수행합니다.


### <a name="retrieving-endpoint-logs"></a>엔드포인트 로그 검색

서비스 버전 v2는 로깅 엔드포인트 결과를 지원했습니다. v2로 엔드포인트의 결과를 검색하려면 시간 범위로 정의된 결과의 스냅샷을 나타내는 "데이터 내보내기"를 만듭니다. 일괄 데이터를 내보내는 프로세스는 유연하지 않았습니다. v3 API를 사용하면 각 개별 파일에 액세스할 수 있으며 해당 파일을 통해 반복할 수 있습니다.

**v3 엔드포인트 실행 성공:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET `$.links.logs`의 응답:**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

엔드포인트 로그의 페이지 매김은 오프셋을 지정할 수 없다는 점을 제외하면 다른 모든 컬렉션과 유사하게 작동합니다. 사용 가능한 데이터의 양이 많기 때문에 페이지 매김은 서버에서 결정됩니다.

v3에서 각 엔드포인트 로그는 파일의 `self`에 대해 `DELETE` 작업을 실행하거나 `$.links.logs`에서 `DELETE`를 사용하여 개별적으로 삭제할 수 있습니다. 종료 날짜를 지정하기 위해 쿼리 매개 변수 `endDate`를 요청에 추가할 수 있습니다.

>[!IMPORTANT]
>`/api/speechtotext/v2.0/endpoints/{id}/data`에서 로그 내보내기를 작성하는 대신 `/v3.0/endpoints/{id}/files/logs/`를 사용하여 로그 파일에 개별적으로 액세스합니다. 

### <a name="using-custom-properties"></a>사용자 지정 속성 사용

선택적 구성 속성에서 사용자 지정 속성을 분리하기 위해 명시적으로 명명된 모든 속성은 이제 `properties` 속성에 있으며 호출자가 정의한 모든 속성은 이제 `customProperties` 속성에 있습니다.

**v2 전사 엔터티:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 전사 엔터티:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

또한 이 변경으로 `properties` 아래에서 명시적으로 명명된 모든 속성에 올바른 유형을 사용할 수 있습니다(예: 문자열 대신 부울).

>[!IMPORTANT]
>`POST` 요청에서 모든 사용자 지정 속성을 `properties` 대신 `customProperties`로 전달합니다.

### <a name="response-headers"></a>응답 헤더

v3에서는 더 이상 `POST` 요청에서 `Location` 헤더 외에 `Operation-Location` 헤더를 반환하지 않습니다. v2에서는 두 헤더 값이 동일했습니다. 이제 `Location`만 반환됩니다.

이제 새 API 버전이 APIM(Azure API 관리)에서 관리되므로 제한 관련 헤더 `X-RateLimit-Limit`, `X-RateLimit-Remaining` 및 `X-RateLimit-Reset`이 응답 헤더에 포함되지 않습니다.

>[!IMPORTANT]
>`Operation-Location` 대신 응답 헤더 `Location`에서 위치를 읽습니다. 429 응답 코드의 경우 `X-RateLimit-Limit`, `X-RateLimit-Remaining` 또는 `X-RateLimit-Reset` 대신 `Retry-After` 헤더 값을 읽습니다.


### <a name="accuracy-tests"></a>정확도 테스트

정확도 테스트는 그 특성을 더 정확하게 설명하기 위해 평가로 이름이 변경되었습니다. 새 경로는 `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`입니다.

>[!IMPORTANT]
>클라이언트 코드에서 경로 세그먼트 `accuracytests`의 이름을 `evaluations`로 바꿉니다.


## <a name="next-steps"></a>다음 단계

다음과 같이 Speech Services에서 제공하며 일반적으로 사용되는 REST API의 모든 기능을 검토합니다.

* [Speech-to-Text REST API](rest-speech-to-text.md)
* REST API v3용 [Swagger 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* Batch Transcription을 수행하는 샘플 코드는 `samples/batch` 하위 디렉터리의 [GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples)를 참조하세요.
