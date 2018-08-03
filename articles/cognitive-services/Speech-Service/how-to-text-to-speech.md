---
title: Speech Service를 사용하여 텍스트에서 음성 변환 사용 | Microsoft Docs
description: Speech Service에서 텍스트에서 음성 변환을 사용하는 방법을 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 84b577cd00d333717a7a5ad1f66182605429f9f1
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213336"
---
# <a name="use-text-to-speech-in-speech-service"></a>Speech Service에서 "텍스트에서 음성 변환" 사용

Speech Service는 간단한 HTTP 요청을 통해 텍스트에서 음성 변환 기능을 제공합니다. 적절한 끝점에 읽혀지도록 텍스트를 POST하면 이 서비스가 합성 음성을 포함하는 오디오 파일(`.wav`)을 반환합니다. 그러면 응용 프로그램은 이 오디오를 적절히 사용할 수 있습니다.

텍스트를 음성으로 변환에 대한 POST 요청 본문은 일반 텍스트(ASCII 또는 UTF8) 또는 [SSML](speech-synthesis-markup.md) 문서일 수 있습니다. 일반 텍스트 요청은 기본 음성으로 읽혀집니다. 대부분의 경우 SSML 본문을 사용하려고 할 수 있습니다. HTTP 요청에는 [권한 부여](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#authentication) 토큰이 포함되어야 합니다. 

국가별 텍스트 음성 변환 끝점은 다음과 같습니다. 구독에 적합한 끝점을 사용하세요.

[!include[](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>음성 지정

음성을 지정하려면 `<voice>` [SSML](speech-synthesis-markup.md) 태그를 사용합니다. 예: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

사용 가능한 음성 및 해당 이름 목록을 보려면 [텍스트를 음성으로 변환](supported-languages.md#text-to-speech)을 참조하세요.

## <a name="make-a-request"></a>요청하기

텍스트 음성 변환 HTTP 요청은 요청 본문에서 읽힐 텍스트를 사용하여 POST 모드로 수행됩니다. HTTP 요청 본문의 최대 길이는 1024자입니다. 요청에는 다음과 같은 헤더가 있어야 합니다. 

헤더|값|설명
-|-|-
|`Content-Type` | `application/ssml+xml` | 입력 텍스트 형식입니다.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | 오디오 출력 형식입니다.
|`User-Agent`   |응용 프로그램 이름 | 응용 프로그램 이름은 필수이며 255자 미만이어야 합니다.
| `Authorization`   | 토큰 서비스에 구독 키를 제공하여 얻은 권한 부여 토큰입니다. 각 토큰은 10분 동안 유효합니다. [REST API: 인증](rest-apis.md#authentication)을 참조하세요.

> [!NOTE]
> 선택한 음성 및 출력 형식의 비트 전송률이 다른 경우 필요에 오디오가 다시 샘플링됩니다. 24khz 음성은 `audio-16khz-16kbps-mono-siren` 및 `riff-16khz-16kbps-mono-siren` 출력 형식을 지원하지 않습니다. 

샘플 요청은 다음과 같습니다.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

상태가 200인 응답 본문에 지정된 출력 형식의 오디오가 포함됩니다.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

오류가 발생하는 경우 아래의 상태 코드가 사용됩니다. 또한 오류에 대한 응답 본문에는 문제에 대한 설명이 포함됩니다.

|코드|설명|문제|
|-|-|-|
400 |잘못된 요청 |필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다.
401|권한 없음 |요청에 권한이 없습니다. 구독 키 또는 토큰이 유효한지 확인합니다.
413|요청 엔터티가 너무 큼|SSML 입력이 1024자보다 깁니다.
|502|잘못된 게이트웨이    | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다.

Text to Speech REST API에 대한 자세한 내용은 [REST API](rest-apis.md#text-to-speech)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C++에서 음성 인식](quickstart-cpp-windows.md)
- [C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
- [Java에서 음성 인식](quickstart-java-android.md)
