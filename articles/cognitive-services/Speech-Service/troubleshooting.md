---
title: Speech SDK의 문제 해결 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Speech Service SDK를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 되는 정보를 제공합니다.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: dbcdfd117a39939491914ebddb717f404e07f09c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55859320"
---
# <a name="troubleshoot-the-speech-service-sdk"></a>Speech Service SDK 문제 해결

이 문서에서는 Speech Service SDK를 사용할 때 발생할 수 있는 문제를 해결하는 데 도움이 되는 정보를 제공합니다.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>오류: 인증 오류(403)를 발생하며 WebSocket 업그레이드가 실패했습니다.

지역 또는 서비스에 대한 잘못된 엔드포인트가 있을 수 있습니다. URI가 올바른지 확인합니다.

또한 구독 키 또는 권한 부여 토큰에 문제가 있을 수 있습니다. 자세한 내용은 다음 섹션을 참조하세요.

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>오류: HTTP 403 사용 권한 없음 또는 HTTP 401 권한 없음

이 오류는 종종 인증 문제로 인해 발생합니다. 유효한 `Ocp-Apim-Subscription-Key` 또는 `Authorization` 헤더가 없는 연결 요청은 상태 403 또는 401로 거부됩니다.

* 인증을 위해 등록 키를 사용하는 경우 다음으로 인해 오류가 발생할 수 있습니다.

    - 구독 키가 없거나 잘못되었습니다.
    - 구독의 사용 할당량을 초과했습니다.

* 인증을 위해 인증 토큰을 사용하는 경우 다음으로 인해 오류가 발생할 수 있습니다.

    - 인증 토큰이 잘못되었습니다.
    - 인증 토큰이 만료되었습니다.

### <a name="validate-your-subscription-key"></a>구독 키의 유효성 검사

다음 명령 중 하나를 실행하여 유효한 구독 키가 있는지 확인할 수 있습니다.

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY`와 `YOUR_REGION`을 고유한 구독 키와 관련 지역으로 바꿉니다.

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

유효한 구독 키를 입력했다면 인증 토큰이 반환되고, 그렇지 않을 경우 오류가 반환됩니다.

### <a name="validate-an-authorization-token"></a>인증 토큰의 유효성 검사

인증에 인증 토큰을 사용하는 경우, 다음 명령 중 하나를 실행하여 인증 토큰이 여전히 유효한지 확인합니다. 토큰은 10분 동안 유효합니다.

> [!NOTE]
> `YOUR_AUDIO_FILE`을 미리 녹음된 오디오 파일의 경로로 바꾸고, `YOUR_ACCESS_TOKEN`을 이전 단계에서 반환된 인증 토큰으로 바꿉니다. `YOUR_REGION`을 올바른 지역으로 바꿉니다.

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

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

유효한 인증 토큰을 입력했다면 오디오 파일의 음성 텍스트가 반환되고, 그렇지 않을 경우 오류가 반환됩니다.

---

## <a name="error-http-400-bad-request"></a>오류: HTTP 400 잘못된 요청

이 오류는 일반적으로 요청 본문에 잘못된 오디오 데이터가 포함된 경우에 발생합니다. WAV 형식만 지원됩니다. 또한 요청의 헤더를 검사하여 적절한 `Content-Type` 및 `Content-Length` 값을 지정했는지 확인합니다.

## <a name="error-http-408-request-timeout"></a>오류: HTTP 408 요청 시간 초과

이 오류는 서비스로 전송 중인 오디오 데이터가 없기 때문에 발생했을 가능성이 큽니다. 네트워크 문제로 인해 발생할 수도 있습니다.

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>응답의 “RecognitionStatus”가 “InitialSilenceTimeout”입니다.

일반적으로 이 문제 오디오 데이터로 인해 발생합니다. 다음 이유로 이 오류가 표시될 수 있습니다.

* 오디오의 시작 부분에 긴 무음 시간이 있습니다. 이 경우 서비스가 몇 초 후에 인식을 중지하고 `InitialSilenceTimeout`을 반환합니다.

* 오디오가 지원되지 않는 코덱 형식을 사용하고 있으며, 이로 인해 오디오 데이터가 무음으로 처리됩니다.

## <a name="next-steps"></a>다음 단계

* [릴리스 정보 검토](releasenotes.md)
