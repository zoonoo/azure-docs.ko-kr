---
title: Cognitive Services Speech SDK 문제 해결
description: Cognitive Services 음성 SDK 문제 해결
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284125"
---
# <a name="troubleshooting-speech-services-sdk"></a>음성 서비스 SDK 문제 해결

이 문서에서는 음성 SDK를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 되는 정보를 제공합니다.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>오류 `WebSocket Upgrade failed with an authentication error (403).`

지역 또는 서비스에 대한 잘못된 끝점이 있을 수 있습니다. URI를 두 번 클릭하여 올바른지 확인합니다. 구독 키 또는 인증 토큰 문제일 수도 있으므로 다음 섹션을 참조하세요.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>오류 `HTTP 403 Forbidden` 또는 오류 `HTTP 401 Unauthorized`

이 오류는 종종 인증 문제로 인해 발생합니다. 유효한 `Ocp-Apim-Subscription-Key` 또는 `Authorization` 헤더가 없는 연결 요청은 상태 401 또는 403으로 거부됩니다.

* 인증에 구독 키를 사용하는 경우 원인은 다음과 같을 수 있습니다.

    - 구독 키가 없거나 잘못되었습니다.
    - 구독의 사용 할당량을 초과했습니다.

* 인증에 인증 토큰을 사용하는 경우 원인은 다음과 같을 수 있습니다.

    - 인증 토큰이 잘못되었습니다.
    - 인증 토큰이 만료되었습니다.

### <a name="validate-your-subscription-key"></a>구독 키의 유효성 검사

아래 명령 중 하나를 실행하여 유효한 구독 키가 있는지 확인할 수 있습니다.

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY`와 `YOUR_REGION`을 각각 고유한 구독 키와 관련 지역으로 바꿉니다.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>인증 토큰의 유효성 검사

인증에 인증 토큰을 사용하는 경우, 다음 명령 중 하나를 실행하여 인증 토큰이 여전히 유효한지 확인합니다. 토큰은 10분 동안 유효합니다.

> [!NOTE]
> `YOUR_AUDIO_FILE`을 미리 녹음된 오디오 파일의 경로로 바꾸고, `YOUR_ACCESS_TOKEN`을 이전 단계에서 반환된 인증 토큰으로 바꾸고, `YOUR_REGION`을 올바른 지역으로 바꿉니다.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>오류 `HTTP 400 Bad Request`

이 오류는 일반적으로 요청 본문에 잘못된 오디오 데이터가 포함된 경우에 발생합니다. `WAV` 형식만 지원됩니다. 또한 요청의 헤더를 검사하여 적절한 `Content-Type` 및 `Content-Length`를 지정했는지 확인합니다.

## <a name="error-http-408-request-timeout"></a>오류 `HTTP 408 Request Timeout`

이 오류는 서비스로 전송 중인 오디오 데이터가 없기 때문에 발생했을 가능성이 큽니다. 네트워크 문제로 인해 발생할 수도 있습니다.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>응답에서 `RecognitionStatus`가 `InitialSilenceTimeout`입니다.

일반적으로 오디오 데이터로 인해 문제가 발생합니다. 예: 

* 오디오의 시작 부분에 긴 무음 시간이 있습니다. 서비스가 몇 초 후에 인식을 중지하고 `InitialSilenceTimeout`을 반환합니다.
* 오디오가 지원되지 않는 코덱 형식을 사용하고 있으며, 이로 인해 오디오 데이터가 무음으로 처리됩니다.

## <a name="next-steps"></a>다음 단계

* [릴리스 정보](releasenotes.md)

