---
title: 웹 후크-음성 서비스
titleSuffix: Azure Cognitive Services
description: 웹 후크는 가져오기, 적응, 정확도 테스트 또는 긴 실행 파일의 경우와 같은 장기 실행 프로세스를 처리할 때 솔루션을 최적화 하는 데 적합 한 HTTP 호출 백업입니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 3d07e540bf88c956f61b5d3b2a98702cad616985
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558797"
---
# <a name="webhooks-for-speech-services"></a>음성 서비스에 대 한 웹 후크

웹 후크는 응용 프로그램이 사용할 수 있게 되 면 음성 서비스의 데이터를 받을 수 있도록 하는 HTTP 콜백과 비슷합니다. 웹 후크를 사용 하 여 지속적으로 응답을 폴링할 필요가 없도록 REST Api의 사용을 최적화할 수 있습니다. 다음 몇 개 섹션에서는 음성 서비스에서 웹 후크를 사용 하는 방법에 대해 알아봅니다.

## <a name="supported-operations"></a>지원되는 작업

음성 서비스는 장기 실행 작업에 대해 웹 후크를 지원 합니다. 아래에 나열 된 각 작업은 완료 될 때 HTTP 콜백을 트리거할 수 있습니다.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

다음으로 webhook를 만들어 보겠습니다.

## <a name="create-a-webhook"></a>웹후크 만들기

오프 라인 기록을 위한 webhook를 만들어 보겠습니다. 시나리오: 사용자에 게 일괄 처리 기록 API를 사용 하 여 비동기적으로 높여줄 하는 장기 실행 오디오 파일이 있습니다.

\<Https://\>cris.ai/api/speechtotext/v2.1/transcriptions/hooks에 대 한 POST 요청을 수행 하 여 웹 후크를 만들 수 있습니다.

요청에 대 한 구성 매개 변수는 JSON으로 제공 됩니다.

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
일괄 처리 기록 API에 대 한 모든 POST 요청 `name`에는가 필요 합니다. 합니다 `description` 고 `properties` 매개 변수는 선택 사항입니다.

`Active` 속성은 webhook 등록을 삭제 하 고 다시 만들 필요 없이 다시 URL에 대 한 호출을 전환 하는 데 사용 됩니다. 프로세스가 완료 된 후에를 한 번만 호출 해야 하는 경우 webhook를 삭제 하 고 `Active` 속성을 false로 전환 합니다.

이벤트 유형은 `TranscriptionCompletion` 이벤트 배열에서 제공 됩니다. 이는 기록을 터미널 상태 (`Succeeded` 또는 `Failed`)로 가져올 때 끝점을 다시 호출 합니다. 등록 된 URL로 다시 호출 하는 경우 요청에 등록 된 `X-MicrosoftSpeechServices-Event` 이벤트 유형 중 하나를 포함 하는 헤더가 포함 됩니다. 등록 된 이벤트 유형별 요청이 하나 있습니다.

구독할 수 없는 하나의 이벤트 유형이 있습니다. `Ping` 이벤트 유형입니다. Ping URL (아래 참조)을 사용 하는 경우 webhook 만들기를 완료 하면이 유형의 요청이 URL로 전송 됩니다.  

구성 `url` 에서 속성이 필요 합니다. POST 요청은이 URL로 전송 됩니다. 는 `secret` 암호를 HMAC 키로 사용 하 여 페이로드의 SHA256 해시를 만드는 데 사용 됩니다. 등록 된 URL을 다시 호출할 `X-MicrosoftSpeechServices-Signature` 때 해시가 헤더로 설정 됩니다. 이 헤더는 b a s e 64로 인코딩됩니다.

이 샘플에서는 다음을 사용 하 여 C#페이로드의 유효성을 검사 하는 방법을 보여 줍니다.

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
이 코드 조각 `secret` 에서는가 디코딩되 고 유효성을 검사 합니다. 또한 웹 후크 이벤트 유형이 전환 되었음을 알 수 있습니다. 현재는 완료 된 기록 당 하나의 이벤트가 있습니다. 이 코드는 각 이벤트에 대해 5 번 (1 초 지연)을 다시 시도 합니다.

### <a name="other-webhook-operations"></a>기타 webhook 작업

등록 된 모든 웹 후크를 가져오려면: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

특정 webhook를 가져오려면 다음을 수행 합니다. GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

특정 webhook 하나를 제거 하려면: DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> 위의 예제에서 지역은 ' westus '입니다. Azure Portal에서 음성 서비스 리소스를 만든 지역으로 바꾸어야 합니다.

본문 https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 게시: 비어 있습니다.

등록 된 URL에 POST 요청을 보냅니다. 요청에 ping 값 `X-MicrosoftSpeechServices-Event` 을 포함 하는 헤더가 포함 되어 있습니다. 암호를 사용 하 여 webhook를 등록 한 경우 암호를 HMAC `X-MicrosoftSpeechServices-Signature` 키로 사용 하 여 페이로드의 SHA256 해시가 포함 된 헤더가 포함 됩니다. 해시는 b a s e 64로 인코딩됩니다.

본문 https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 게시: 비어 있습니다.

구독 된 이벤트 유형 (기록)의 엔터티가 시스템에 있고 적절 한 상태에 있는 경우 등록 된 URL에 POST 요청을 보냅니다. 페이로드는 웹 후크를 호출한 마지막 엔터티에서 생성 됩니다. 엔터티가 없는 경우 POST는 204에 응답 합니다. 테스트 요청을 수행할 수 있는 경우 200를 사용 하 여 응답 합니다. 요청 본문은 웹 후크가 구독 한 특정 엔터티에 대 한 GET 요청에서와 동일한 모양입니다 (예를 들어, 기록). 앞에서 설명한 대로 요청 `X-MicrosoftSpeechServices-Event` 에 `X-MicrosoftSpeechServices-Signature` 및 헤더가 포함 됩니다.

### <a name="run-a-test"></a>테스트 실행

웹 사이트 https://bin.webhookrelay.com 를 사용 하 여 빠른 테스트를 수행할 수 있습니다. 여기에서 문서 앞부분에서 설명한 webhook를 만들기 위해 매개 변수로 전달할 Url을 HTTP POST에 가져올 수 있습니다.

' 버킷 만들기 '를 클릭 하 고 화면의 지시에 따라 후크를 가져옵니다. 그런 다음이 페이지에 제공 된 정보를 사용 하 여 음성 서비스에 후크를 등록 합니다. 릴레이 메시지의 페이로드 – 기록 완료에 대 한 응답으로는 다음과 같습니다.

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
메시지에는 기록을 높여줄 하는 데 사용 되는 기록 URL 및 모델이 포함 되어 있습니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
