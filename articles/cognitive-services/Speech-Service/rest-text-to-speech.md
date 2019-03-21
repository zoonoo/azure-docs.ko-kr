---
title: 텍스트 음성 변환 API 참조 (REST)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 텍스트 음성 변환 REST API를 사용 하는 방법을 알아봅니다. 이 문서에서는 권한 부여 옵션, 쿼리 옵션, 요청을 구성하고 응답을 받는 방법을 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: d67d8462c177d19dfa3cebbd0b4b000fbe3f41b8
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/14/2019
ms.locfileid: "57895319"
---
# <a name="text-to-speech-rest-api"></a>Text-to-Speech REST API

음성 서비스를 사용 하면 REST API를 사용 하 여 텍스트 음성 변환 서비스로 변환할 수 있습니다. 액세스 가능한 각 엔드포인트는 지역과 연결됩니다. 사용하려는 엔드포인트에 대한 구독 키가 애플리케이션에 필요합니다. 텍스트를 음성으로 변환 REST API는 인공신경망 및 표준 텍스트를 음성으로 보이스를 지원하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다.

* 음성의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.
* 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#text-to-speech)을 참조하세요.

> [!IMPORTANT]
> 비용은 표준, 사용자 지정 및 인공신경망 음성별로 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)을 참조하세요.

이 API를 사용 하기 전에 다음을 이해 합니다.

* Text-to-Speech REST API에는 인증 헤더가 필요합니다. 즉, 서비스에 액세스하기 위해 토큰 교환을 완료해야 합니다. 자세한 내용은 [인증](#authentication)을 참조하세요.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="request-headers"></a>헤더 요청

이 표에는 음성 텍스트 변환 요청에 대한 필수 헤더 및 선택적 헤더가 나와 있습니다.

| 헤더 | 설명 | 필수/선택 |
|--------|-------------|---------------------|
| `Authorization` | 앞에 `Bearer` 단어가 표시되는 인증 토큰입니다. 자세한 내용은 [인증](#authentication)을 참조하세요. | 필수 |
| `Content-Type` | 제공된 텍스트의 콘텐츠 형식을 지정합니다. 허용되는 값: `application/ssml+xml`. | 필수 |
| `X-Microsoft-OutputFormat` | 오디오 출력 형식을 지정합니다. 허용되는 값의 전체 목록은 [오디오 출력](#audio-outputs)을 참조하세요. | 필수 |
| `User-Agent` | 애플리케이션 이름입니다. 제공 된 값은 255 자 미만 이어야 합니다. | 필수 |

## <a name="audio-outputs"></a>오디오 출력

각 요청에서 `X-Microsoft-OutputFormat` 헤더로 전송되는 지원되는 오디오 형식 목록입니다. 각 항목에 전송률 및 인코딩 형식이 포함됩니다. 음성 서비스 24 KHz, 16 KHz 지원 하 고 8 44.1khz 오디오 출력 합니다.

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> 선택한 음성 및 출력 형식의 비트 전송률이 다른 경우 필요에 오디오가 다시 샘플링됩니다. 단, 24khz 음성은 `audio-16khz-16kbps-mono-siren` 및 `riff-16khz-16kbps-mono-siren` 출력 형식을 지원하지 않습니다.

## <a name="request-body"></a>요청 본문

각 `POST` 요청의 본문은 [SSML(Speech Synthesis Markup Language)](speech-synthesis-markup.md)로 전송됩니다. SSML에서는 텍스트 음성 변환 서비스에서 반환한 합성된 음성의 목소리와 언어를 선택할 수 있습니다. 지원되는 목소리의 전체 목록은 [언어 지원](language-support.md#text-to-speech)을 참조하세요.

> [!NOTE]
> 사용자 지정 목소리를 사용하는 경우에는 요청 본문을 일반 텍스트(ASCII 또는 UTF-8)로 전송할 수 있습니다.

## <a name="sample-request"></a>샘플 요청

이 HTTP 요청은 SSML을 사용하여 음성 및 언어를 지정합니다. 본문은 1,000자를 초과할 수 없습니다.

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

언어별 예제에 대 한 빠른 시작을 참조 하세요.

* [.NET Core, C#](quickstart-dotnet-text-to-speech.md)
* [Python](quickstart-python-text-to-speech.md)
* [Node.JS](quickstart-nodejs-text-to-speech.md)

## <a name="http-status-codes"></a>HTTP 상태 코드

각 응답의 HTTP 상태 코드는 성공 또는 일반 오류를 나타냅니다.

| HTTP 상태 코드 | 설명 | 가능한 원인 |
|------------------|-------------|-----------------|
| 200 | 확인 | 요청이 성공했습니다. 응답 본문이 오디오 파일입니다. |
| 400 | 잘못된 요청 | 필수 매개 변수가 없거나 비어 있거나 null입니다. 또는 필수 또는 선택적 매개 변수에 전달된 값이 올바르지 않습니다. 일반적인 문제는 헤더가 너무 긴 경우입니다. |
| 401 | 권한 없음 | 요청에 권한이 없습니다. 구독 키 또는 토큰이 유효하고 올바른 영역에 있는지 확인하세요. |
| 413 | 요청 엔터티가 너무 큼 | SSML 입력이 1024자보다 깁니다. |
| 429 | 너무 많은 요청 | 구독에 허용되는 요청의 할당량 또는 속도가 초과되었습니다. |
| 502 | 잘못된 게이트웨이 | 네트워크 또는 서버 쪽 문제입니다. 잘못된 헤더를 나타낼 수도 있습니다. |

HTTP 상태가 `200 OK`인 경우 응답 본문은 요청된 형식으로 오디오 파일을 포함합니다. 이 파일은 전송 시 재생하거나, 버퍼에 저장하거나, 파일에 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [음향 모델 사용자 지정](how-to-customize-acoustic-models.md)
- [언어 모델 사용자 지정](how-to-customize-language-model.md)
