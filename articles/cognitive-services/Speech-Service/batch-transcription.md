---
title: 배치 전사란 무엇인가 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: Batch Transcription은 Azure Blob과 같이 스토리지에 많은 양의 오디오를 전사하려는 경우에 이상적입니다. 전용 REST API를 사용하면 SAS(공유 액세스 서명) URI가 있는 오디오 파일을 가리키고 비동기식으로 전사를 수신할 수 있습니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 1f88df186526c2f9903337bb3331940be0989c3d
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892464"
---
# <a name="what-is-batch-transcription"></a>일괄 전사란 무엇입니까?

일괄 처리 전사는 저장 저장소에 많은 양의 오디오를 전사할 수 있는 REST API 작업 집합입니다. 공유 액세스 서명(SAS) URI가 있는 오디오 파일을 가리키고 비동기적으로 전사 결과를 받을 수 있습니다.

비동기 음성-텍스트 전사는 기능 중 하나일 뿐입니다. 일괄 전사 REST API를 사용하여 다음 방법을 호출할 수 있습니다.



|    배치 전사 작업                                             |    방법    |    REST API 호출                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    새 전사를 만듭니다.                                              |    POST      |    api/스피치토텍스트/v2.0/전사            |
|    인증된 구독에 대한 전사 목록을 검색합니다.    |    GET       |    api/스피치토텍스트/v2.0/전사            |
|    오프라인 전사에 대해 지원되는 로캘 목록을 가져옵니다.              |    GET       |    api/스피치토텍스트/v2.0/전사/로캘    |
|    ID로 식별된 전사의 가변 세부 정보를 업데이트합니다.    |    패치     |    api/스피치토텍스트/v2.0/전사/{id}       |
|    지정된 전사 작업을 삭제합니다.                                 |    Delete    |    api/스피치토텍스트/v2.0/전사/{id}       |
|    지정된 ID로 식별된 전사를 가져옵니다.                        |    GET       |    api/스피치토텍스트/v2.0/전사/{id}       |




제목 `Custom Speech transcriptions`아래에 [Swagger 문서로](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)사용할 수 있는 자세한 API를 검토하고 테스트할 수 있습니다.

일괄 전사 작업은 최선의 노력을 기울여 예약됩니다. 현재 작업이 실행 상태로 변경되는 시기에 대한 예측은 없습니다. 정상적인 시스템 부하에서는 몇 분 내에 발생합니다. 실행 중인 상태에서 실제 전사는 오디오 실시간보다 빠르게 처리됩니다.

사용하기 쉬운 API 옆에는 사용자 지정 끝점을 배포할 필요가 없으며 관찰할 동시성 요구 사항이 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="subscription-key"></a>구독 키

Speech Service의 모든 기능과 마찬가지로, [시작 가이드](get-started.md)에 따라 [Azure Portal](https://portal.azure.com)에서 구독 키를 만듭니다.

>[!NOTE]
> 일괄 전사를 사용하려면 음성 서비스에 대한 표준 구독(S0)이 필요합니다. 무료 구독 키(F0)가 작동하지 않습니다. 자세한 내용은 [가격 및 한도를](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)참조하십시오.

### <a name="custom-models"></a>사용자 지정 모델

어쿠스틱 또는 언어 모델을 사용자 지정하려는 경우 [어쿠스틱 모델 사용자 지정](how-to-customize-acoustic-models.md) 및 [사용자 지정 언어 모델 디자인의](how-to-customize-language-model.md)단계를 따릅니다. 생성된 모델을 일괄 전사로 사용하려면 해당 모델 의 모델 이 필요합니다. 모델의 세부 정보를 검사할 때 모델 ID를 검색할 수 있습니다. 배치 전사 서비스에 배포된 사용자 지정 끝점이 필요하지 않습니다.

## <a name="the-batch-transcription-api"></a>Batch Transcription API

### <a name="supported-formats"></a>지원되는 형식

Batch Transcription API에서 지원하는 형식은 다음과 같습니다.

| 형식 | Codec | Bitrate | 샘플링 주기                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |
| MP3    | PCM   | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |
| OGG    | OPUS  | 16비트  | 8kHz 또는 16kHz, 모노 또는 스테레오 |

스테레오 오디오 스트림의 경우 전사 중에 왼쪽 및 오른쪽 채널이 분할됩니다. 각 채널에 대해 JSON 결과 파일이 생성되고 있습니다. 발언당 생성된 타임스탬프를 통해 개발자는 정렬된 최종 자막을 만들 수 있습니다.

### <a name="configuration"></a>구성

구성 매개 변수는 JSON으로 제공됩니다.

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>구성 속성

이러한 선택적 속성을 사용하여 전사를 구성합니다.

:::row:::
   :::column span="1":::
      **매개 변수**
   :::column-end:::
   :::column span="2":::
      **설명**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      인식 결과에서 욕설의 처리 방법을 지정합니다. 허용된 `None` 값은 욕설 필터링을 `Masked` 사용하지 않도록 설정하거나, 욕설을 `Removed` 별표로 바꾸거나, 결과에서 `Tags` 모든 욕설을 제거하거나, "욕설" 태그를 추가하는 것입니다. 기본 설정은 `Masked`입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      인식 결과에서 문장 부호의 처리 방법을 지정합니다. 허용된 `None` 값은 문장 부호를 사용하지 않도록 설정하거나, `Dictated` 명시적(음성) 구두점을 암시하거나, `Automatic` 디코더가 구두점을 처리하도록 하거나, `DictatedAndAutomatic` 받아쓰기 및 자동 문장 부호를 사용하는 것입니다. 기본 설정은 `DictatedAndAutomatic`입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      단어 수준 타임스탬프를 출력에 추가할지 여부를 지정합니다. 허용된 `true` 값은 단어 수준 타임스탬프를 활성화하고(기본값) `false` 비활성화할 수 있도록 하는 것입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      감정 분석을 발화에 적용할지 지정합니다. 허용된 `true` 값은 `false` 활성화하고(기본값) 비활성화하는 것입니다. 자세한 내용은 [감정 분석을](#sentiment-analysis) 참조하십시오.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      두 개의 음성을 포함하는 모노 채널이 될 것으로 예상되는 입력에서 이화 분석을 수행해야 함을 지정합니다. 허용된 `true` 값은 디어화 `false` 및(기본값)을 사용하여 비활성화할 수 있습니다. 또한 true로 설정해야 합니다. `AddWordLevelTimestamps`
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Azure에서 쓰기 가능한 컨테이너에 [대한 서비스 SAS가](../../storage/common/storage-sas-overview.md) 있는 선택적 URL입니다. 결과는 이 컨테이너에 저장됩니다.
:::row-end:::

### <a name="storage"></a>스토리지

일괄 전사는 [오디오를](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) 읽고 저장소에 기록기록을 쓰기 위한 Azure Blob 저장소를 지원합니다.

## <a name="the-batch-transcription-result"></a>일괄 전사 결과

모노 입력 오디오의 경우 하나의 전사 결과 파일이 생성됩니다. 스테레오 입력 오디오의 경우 두 개의 전사 결과 파일이 생성됩니다. 각각이 구조는 다음과 있습니다.

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

결과에는 다음 양식이 포함됩니다.

:::row:::
   :::column span="1":::
      **Form**
   :::column-end:::
   :::column span="2":::
      **콘텐츠**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      실제 단어가 인식되었습니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      인식된 텍스트의 역텍스트 정규화 형식입니다. 약어 ("의사 대장장이"에서 "dr smith"), 전화 번호 및 기타 변형이 적용됩니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      욕설 마스킹이 적용된 ITN 양식입니다.
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      인식된 텍스트의 표시 양식입니다. 추가된 문장 부호와 대문자가 포함되어 있습니다.
:::row-end:::

## <a name="speaker-separation-diarization"></a>스피커 분리(표준화)

디아이어리화는 오디오에서 스피커를 분리하는 과정입니다. 당사의 Batch 파이프라인은 디아화를 지원하며 모노 채널 레코딩에서 두 개의 스피커를 인식할 수 있습니다. 스테레오 레코딩에서는 이 기능을 사용할 수 없습니다.

모든 전사 출력에는 `SpeakerId`을 포함합니다. 다이아리화를 사용하지 않으면 JSON 출력에 표시됩니다. `"SpeakerId": null` diarization의 경우 두 개의 음성을 지원하므로 `"2"`스피커가 또는 로 `"1"` 식별됩니다.

diarization을 요청하려면 아래와 같이 HTTP 요청에 관련 매개 변수를 추가하기만 하면 됩니다.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

위의 요청의 매개 변수가 나타내는 것처럼 단어 수준 타임스탬프도 '켜짐'해야 합니다.

## <a name="sentiment-analysis"></a>감정 분석

감정 기능은 오디오에 표현된 감정을 추정합니다. 감정은 에 `Negative` `Neutral`대한 0과 1 사이의 값으로 표현됩니다. `Positive` 예를 들어 감정 분석은 콜 센터 시나리오에서 사용할 수 있습니다.

- 고객 만족도에 대한 통찰력 얻기
- 상담원(통화 팀)의 성과에 대한 통찰력 얻기
- 통화가 음의 방향으로 전환된 정확한 시점을 찾습니다.
- 부정적인 호출을 긍정적 인 방향으로 전환 할 때 잘 된 것
- 고객이 좋아하는 것과 제품 또는 서비스에 대해 싫어하는 것 파악

감정은 어휘 형식에 따라 오디오 세그먼트당 점수가 매겨지습니다. 해당 오디오 세그먼트 내의 전체 텍스트는 감정을 계산하는 데 사용됩니다. 전체 전사에 대해 집계 감정이 계산되지 않습니다. 현재 감정 분석은 영어에서만 사용할 수 있습니다.

> [!NOTE]
> 대신 Microsoft 텍스트 분석 API를 사용하는 것이 좋습니다. 핵심 구 추출, 자동 언어 검색 등과 같은 감정 분석을 넘어 더 많은 고급 기능을 제공합니다. [텍스트 분석 설명서에서](https://azure.microsoft.com/services/cognitive-services/text-analytics/)정보와 샘플을 찾을 수 있습니다.
>

JSON 출력 샘플은 다음과 같습니다.

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>모범 사례

전사 서비스는 제출된 많은 전사를 처리할 수 있습니다. 에 `GET` 대한 전사 [방법을](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)통해 전사 상태를 쿼리할 수 있습니다. `take` 매개 변수(수백 개)를 지정하여 정보를 적절한 크기로 반환합니다. 결과를 검색한 후 서비스에서 정기적으로 [기록을 삭제합니다.](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) 이렇게 하면 전사 관리 호출에서 빠른 응답을 보장합니다.

## <a name="sample-code"></a>예제 코드

전체 샘플은 하위 디렉터리 내부의 [GitHub 샘플 리포지토리에서](https://aka.ms/csspeech/samples) `samples/batch` 사용할 수 있습니다.

사용자 지정 어쿠스틱 또는 언어 모델을 사용하려는 경우 사용자 구독 정보, 서비스 지역, 전사할 오디오 파일을 가리키는 SAS URI 및 모델 ID를 사용하여 샘플 코드를 사용자 지정해야 합니다.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

샘플 코드는 클라이언트를 설정하고 전사 요청을 제출합니다. 그런 다음 상태 정보를 폴링을 하고 전사 진행 에 대한 세부 정보를 인쇄합니다.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

이전 호출에 대한 자세한 내용은 [Swagger 문서](https://westus.cris.ai/swagger/ui/index)를 참조하세요. 여기에 표시된 전체 샘플을 보려면 [GitHub](https://aka.ms/csspeech/samples)의 `samples/batch` 하위 디렉터리로 이동합니다.

오디오를 게시하고 전사 상태를 수신하려면 비동기 설정을 기록해 둡니다. 만든 클라이언트는 .NET HTTP 클라이언트입니다. 오디오 파일 세부 정보를 전송하기 위한 `PostTranscriptions` 메서드 및 결과를 수신하기 위한 `GetTranscriptions` 메서드가 있습니다. `PostTranscriptions`는 핸들을 반환하고, `GetTranscriptions`는 이 핸들을 사용하여 전사 상태를 가져오기 위한 핸들을 만듭니다.

현재 샘플 코드는 사용자 지정 모델을 지정하지 않습니다. 이 서비스는 파일을 전사하기 위해 기준 모델을 사용합니다. 모델을 지정하려면 음향 및 언어 모델에 대해 모델 ID와 동일한 메서드를 전달할 수 있습니다.

> [!NOTE]
> 기준 전사의 경우 기준 모델의 ID를 선언할 필요가 없습니다. 언어 모델 ID(어쿠스틱 모델 ID 아님)만 지정하면 일치하는 어쿠스틱 모델이 자동으로 선택됩니다. 어쿠스틱 모델 ID만 지정하면 일치하는 언어 모델이 자동으로 선택됩니다.

## <a name="download-the-sample"></a>샘플 다운로드

[GitHub 샘플 리포지토리](https://aka.ms/csspeech/samples)의 `samples/batch` 디렉터리에서 샘플을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
