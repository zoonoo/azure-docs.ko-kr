---
title: 웹 후크-음성 서비스
titlesuffix: Azure Cognitive Services
description: 웹 후크 HTTP 콜백 장기를 다룰 때 솔루션을 최적화 하기 위한 이상적인는 가져오기, 적응, 정확도 테스트 또는 장기 실행 파일의 기록 같은 프로세스를 실행 합니다.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 7b47d4fc3aa4a1a50e441e668a856703c67045ae
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2019
ms.locfileid: "59581037"
---
# <a name="webhooks-for-speech-services"></a>음성 서비스에 대 한 웹 후크

웹 후크는 사용할 수 있을 때 음성 서비스에서 데이터를 허용 하도록 응용 프로그램을 허용 하는 HTTP 콜백을 비슷합니다. 웹 후크를 사용 하는 응답에 대 한 폴링을 계속 하려면 필요를 없애 REST Api 사용을 최적화할 수 있습니다. 다음 단원에서는 음성 서비스를 사용 하 여 웹 후크를 사용 하는 방법에 알아봅니다.

## <a name="supported-operations"></a>지원되는 작업

음성 서비스는 모든 장기 실행 작업에 대 한 웹 후크를 지원 합니다. 아래 나열 된 작업의 각 완료 되 면 HTTP 콜백을 트리거할 수 있습니다. 

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

다음으로, 웹 후크를 만들어 보겠습니다.

## <a name="create-a-webhook"></a>웹후크 만들기

오프 라인을 기록에 대 한 웹 후크를 만들어 보겠습니다. 시나리오: 사용자는 일괄 처리 기록 API를 사용 하 여 비동기적으로 기록 하 고 싶은 장기 실행 중인 오디오 파일을 포함 합니다. 

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
일괄 처리 기록 API에 대 한 모든 POST 요청 필요는 `name`합니다. 합니다 `description` 고 `properties` 매개 변수는 선택 사항입니다.

`Active` 속성 삭제 및 웹 후크 등록을 다시 만들 필요가 없는 URL 설정 및 해제를 다시 호출을 전환 하는 데 사용 됩니다. 다시 한 번 호출 하는 프로세스 후 완료 해야 하는 경우 다음 삭제 웹 후크 및 스위치는 `Active` 속성을 false로 합니다.

이벤트 유형 `TranscriptionCompletion` 이벤트 배열에서 제공 됩니다. 터미널 상태에는 기록을 가져오면 끝점으로 다시 호출 합니다 (`Succeeded` 또는 `Failed`). 를 호출할 때 다시 등록 된 URL로 요청 포함 됩니다는 `X-MicrosoftSpeechServices-Event` 등록된 된 이벤트 유형 중 하나를 포함 하는 헤더입니다. 등록 된 이벤트 유형 마다 하나의 요청이 있습니다. 

형식이 하나 이벤트를 구독할 수 없습니다. 것은 `Ping` 이벤트 유형입니다. 이 형식 사용 하 여 요청을 ping URL (아래 참조)를 사용 하는 경우 웹 후크를 만드는 작업을 완료 하는 경우 URL로 보내집니다.  

구성에는 `url` 속성이 필요 합니다. POST 요청은이 URL로 전송 됩니다. `secret` HMAC 키로 암호를 사용 하 여 페이로드의 SHA256 해시를 만드는 데 사용 됩니다. 로 설정 되어 해시를 `X-MicrosoftSpeechServices-Signature` 등록된 URL을 다시 호출 하는 경우 헤더입니다. 이 헤더는 Base64로 인코딩됩니다.

이 샘플에 사용 하 여 페이로드 유효성을 검사 하는 방법을 보여 줍니다 C#:

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
이 코드 조각에는 `secret` 디코딩된 이며 유효성을 검사 합니다. 웹 후크 이벤트 형식 전환 된 알 수 있습니다. 현재는 완료 된 기록 당 하나의 이벤트입니다. 코드를 포기 하기 전에 1 초 지연) (인 각 이벤트에 대해 5 번 다시 시도 합니다.

### <a name="other-webhook-operations"></a>다른 웹 후크 작업

등록 된 모든 웹 후크를 가져오려면: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

특정 웹 후크 하나를 가져오려면: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

에 특정 웹 후크 하나를 제거 합니다. DELETE https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note] 
> 위의 예에서 지역은 'westus'입니다. 이 음성 서비스 리소스에서에서 만든 Azure portal 지역별 대체 되어야 합니다.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping 본문: 빈

등록된 URL에 POST 요청을 보냅니다. 요청에는 `X-MicrosoftSpeechServices-Event` 값 ping 사용 하 여 헤더입니다. 웹 후크는 암호를 사용 하 여 등록 된 경우 포함 됩니다는 `X-MicrosoftSpeechServices-Signature` HMAC 키로 암호를 사용 하 여 페이로드의 SHA256 해시를 사용 하 여 헤더입니다. 해시는 Base64로 인코딩됩니다. 

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test 본문: 빈

구독된 이벤트 유형 (기록)에 대 한 엔터티 시스템에 표시 되며, 적절 한 상태인 경우 등록 된 URL에 POST 요청을 보냅니다. 페이로드는 웹 후크 호출는 마지막 엔터티에서 생성 됩니다. 엔터티가 있으면 게시물 204로 응답 합니다. 테스트 요청 수를 200으로 응답 합니다. 요청 본문은 특정 엔터티에 대 한 GET 요청 (예를 들어 기록)에 대 한 웹 후크 구독와 같이 동일한 모양입니다. 요청 해야 합니다 `X-MicrosoftSpeechServices-Event` 및 `X-MicrosoftSpeechServices-Signature` 전에 설명 된 대로 헤더입니다.

### <a name="run-a-test"></a>테스트 실행

빠른 테스트를 수행할 수 있습니다 웹 사이트를 사용 하 여 https://bin.webhookrelay.com입니다. 여기에서 호출을 가져올 수 있습니다는 문서의 앞부분에서 설명한 웹 후크를 만드는 HTTP POST를 매개 변수로 전달 하는 Url을 백업 합니다.

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
