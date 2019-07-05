---
title: Bing Speech 문제 해결 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Bing Speech 사용 시의 문제를 해결하는 방법입니다.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e70e7b79be7dd4ea55c56898eaf8007d25732366
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60513965"
---
# <a name="troubleshooting-bing-speech"></a>Bing Speech 문제 해결

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

## <a name="error-http-403-forbidden"></a>오류 `HTTP 403 Forbidden`

음성 인식 API를 사용할 때 `HTTP 403 Forbidden` 오류가 반환됩니다.

### <a name="cause"></a>원인

이 오류는 종종 인증 문제로 인해 발생합니다. 유효한 `Ocp-Apim-Subscription-Key` 또는 `Authorization` 헤더가 없는 연결 요청은 서비스에서 `HTTP 403 Forbidden` 응답과 함께 거부됩니다.

인증에 구독 키를 사용하는 경우 그 이유는 다음과 같을 수 있습니다.

- 구독 키가 없거나 잘못되었습니다.
- 구독 키의 사용 할당량이 초과되었습니다.
- REST API가 호출될 때 `Ocp-Apim-Subscription-Key` 필드가 요청 헤더에 설정되지 않았습니다.

인증에 권한 부여 토큰을 사용하는 경우 다음과 같은 이유로 이 오류가 발생할 수 있습니다.

- REST를 사용할 때 요청에서 `Authorization` 헤더가 누락되었습니다.
- 권한 부여 헤더에 지정된 권한 부여 토큰이 잘못되었습니다.
- 권한 부여 토큰이 만료되었습니다. 액세스 토큰의 만료 기간은 10분입니다.

인증에 대한 자세한 내용은 [인증](How-to/how-to-authentication.md) 페이지를 참조하세요.

### <a name="troubleshooting-steps"></a>문제 해결 단계

#### <a name="verify-that-your-subscription-key-is-valid"></a>구독 키가 유효한지 확인

다음 명령을 실행하여 확인할 수 있습니다. *YOUR_SUBSCRIPTION_KEY*를 사용자 고유의 구독 키로 바꿉니다. 구독 키가 유효하면 응답에서 권한 부여 토큰을 JWT(JSON Web Token)로 받습니다. 그렇지 않으면 응답에 오류가 발생합니다.

> [!NOTE]
> `YOUR_SUBSCRIPTION_KEY`를 사용자 고유의 구독 키로 바꾸세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

이 예제에서는 bash가 있는 Linux에서 curl을 사용합니다. 플랫폼에서 사용할 수 없는 경우 curl을 설치해야 할 수 있습니다. 또한 이 예제는 Windows의 Cygwin, Git Bash, zsh 및 다른 셸에서도 작동합니다.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```
---

애플리케이션 또는 REST 요청에서 위에서 사용한 것과 동일한 구독 키를 사용해야 합니다.

#### <a name="verify-the-authorization-token"></a>권한 부여 토큰 확인

이 단계는 인증에 권한 부여 토큰을 사용하는 경우에만 필요합니다. 다음 명령을 실행하여 권한 부여 토큰이 여전히 유효한지 확인합니다. 이 명령은 서비스에 POST를 요청하고 서비스의 응답 메시지를 예상합니다. 여전히 HTTP `403 Forbidden` 오류가 발생하면 액세스 토큰이 정확하게 설정되고 만료되지 않았는지 다시 확인합니다.

> [!IMPORTANT]
> 토큰의 만료 기간은 10분입니다.
> [!NOTE]
> `YOUR_AUDIO_FILE`을 미리 녹음된 오디오 파일의 경로로 바꾸고, `YOUR_ACCESS_TOKEN`을 이전 단계에서 반환된 권한 부여 토큰으로 바꾸세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

---

## <a name="error-http-400-bad-request"></a>오류 `HTTP 400 Bad Request`

일반적으로 요청 본문에 잘못된 오디오 데이터가 포함되어 있기 때문에 이 오류가 발생합니다. 현재 WAV 파일만 지원합니다.

## <a name="error-http-408-request-timeout"></a>`HTTP 408 Request Timeout` 오류

이 오류는 오디오 데이터를 서비스에 보내지 않아 발생했을 가능성이 크며, 시간 제한을 초과하면 서비스에서 이 오류를 반환합니다. REST API의 경우 오디오 데이터가 요청 본문에 배치되어야 합니다.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>응답에서 `RecognitionStatus`가 `InitialSilenceTimeout`입니다

일반적으로 오디오 데이터로 인해 문제가 발생합니다. 예를 들면 다음과 같습니다.

- 오디오의 시작 부분에 긴 묵음 시간이 있습니다. 서비스에서 수 초 후에 인식을 중지하고 `InitialSilenceTimeout`을 반환합니다.
- 오디오에서 지원되지 않는 코덱 형식을 사용하고 있으며, 이에 따라 오디오 데이터가 묵음으로 처리되었습니다.
