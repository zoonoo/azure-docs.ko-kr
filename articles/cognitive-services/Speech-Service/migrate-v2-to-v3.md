---
title: V 2에서 v3 REST API로 마이그레이션-Speech service
titleSuffix: Azure Cognitive Services
description: 이 문서는 개발자가 음성 서비스 음성-텍스트 REST API에서 v 2의 코드를 v3로 마이그레이션하는 데 도움이 됩니다.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: e9e5db87f983c5db59715eb8b6a9561acf5fad14
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630618"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>REST API v 2.0에서 v 3.0으로 코드 마이그레이션

V 2와 비교 하 여, 음성-텍스트에 대 한 음성 REST API 서비스의 v3 버전은 보다 안정적이 고, 사용 하기 쉬우며, 유사한 서비스의 Api와 더 일치 합니다. 대부분의 팀은 1 일 또는 2 일에 v2에서 v3로 마이그레이션할 수 있습니다.

## <a name="forward-compatibility"></a>이후 버전과의 호환성

V2의 모든 엔터티는 동일한 id의 v3 API 에서도 찾을 수 있습니다. 결과의 스키마가 변경 된 경우 (예를 들어, 예를 들어,) v3 버전의 API에 있는 GET 결과는 v3 스키마를 사용 합니다. V2 버전의 API에서 GET 결과는 동일한 v2 스키마를 사용 합니다. V3에서 새로 만든 엔터티는 v2 Api의 결과에서 사용할 수 **없습니다** .

## <a name="breaking-changes"></a>주요 변경 내용

주요 변경 내용 목록이 조정에 필요한 변경의 크기를 기준으로 정렬 되었습니다. 몇 가지 변경 내용 으로만 호출 코드에서 중요 한 변경 내용이 필요 하지 않습니다. 대부분의 변경 내용에는 항목 이름을 변경 해야 합니다.

### <a name="host-name-changes"></a>호스트 이름 변경

끝점 호스트 이름이에서로 변경 `{region}.cris.ai` 되었습니다 `{region}.api.cognitive.microsoft.com` . 새 끝점의 경로는 `api/` 호스트 이름에 포함 되기 때문에 더 이상 포함 되지 않습니다. [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) 에는 유효한 지역과 경로가 나열 됩니다.
>[!IMPORTANT]
>호스트 이름을에서로 변경 합니다 `{region}.cris.ai` `{region}.api.cognitive.microsoft.com` . 여기서 region은 음성 구독의 지역입니다. 또한 `api/` 클라이언트 코드의 모든 경로에서을 제거 합니다.

### <a name="identity-of-an-entity"></a>엔터티 id

속성은 `id` 이제 `self` 입니다. V2에서 API 사용자는 API의 경로를 만드는 방법을 알고 있어야 했습니다. 이는 확장 가능 하지 않으며 사용자의 불필요 한 작업이 필요 합니다. 속성 `id` (uuid)은 `self` 엔터티 (URL)의 위치인 (string)로 대체 됩니다. 이 값은 모든 엔터티 사이에서 고유 합니다. `id`를 코드에 문자열로 저장 하는 경우 새 스키마를 지원 하기에는 이름 바꾸기가 충분 합니다. 이제 `self` `GET` `PATCH` 엔터티에 대 한, 및 REST 호출에 대 한 URL로 콘텐츠를 사용할 수 있습니다 `DELETE` .

엔터티에 다른 경로를 통해 사용할 수 있는 추가 기능이 있는 경우 아래에 나열 됩니다 `links` . 다음 기록 예제에서는 기록 내용에 대 한 별도의 메서드를 보여 줍니다 `GET` .
>[!IMPORTANT]
>`id`클라이언트 코드에서 속성의 이름을로 바꿉니다 `self` . 필요한 경우 형식을에서 `uuid` 로 변경 `string` 합니다. 

**v2 기록:**

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

**v3 기록:**

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

코드의 구현에 따라 속성의 이름을 변경 하는 데 충분 하지 않을 수 있습니다. 반환 된 `self` 및 `links` 값을 클라이언트에서 경로를 생성 하는 대신 REST 호출의 대상 url로 사용 하는 것이 좋습니다. 반환 된 Url을 사용 하 여 나중에 경로를 변경 해도 클라이언트 코드가 중단 되지 않도록 할 수 있습니다.

### <a name="working-with-collections-of-entities"></a>엔터티 컬렉션 작업

이전에 v2 API는 사용 가능한 모든 엔터티를 결과로 반환 했습니다. V3에서 예상 응답 크기를 보다 세밀 하 게 제어할 수 있도록 모든 컬렉션 결과의 페이지가 매겨집니다. 반환 된 엔터티 수와 페이지의 시작 오프셋을 제어할 수 있습니다. 이 동작을 통해 응답 프로세서의 런타임을 쉽게 예측할 수 있습니다.

응답의 기본 모양은 모든 컬렉션에 대해 동일 합니다.

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

속성에는 `values` 사용 가능한 컬렉션 엔터티의 하위 집합이 포함 되어 있습니다. 개수와 오프셋은 `skip` 및 쿼리 매개 변수를 사용 하 여 제어할 수 있습니다 `top` . `@nextLink`이이 아닌 경우 `null` 에는를 사용 하 여 데이터의 다음 일괄 처리를 검색할 수 있습니다 `$.@nextLink` .

이렇게 변경 하려면 `GET` 모든 요소가 반환 될 때까지 루프의 컬렉션에 대해를 호출 해야 합니다.

>[!IMPORTANT]
>GET의 응답이 `speechtotext/v3.0/{collection}` 에 값을 포함 하는 경우 `$.@nextLink` `GETs` `$.@nextLink` `$.@nextLink` 이 컬렉션의 모든 요소를 검색 하도록 설정 되지 않을 때까지를 계속 실행 합니다.

### <a name="creating-transcriptions"></a>기록 만들기

대량 배치를 만드는 방법에 대 한 자세한 설명은 [배치 기록 방법을](./batch-transcription.md)참조 하세요.

V3 기록 API를 사용 하면 특정 기록 옵션을 명시적으로 설정할 수 있습니다. 이제 속성에서 모든 (옵션) 구성 속성을 설정할 수 있습니다 `properties` .
또한 버전 v3은 여러 입력 파일을 지원 하므로 v2에서 단일 URL이 아닌 Url 목록이 필요 합니다. V2 속성 이름은 `recordingsUrl` 이제 v3로 되어 `contentUrls` 있습니다. 감정에서의 분석을 분석 하는 기능은 v3에서 제거 되었습니다. 감정 분석 옵션에 대 한 Microsoft 인식 서비스 [텍스트 분석](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 을 참조 하세요.

의 새 속성 `timeToLive` 은 `properties` 완료 된 기존 엔터티를 정리 하는 데 도움이 됩니다. 는 `timeToLive` 완료 된 엔터티를 자동으로 삭제 하는 기간을 지정 합니다. `PT12H`엔터티를 지속적으로 추적, 사용 및 삭제 하 여 12 시간이 경과 하기 전에 일반적으로 처리 하는 경우에는이 값을 높은 값 (예:)으로 설정 합니다.

**v2 기록 게시 요청 본문:**

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

**v3 기록 게시 요청 본문:**

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
>속성의 이름을 `recordingsUrl` 로 바꾸고 `contentUrls` 단일 url 대신 url 배열을 전달 합니다. 대신 또는에 대 한 설정을 전달 `diarizationEnabled` `wordLevelTimestampsEnabled` `bool` `string` 합니다.

### <a name="format-of-v3-transcription-results"></a>V3 기록 결과의 형식

기록 결과의 스키마가 실시간 끝점에서 생성 된 것과 일치 하도록 약간 변경 되었습니다. [일괄 처리](./batch-transcription.md)기록에서 새 형식에 대 한 자세한 설명을 찾습니다. 결과의 스키마는의 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) 게시 됩니다 `samples/batch/transcriptionresult_v3.schema.json` .

이제 속성 이름은 카멜식 대/소문자를 사용 하 고 및에 대 한 값은 `channel` `speaker` 정수 형식을 사용 합니다. 이제 기간 형식에는 ISO 8601에 설명 된 구조가 사용 됩니다 .이 구조는 다른 Azure Api에서 사용 되는 기간 서식과 일치 합니다.

V3 기록 결과의 샘플입니다. 차이점은 주석에 설명 되어 있습니다.

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
>위와 같이 기록 결과를 새 형식으로 Deserialize 합니다. `channel`에서 각 요소에 대 한 속성 값을 확인 하 여 오디오 채널 당 단일 파일 대신 채널을 구분 `recognizedPhrases` 합니다. 이제 각 입력 파일에 대 한 단일 결과 파일이 있습니다.


### <a name="getting-the-content-of-entities-and-the-results"></a>엔터티 및 결과의 콘텐츠 가져오기

V 2에서 입력 또는 결과 파일에 대 한 링크는 나머지 엔터티 메타 데이터로 인라인 되었습니다. V3의 향상 된 기능으로, GET에 의해 반환 되는 엔터티 메타 데이터 `$.self` 와 결과 파일에 액세스 하기 위한 세부 정보 및 자격 증명을 명확히 구분 합니다. 이러한 분리를 통해 고객 데이터를 보호 하 고 자격 증명의 유효 기간을 세밀 하 게 제어할 수 있습니다.

V3에 `links` `files` 는 엔터티가 데이터를 노출 하는 경우에 호출 되는 하위 속성 (데이터 집합, 데이터 집합, 최종 스크립트가 포함, 끝점 또는 평가)이 포함 됩니다. GET on `$.links.files` 은 파일 목록과 SAS URL을 반환 하 여 각 파일의 내용에 액세스 합니다. SAS Url의 유효 기간을 제어 하기 위해 쿼리 매개 변수를 `sasValidityInSeconds` 사용 하 여 수명을 지정할 수 있습니다.

**v2 기록:**

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

**v3 기록:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**에 대 한 GET의 `$.links.files` 결과는 다음과 같습니다.**

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

`kind`속성은 파일의 콘텐츠 형식을 나타냅니다. 이러한 파일의 경우 종류의 파일은 작업 `TranscriptionReport` 의 요약 이며 종류의 파일은 `Transcription` 작업 자체의 결과입니다.

>[!IMPORTANT]
>작업 결과를 가져오려면에서를 사용 `GET` `/speechtotext/v3.0/{collection}/{id}/files` `GET` `/speechtotext/v3.0/{collection}/{id}` 합니다 .이 경우에는 또는의 응답에 더 이상 포함 되지 않습니다 `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>모델 사용자 지정

V3 이전에는 모델을 학습 하는 동안 _음향 모델과_ _언어 모델_ 을 구분 했습니다. 이러한 차이 때문에 끝점을 만들 때 여러 모델을 지정 해야 합니다. 호출자에 대 한이 프로세스를 간소화 하기 위해 차이점을 제거 하 고 모든 항목이 모델 학습에 사용 되는 데이터 집합의 내용에 따라 달라 집니다. 이러한 변경으로 모델 만들기는 이제 혼합 된 데이터 집합 (언어 데이터 및 음향 데이터)을 지원 합니다. 이제 끝점과 온라인 스크립트가 하나의 모델만 필요 합니다.

이러한 변경으로 인해 작업에서에 대 한 요구 사항이 `kind` `POST` 제거 되 고 배열에 `datasets[]` 동일한 또는 혼합 종류의 여러 데이터 집합이 포함 될 수 있습니다.

학습 된 모델의 결과를 향상 시키기 위해 음향 데이터는 언어 학습 중에 내부적으로 자동으로 사용 됩니다. 일반적으로 v3 API를 통해 생성 된 모델은 v2 API를 사용 하 여 만든 모델 보다 더 정확한 결과를 제공 합니다.

>[!IMPORTANT]
>어쿠스틱 및 언어 모델 부분을 모두 사용자 지정 하려면 게시물의에 필요한 모든 언어 및 음향 데이터 집합 `datasets[]` 을에 전달 `/speechtotext/v3.0/models` 합니다. 이렇게 하면 사용자 지정 된 두 부분으로 단일 모델이 만들어집니다.

### <a name="retrieving-base-and-custom-models"></a>기본 모델 및 사용자 지정 모델 검색

사용 가능한 모델을 간소화 하기 위해 v3은 "사용자 지정 모델" 소유의 고객 으로부터 "기본 모델"의 컬렉션을 분리 했습니다. 이제 두 개의 경로가 `GET /speechtotext/v3.0/models/base` 및 `GET /speechtotext/v3.0/models/` 입니다.

V2에서는 모든 모델이 단일 응답으로 함께 반환 되었습니다.

>[!IMPORTANT]
>사용자 지정을 위해 제공 된 기본 모델 목록을 가져오려면에서를 `GET` 사용 `/speechtotext/v3.0/models/base` 합니다. 에서를 사용 하 여 고유한 사용자 지정 모델을 찾을 수 있습니다 `GET` `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>엔터티 이름

`name`속성은 이제 `displayName` 입니다. 이는 다른 Azure Api와 일치 하 여 id 속성을 나타내지 않습니다. 이 속성의 값은 고유 하지 않아야 하 고 작업을 사용 하 여 엔터티를 만든 후 변경할 수 있습니다 `PATCH` .

**v2 기록:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 기록:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>`name`클라이언트 코드에서 속성의 이름을로 바꿉니다 `displayName` .

### <a name="accessing-referenced-entities"></a>참조 된 엔터티 액세스

V 2에서 참조 된 엔터티는 사용 된 끝점의 모델과 같이 항상 인라인 되었습니다. 엔터티 중첩으로 인해 많은 응답과 소비자가 중첩 된 콘텐츠를 거의 사용 하지 않았습니다. 응답 크기를 줄이고 성능을 향상 시키기 위해 참조 된 엔터티는 더 이상 응답에서 인라인 되지 않습니다. 대신, 다른 엔터티에 대 한 참조가 표시 되 고, `GET` 링크와 같은 패턴에 따라 후속 (URL도)에 대해 직접 사용할 수 있습니다 `self` .

**v2 기록:**

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

**v3 기록:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

위의 예제와 같이 참조 된 모델의 세부 정보를 사용 해야 하는 경우에는에 대 한 GET을 실행 하면 `$.model.self` 됩니다.

>[!IMPORTANT]
>참조 된 엔터티의 메타 데이터를 검색 하려면 on을 `$.{referencedEntity}.self` 실행 합니다. 예를 들어에 대해를 수행 하는 기록의 모델을 검색 합니다 `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>끝점 로그 검색

서비스의 버전 v2에서 끝점 결과 로깅을 지원 합니다. V 2를 사용 하 여 끝점의 결과를 검색 하려면 시간 범위로 정의 된 결과의 스냅숏을 나타내는 "데이터 내보내기"를 만듭니다. 데이터 일괄 처리를 내보내는 프로세스는 유연성이 없습니다. V3 API는 각 개별 파일에 대 한 액세스를 제공 하 고 반복할 수 있도록 합니다.

**V3 끝점을 실행 했습니다.**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET 응답 `$.links.logs` :**

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

끝점 로그의 페이지 매김은 오프셋을 지정할 수 없는 경우를 제외 하 고 다른 모든 컬렉션과 유사 하 게 작동 합니다. 사용 가능한 데이터의 양이 많기 때문에 페이지 매김을 서버에 의해 결정 됩니다.

V3에서 각 끝점 로그는 파일의에서 작업을 실행 하거나를 사용 하 여 개별적으로 삭제할 수 있습니다 `DELETE` `self` `DELETE` `$.links.logs` . 종료 날짜를 지정 하기 위해 쿼리 매개 변수를 `endDate` 요청에 추가할 수 있습니다.

>[!IMPORTANT]
>로그 `/api/speechtotext/v2.0/endpoints/{id}/data` 파일에 개별적으로 액세스 하는 데 사용 하는 로그 내보내기를 만드는 대신 `/v3.0/endpoints/{id}/files/logs/` 

### <a name="using-custom-properties"></a>사용자 지정 속성 사용

선택적 구성 속성의 사용자 지정 속성을 구분 하기 위해 모든 명시적으로 명명 된 속성이 이제 `properties` 속성에 있고 호출자가 정의한 모든 속성이 이제 속성에 있습니다 `customProperties` .

**v2 기록 엔터티:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 기록 엔터티:**

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

이 변경으로 인해에서 명시적으로 명명 된 모든 속성 `properties` (예: 문자열 대신 부울)에 올바른 형식을 사용할 수도 있습니다.

>[!IMPORTANT]
>모든 사용자 지정 속성을 `customProperties` 요청에서 대신으로 전달 `properties` `POST` 합니다.

### <a name="response-headers"></a>응답 헤더

v3은 `Operation-Location` 요청에 헤더를 추가 하 여 더 이상 헤더를 반환 하지 않습니다 `Location` `POST` . V 2의 두 헤더 값은 동일 합니다. 이제만 `Location` 반환 됩니다.

새 API 버전은 이제 APIM (Azure API management)에 의해 관리 되기 때문에 제한 관련 헤더 `X-RateLimit-Limit` , `X-RateLimit-Remaining` 및는 `X-RateLimit-Reset` 응답 헤더에 포함 되지 않습니다.

>[!IMPORTANT]
>대신 응답 헤더에서 위치를 읽습니다 `Location` `Operation-Location` . 429 응답 코드의 경우 `Retry-After` , 또는 대신 헤더 값을 읽습니다 `X-RateLimit-Limit` `X-RateLimit-Remaining` `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>정확도 테스트

새 이름이 더 나은 결과를 설명 하기 때문에 정확도 테스트는 평가로 이름이 변경 되었습니다. 새 경로는입니다. `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations`

>[!IMPORTANT]
>`accuracytests`클라이언트 코드에서 경로 세그먼트의 이름을로 바꿉니다 `evaluations` .


## <a name="next-steps"></a>다음 단계

Speech services에서 제공 하는 다음과 같은 일반적으로 사용 되는 REST Api의 모든 기능을 검사 합니다.

* [Speech-to-Text REST API](rest-speech-to-text.md)
* REST API v3의 [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* 일괄 처리를 수행 하는 샘플 코드는 하위 디렉터리에서 [GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples) 를 참조 `samples/batch` 하세요.
