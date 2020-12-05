---
title: V 2에서 v3 REST API로 마이그레이션-Speech service
titleSuffix: Azure Cognitive Services
description: V2와 비교할 때 새 API 버전 v3에는 더 작은 주요 변경 내용 집합이 포함 되어 있습니다. 이 문서는 새로운 주 버전으로 마이그레이션하는 데 도움이 됩니다.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737994"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>V 2.0에서 음성 3.0으로의 마이그레이션-텍스트 REST API

V3 버전의 음성 REST API은 안정성 및 사용 편의성을 고려 하 여 이전 API 버전 보다 향상 되었습니다. API 레이아웃은 다른 Azure 또는 Cognitive Services API에 더 가깝게 정렬 됩니다. 그러면 speech API를 사용할 때 기존 기술을 적용 하는 데 도움이 됩니다.

API 개요는 [Swagger 문서로](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)사용할 수 있습니다. 이는 API 개요를 제공 하 고 새 API를 테스트 하는 데 적합 합니다.

C # 및 Python에 대 한 샘플을 제공 하 고 있습니다. Batch의 경우 하위 디렉터리 내의 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) 샘플을 찾을 수 있습니다 `samples/batch` .

## <a name="forward-compatibility"></a>이후 버전과의 호환성

V3로의 원활한 마이그레이션을 위해 v2의 모든 엔터티를 동일한 id의 v3 API 에서도 찾을 수 있습니다. 결과 스키마 변경 (예: 기록)이 있으면 v3 버전의 API에 대 한 응답이 v3 스키마에 표시 됩니다. V2 버전의 API에서 GET을 수행 하는 경우 결과 스키마는 v2 형식이 됩니다. V3에서 새로 만든 엔터티 **는** v 2에서 사용할 수 없습니다.

## <a name="breaking-changes"></a>주요 변경 내용

주요 변경 내용 목록이 조정에 필요한 변경의 크기를 기준으로 정렬 되었습니다. 호출 코드에 사소한 변경이 필요한 몇 가지 변경 내용이 있습니다. 대부분의 변경 내용에는 단순한 이름 바꾸기가 필요 합니다. 팀이 v2에서 v3로 마이그레이션하는 데 걸린 시간은 몇 시간에서 며칠까지 다양 합니다. 그러나 안정성, 간소화 된 코드, 신속한 응답으로 인해 투자를 신속 하 게 오프셋할 경우의 이점이 있습니다. 

### <a name="host-name-changes"></a>호스트 이름 변경

호스트 이름이 {region} (으)로 변경 되었습니다. cris ai는 {region}. api-version. 이 변경에서 경로는 호스트 이름에 포함 되어 있으므로 더 이상 "api/"를 포함 하지 않습니다. 영역 및 경로에 대 한 전체 설명은 [Swagger 문서](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) 를 참조 하세요.

### <a name="identity-of-an-entity"></a>엔터티 id

속성이 `id` 로 대체 되었습니다 `self` . V2에서 API 사용자는 API의 경로를 만드는 방법을 알고 있어야 했습니다. 이는 확장 가능 하지 않으며 사용자의 불필요 한 작업이 필요 합니다. 속성 `id` (uuid)은 `self` 엔터티 (URL)의 위치인 (string)로 대체 됩니다. 이 값은 모든 엔터티 사이에서 고유 합니다. `id`를 코드에 문자열로 저장 하는 경우 단순한 이름 바꾸기가 새 스키마를 지원 하기에 충분 합니다. 이제 `self` 엔터티 (GET, PATCH, DELETE)에 대 한 모든 REST 호출에 대 한 URL로 콘텐츠를 사용할 수 있습니다.

엔터티에 다른 경로에서 사용할 수 있는 추가 기능이 있는 경우 아래에 나열 됩니다 `links` . 좋은 예로는 기록 내용에 대 한 별도의 방법이 포함 된 기록을 들 수 `GET` 있습니다.

v2 기록:

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

v3 기록:

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

클라이언트 구현에 따라 속성 이름을 변경 하는 데 충분 하지 않을 수 있습니다. `self` `links` 클라이언트에서 경로를 생성 하지 않고 및의 반환 된 값을 rest 호출의 대상 url로 사용 하는 방법을 활용 하는 것이 좋습니다. 반환 된 Url을 사용 하 여 나중에 경로를 변경 해도 클라이언트 코드가 중단 되지 않도록 할 수 있습니다.

### <a name="working-with-collections-of-entities"></a>엔터티 컬렉션 작업

이전에 v2 API는 응답에서 사용 가능한 모든 엔터티를 반환 했습니다. 예상 응답 크기를 보다 세밀 하 게 제어할 수 있도록 하기 위해 v3에서 컬렉션의 모든 응답에 대 한 페이지가 매겨집니다. 반환 된 엔터티 수와 페이지 오프셋을 제어할 수 있습니다. 이 동작은 응답 프로세서의 런타임을 쉽게 예측할 수 있도록 하 고 다른 Azure Api와 일치 합니다.

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

속성에는 `values` 사용 가능한 컬렉션 엔터티의 하위 집합이 포함 되어 있습니다. Count 및 offset은 쿼리 매개 변수 및를 사용 하 여 제어할 수 있습니다 `skip` `top` . `@nextLink`가 null이 아닌 경우에는 더 많은 데이터를 사용할 수 있으며 가져오기를 수행 하 여 다음 데이터 일괄 처리를 검색할 수 있습니다 `$.@nextLink` .

이렇게 변경 하려면 `GET` 모든 요소가 반환 될 때까지 루프의 컬렉션에 대해를 호출 해야 합니다.

### <a name="creating-transcriptions"></a>기록 만들기

기록을 만드는 방법에 대 한 자세한 설명은 [배치](./batch-transcription.md)기록에서 찾을 수 있습니다.

특정 기록 옵션을 명시적으로 설정할 수 있도록 v3의 생성이 변경 되었습니다. 이제 속성에서 모든 (옵션) 구성 속성을 설정할 수 있습니다 `properties` .
또한 버전 v3은 이제 여러 입력 파일을 지원 하므로 v2에서 요구 하는 단일 URL이 아닌 Url 목록이 필요 합니다. 속성 이름의 이름이에서로 변경 `recordingsUrl` 되었습니다 `contentUrls` . 감정에서의 분석을 분석 하는 기능은 v3에서 제거 되었습니다. Microsoft 인지 서비스 [텍스트 분석](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 을 대신 사용 하는 것이 좋습니다.

의 새 속성 `timeToLive` 은 `properties` 완료 된 기존 엔터티를 정리 하는 데 도움이 될 수 있습니다. 는 `timeToLive` 완료 된 엔터티가 자동으로 삭제 되는 기간을 지정 합니다. `PT12H`엔터티를 지속적으로 추적, 사용 및 삭제 하는 경우에는 높은 값 (예:)으로 설정 합니다. 따라서 일반적으로 12 시간이 경과 하기 전에 긴 시간을 처리 합니다.

v2 기록 게시 요청 본문:

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

v3 기록 게시 요청 본문:

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

### <a name="format-of-v3-transcription-results"></a>V3 기록 결과의 형식

기록 결과의 스키마는 실시간 끝점에서 생성 된 것과 일치 하도록 약간 변경 되었습니다. 새 형식에 대 한 자세한 설명은 [배치](./batch-transcription.md)기록에서 찾을 수 있습니다. 결과의 스키마는의 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) 게시 됩니다 `samples/batch/transcriptionresult_v3.schema.json` .

이제 속성 이름은 카멜식 대/소문자를 사용 하 고 채널 및 스피커의 값은 정수 유형을 사용 합니다. 기간 형식을 다른 Azure Api와 맞추려면 ISO 8601에 설명 된 대로 형식이 지정 되어 있습니다.

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

### <a name="getting-the-content-of-entities-and-the-results"></a>엔터티 및 결과의 콘텐츠 가져오기

V 2에서 입력 또는 결과 파일에 대 한 링크는 나머지 엔터티 메타 데이터로 인라인 되었습니다. V3의 향상 된 기능으로, GET에 의해 반환 되는 엔터티 메타 데이터 `$.self` 와 결과 파일에 액세스 하기 위한 세부 정보 및 자격 증명을 명확 하 게 구분 합니다. 이러한 분리를 통해 고객 데이터를 보호 하 고 자격 증명의 유효 기간을 세밀 하 게 제어할 수 있습니다.

V3에는 `files` 엔터티가 데이터를 노출 하는 경우 (데이터 집합, 데이터 집합, 기록, 끝점, 평가) 링크 아래에 라는 속성이 있습니다. GET on `$.links.files` 은 파일 및 SAS URL 목록을 반환 하 여 각 파일의 내용에 액세스 합니다. SAS Url의 유효 기간을 제어 하기 위해 쿼리 매개 변수를 `sasValidityInSeconds` 사용 하 여 수명을 지정할 수 있습니다.

v2 기록:

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

v3 기록:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

그러면에 대 한 GET이 `$.links.files` 다음과 같이 발생 합니다.

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

는 `kind` 파일의 콘텐츠 형식을 나타냅니다. 이러한 파일의 경우 종류의 파일은 작업 `TranscriptionReport` 의 요약 이며 종류의 파일은 `Transcription` 작업 자체의 결과입니다.

### <a name="customizing-models"></a>모델 사용자 지정

V3 이전에는 모델을 학습 하는 동안 "음향 모델"과 "언어 모델"을 구분 했습니다. 이러한 차이 때문에 끝점을 만들 때 여러 모델을 지정 해야 합니다. 호출자에 대 한이 프로세스를 간소화 하기 위해 모델 학습에 사용 되는 데이터 집합의 콘텐츠에 따라 차이가 제거 됩니다. 이러한 변경으로 모델 만들기는 이제 혼합 된 데이터 집합 (언어 데이터 및 음향 데이터)을 지원 합니다. 이제 끝점과 온라인 스크립트가 하나의 모델만 필요 합니다.

이러한 변경으로 인해 게시물의에 대 한 요구 사항이 `kind` 제거 되 고에는 `datasets[]` 동일한 또는 혼합 종류의 여러 데이터 집합이 포함 될 수 있습니다.

학습 된 모델의 결과를 개선 하기 위해 음향 데이터는 언어 학습을 위해 내부적으로 자동으로 사용 됩니다. 일반적으로 v3 API를 통해 생성 된 모델은 v2 API를 사용 하 여 만든 모델 보다 더 정확한 결과를 제공 합니다.

### <a name="retrieving-base-and-custom-models"></a>기본 모델 및 사용자 지정 모델 검색

사용 가능한 모델을 단순화 하기 위해 v3은 "사용자 지정 모델" 소유의 고객 으로부터 "기본 모델"의 컬렉션을 분리 했습니다. 이제 두 개의 경로가 `GET /speechtotext/v3.0/models/base` 및 `GET /speechtotext/v3.0/models/` 입니다.

이전에는 모든 모델이 단일 응답으로 함께 반환 되었습니다.

### <a name="name-of-an-entity"></a>엔터티 이름

속성 이름이 `name` 로 바뀝니다 `displayName` . 이는 다른 Azure Api에 정렬 되어 id 속성을 나타내지 않습니다. 이 속성의 값은 고유 하지 않아야 하 고를 사용 하 여 엔터티를 만든 후 변경할 수 있습니다 `PATCH` .

v2 기록:

```json
{
    "name": "Transcription using locale en-US"
}
```

v3 기록:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>참조 된 엔터티 액세스

V2에서 참조 된 엔터티는 사용 된 끝점의 모델과 같이 항상 인라인 되어 있습니다. 엔터티 중첩으로 인해 많은 응답과 소비자가 중첩 된 콘텐츠를 거의 사용 하지 않았습니다. 모든 API 사용자의 응답 크기를 줄이고 성능을 향상 시키기 위해 참조 된 엔터티는 더 이상 응답에서 인라인 되지 않습니다. 대신 다른 엔터티에 대 한 참조를 사용 하 여 직접 사용할 수 있습니다. 이 참조는 `GET` 링크와 동일한 패턴을 따라 후속 (URL도)에 사용할 수 있습니다 `self` .

v2 기록:

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

v3 기록:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

위의 예제와 같이 참조 된 모델의 세부 정보를 사용 해야 하는 경우에는 GET을 사용 하 여 실행 하는 것이 간단해 집니다 `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>끝점 로그 검색

서비스의 버전 v2가 끝점의 응답 로깅을 지원 합니다. V 2를 사용 하 여 끝점의 결과를 검색 하려면 시간 범위로 정의 된 결과의 스냅숏을 나타내는 "데이터 내보내기"를 만들어야 했습니다. 데이터 일괄 처리를 내보내는 프로세스는 유연성이 떨어질 수 있습니다. V3 API는 각 개별 파일에 대 한 액세스를 제공 하 고 반복할 수 있도록 합니다.

V3 끝점을 실행 했습니다.

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

GET 응답 `$.links.logs` :

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

끝점 로그의 페이지 매김은 오프셋을 지정할 수 없는 경우를 제외 하 고 다른 모든 컬렉션과 유사 하 게 작동 합니다. 사용 가능한 데이터의 양이 많기 때문에 서버 기반 페이지 매김을 구현 해야 했습니다.

V3에서 각 끝점 로그는 파일의에서 삭제를 실행 `self` 하거나 delete on을 사용 하 여 개별적으로 삭제할 수 있습니다 `$.links.logs` . 최종 데이터를 지정 하기 위해 쿼리 매개 변수를 `endDate` 요청에 추가할 수 있습니다.

### <a name="using-custom-properties"></a>"Custom" 속성 사용

선택적 구성 속성의 사용자 지정 속성을 구분 하기 위해 모든 명시적으로 명명 된 속성이 이제 `properties` 속성에 있고 호출자가 정의한 모든 속성이 이제 속성에 있습니다 `customProperties` .

v2 기록 엔터티

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3 기록 엔터티

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

이 변경으로 인해의 명시적으로 명명 된 모든 속성 `properties` (예: 문자열 대신 bool)에 올바른 형식의 사용이 설정 됩니다.

### <a name="response-headers"></a>응답 헤더

v3은 `Operation-Location` POST 요청에 헤더를 추가 하 여 더 이상 헤더를 반환 하지 않습니다 `Location` . 정확히 동일 하 게 사용 되는 두 헤더의 값입니다. 이제만 `Location` 이 반환 됩니다.

새 API 버전은 이제 APIM (Azure API management)에 의해 관리 되기 때문에 제한 관련 헤더 `X-RateLimit-Limit` , `X-RateLimit-Remaining` 및는 `X-RateLimit-Reset` 응답 헤더에 포함 되지 않습니다.

### <a name="accuracy-tests"></a>정확도 테스트

새 이름이 더 나은 결과를 설명 하기 때문에 정확도 테스트는 평가로 이름이 변경 되었습니다. 뉴스 경로는 예를 들어 "https://{region}. api-version/speechtotext/v 3.0/평가용"입니다.
