---
title: Bing Speech WebSocket 프로토콜 | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: WebSockets 기반 Bing Speech를 위한 프로토콜 설명서
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6601f57d87b518b2061df64174818432b822755
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58076193"
---
# <a name="bing-speech-websocket-protocol"></a>Bing Speech WebSocket 프로토콜

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech는 음성 오디오를 텍스트로 변환할 수 있는 최고급 알고리즘을 탑재한 클라우드 기반 플랫폼입니다. Bing Speech 프로토콜은 클라이언트 애플리케이션과 서비스 간의 [연결 설정](#connection-establishment) 및 상대 간에 교환하는 음성 인식 메시지([클라이언트 시작 메시지](#client-originated-messages) 및 [서비스 시작 메시지](#service-originated-messages))를 정의합니다. 또한 [원격 분석 메시지](#telemetry-schema) 및 [오류 처리](#error-handling)를 설명합니다.

## <a name="connection-establishment"></a>연결 설정

음성 서비스 프로토콜은 WebSocket 표준 사양 [IETF RFC 6455](https://tools.ietf.org/html/rfc6455)를 따릅니다. WebSocket 연결은 HTTP 헤더를 포함한 HTTP 요청으로 시작하며, 이 헤더는 HTTP 의미 체계를 사용하는 대신에 WebSocket에 대한 연결을 업그레이드하고자 하는 클라이언트의 바람을 나타냅니다. 서버는 HTTP `101 Switching Protocols` 응답을 반환하여 WebSocket 연결에 참가할 자신의 의향을 나타냅니다. 이 핸드셰이크를 교환한 후 클라이언트와 서버는 모두 소켓을 개방 상태로 유지하고 메시지 기반 프로토콜을 사용하여 정보를 보내고 받기 시작합니다.

WebSocket 핸드셰이크를 시작하려면 클라이언트 애플리케이션은 HTTPS GET 요청을 서비스에 보냅니다. 이는 음성 특유의 다른 헤더와 함께 표준 WebSocket 업그레이드 헤더를 포함합니다.

```HTTP
GET /speech/recognition/interactive/cognitiveservices/v1 HTTP/1.1
Host: speech.platform.bing.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Authorization: t=EwCIAgALBAAUWkziSCJKS1VkhugDegv7L0eAAJqBYKKTzpPZOeGk7RfZmdBhYY28jl&p=
X-ConnectionId: A140CAF92F71469FA41C72C7B5849253
Origin: https://speech.platform.bing.com
```

서비스는 다음 내용으로 응답합니다.

```HTTP
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: upgrade
Sec-WebSocket-Key: 2PTTXbeeBXlrrUNsY15n01d/Pcc=
Set-Cookie: SpeechServiceToken=AAAAABAAWTC8ncb8COL; expires=Wed, 17 Aug 2016 15:39:06 GMT; domain=bing.com; path="/"
Date: Wed, 17 Aug 2016 15:03:52 GMT
```

모든 음성 요청에 대해 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) 암호화가 필요합니다. 암호화되지 않은 음성 요청의 사용은 지원하지 않습니다. 다음 TLS 버전을 지원합니다.

* TLS 1.2

### <a name="connection-identifier"></a>연결 식별자

음성 서비스는 모든 클라이언트가 연결을 식별하기 위한 고유한 ID를 포함할 것을 요구합니다. 클라이언트는 WebSocket 핸드셰이크를 시작할 때 *X-ConnectionId* 헤더를 포함*해야 합니다*. *X-ConnectionId* 헤더는 UUID([universally unique identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier)) 값이어야 합니다. *X-ConnectionId*를 포함하지 않거나 *X-ConnectionId* 헤더에 대한 값을 지정하지 않거나 유효한 UUID 값을 포함하지 않은 WebSocket 업그레이드 요청은 서비스에서 `400 Bad Request` 응답과 함께 거부됩니다.

### <a name="authorization"></a>권한 부여

표준 WebSocket 핸드셰이크 헤더 외에 음성 요청에서는 *인증* 헤더가 필요합니다. 이 헤더가 없는 연결 요청은 서비스에서 HTTP `403 Forbidden` 응답과 함께 거부됩니다.

*인증* 헤더는 JWT(JSON Web Token) 액세스 토큰을 포함해야 합니다.

유효한 JWT 액세스 토큰을 검색하는 데 사용되는 API 키를 구독하고 가져오는 방법에 대한 자세한 내용은 [Cognitive Services 구독](https://azure.microsoft.com/try/cognitive-services/) 페이지를 참조하세요.

API 키는 토큰 서비스에 전달됩니다. 예를 들면 다음과 같습니다.

``` HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

액세스 토큰을 위해 다음 헤더 정보가 필요합니다.

| 이름 | 형식 | 설명 |
|----|----|----|
| Ocp-Apim-Subscription-Key | ASCII | 구독 키 |

토큰 서비스는 JWT 액세스 토큰을 `text/plain`으로 반환합니다. 그런 다음, JWT는 문자열 `Bearer`가 앞에 추가된 *인증* 헤더로 핸드셰이크에 `Base64 access_token`으로 전달됩니다. 예를 들면 다음과 같습니다.

`Authorization: Bearer [Base64 access_token]`

### <a name="cookies"></a>쿠키

클라이언트는 [RFC 6265](https://tools.ietf.org/html/rfc6265)에 지정한 대로 HTTP 쿠키를 지원*해야 합니다*.

### <a name="http-redirection"></a>Http 리디렉션

클라이언트는 [HTTP 프로토콜 사양](https://www.w3.org/Protocols/rfc2616/rfc2616.html)에서 지정한 표준 리디렉션 메커니즘을 지원*해야 합니다*.

### <a name="speech-endpoints"></a>음성 엔드포인트

클라이언트는 음성 서비스의 적절한 엔드포인트를 사용*해야 합니다*. 엔드포인트는 인식 모드 및 언어를 기반으로 합니다. 다음 표가 몇 가지 예를 보여줍니다.

| Mode | path | 서비스 URI |
| -----|-----|-----|
| 대화형 | /speech/recognition/interactive/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| 대화 | /speech/recognition/conversation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US |
| 받아쓰기 | /speech/recognition/dictation/cognitiveservices/v1 | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR |

자세한 내용은 [서비스 URI](../GetStarted/GetStartedREST.md#service-uri) 페이지를 참조하세요.

### <a name="report-connection-problems"></a>연결 문제 보고

클라이언트는 연결하는 중에 발견된 모든 문제를 즉시 보고하는 것이 좋습니다. 실패한 연결을 보고하기 위한 메시지 프로토콜은 [연결 실패 원격 분석](#connection-failure-telemetry)에서 설명합니다.

### <a name="connection-duration-limitations"></a>연결 지속 시간 제한

대표적인 웹 서비스 HTTP 연결과 비교할 때, WebSocket 연결은 *오랜* 시간 동안 지속됩니다. 음성 서비스는 서비스에 대한 WebSocket 연결의 지속시간을 제한합니다.

 * 활성 WebSocket 연결에 대한 최대 지속 시간은 10분입니다. 연결은 서비스 또는 클라이언트가 해당 연결을 통해 WebSocket 메시지를 보내면 활성화됩니다. 서비스는 제한에 도달하면 경고 없이 연결을 종료합니다. 클라이언트는 연결이 최대 연결 수명 시간 또는 임박한 시간까지 활성 상태로 유지되는 것을 요구하지 않는 사용자 시나리오를 개발하는 것이 좋습니다.

 * 비활성 WebSocket 연결에 대한 최대 지속 시간은 180초입니다. 연결은 서버도 클라이언트도 해당 연결을 통해 WebSocket 메시지를 보내지 않으면 비활성화됩니다. 최대 비활성 수명에 도달한 후 서비스는 비활성 WebSocket 연결을 종료합니다.

## <a name="message-types"></a>메시지 유형

클라이언트와 서비스 간에 WebSocket 연결이 설정된 후 클라이언트와 서비스가 모두 메시지를 보낼 수 있습니다. 이 섹션에서는 이러한 WebSocket 메시지의 형식을 설명합니다.

[IETF RFC 6455](https://tools.ietf.org/html/rfc6455)는 WebSocket 메시지가 텍스트 또는 이진 인코딩을 사용하여 데이터를 송신할 수 있도록 할 것을 지정합니다. 두 인코딩은 서로 다른 실시간 형식을 사용합니다. 각 형식은 효율적인 인코딩, 전송 및 메시지 페이로드의 디코딩을 위해 최적화됩니다.

### <a name="text-websocket-messages"></a>텍스트 WebSocket 메시지

텍스트 WebSocket 메시지는 헤더 섹션 및 HTTP 메시지에 사용되는 친숙한 이중 캐리지 리턴 줄 바꿈 쌍으로 구분된 본문으로 구성된 텍스트 정보의 페이로드를 전달합니다. 그리고 HTTP 메시지와 마찬가지로 텍스트 WebSocket 메시지는 단일 캐리지 리턴 줄 바꿈 쌍으로 구분된 *이름:값* 형식으로 헤더를 지정합니다. 텍스트 WebSocket 메시지에 포함된 텍스트는 [UTF-8](https://tools.ietf.org/html/rfc3629) 인코딩을 사용*해야 합니다*.

텍스트 WebSocket 메시지는 헤더 *경로*에 메시지 경로를 지정해야 합니다. 이 헤더의 값은 이 문서에서 나중에 정의하는 음성 프로토콜 메시지 유형 중 하나이어야 합니다.

### <a name="binary-websocket-messages"></a>이진 WebSocket 메시지

이진 WebSocket 메시지는 이진 페이로드를 전달합니다. 음성 서비스 프로토콜에서 오디오는 이진 WebSocket 메시지를 사용하여 서비스에서 송신 및 수신됩니다. 모든 다른 메시지는 텍스트 WebSocket 메시지입니다.

텍스트 WebSocket 메시지와 마찬가지로 이진 WebSocket 메시지도 헤더와 본문 섹션으로 구성됩니다. 이진 WebSocket 메시지의 처음 2바이트는 헤더 섹션의 16비트 정수 크기를 [big-endian](https://en.wikipedia.org/wiki/Endianness) 순서로 지정합니다. 최소 헤더 섹션 크기는 0바이트입니다. 최대 크기는 8,192바이트입니다. 이진 WebSocket 메시지의 헤더의 텍스트는 [US-ASCII](https://tools.ietf.org/html/rfc20) 인코딩을 사용*해야 합니다*.

이진 WebSocket 메시지의 헤더는 텍스트 WebSocket 메시지에서와 같은 형식으로 인코딩됩니다. *이름:값* 형식은 단일 캐리지 리턴 줄 바꿈 쌍으로 구분됩니다. 이진 WebSocket 메시지는 헤더 *경로*에 메시지 경로를 지정해야 합니다. 이 헤더의 값은 이 문서에서 나중에 정의하는 음성 프로토콜 메시지 유형 중 하나이어야 합니다.

텍스트 및 이진 WebSocket 메시지 모두 음성 서비스 프로토콜에 사용됩니다.

## <a name="client-originated-messages"></a>클라이언트 시작 메시지

연결이 설정된 후 클라이언트와 서비스가 모두 메시지를 보내기 시작할 수 있습니다. 이 섹션에서는 클라이언트 애플리케이션이 음성 서비스에 보내는 메시지의 형식 및 페이로드를 설명합니다. [서비스 시작 메시지](#service-originated-messages) 섹션에서는 음성 서비스에서 시작하고 클라이언트 애플리케이션에 보내지는 메시지를 보여줍니다.

클라이언트가 서비스에 보내는 주 메시지는 `speech.config`, `audio` 및 `telemetry` 메시지입니다. 각 메시지를 자세히 검토하기 전에 이러한 모든 메시지에 공통적으로 필요한 메시지 헤더를 설명합니다.

### <a name="required-message-headers"></a>필요한 메시지 헤더

모든 클라이언트 시작 메시지에 대해 다음 헤더가 필요합니다.

| 헤더 | 값 |
|----|----|
| path | 이 문서에 지정된 메시지 경로 |
| X-RequestId | "대시 없는" 형식의 UUID |
| X-Timestamp | ISO 8601 형식의 UTC 시계 타임스탬프 |

#### <a name="x-requestid-header"></a>X-RequestId 헤더

클라이언트 시작 요청은 *X-RequestId* 메시지 헤더에 의해 고유하게 식별됩니다. 이 헤더는 모든 클라이언트 시작 메시지에 대해 필요합니다. *X-RequestId* 헤더 값은 "대시 없는" 형식의 UUID이어야 합니다(예: *123e4567e89b12d3a456426655440000*). 이는 Canonical 형식 *123e4567-e89b-12d3-a456-426655440000*일 수 *없습니다*. *X-RequestId* 헤더가 없거나 UUID에 대해 잘못된 형식을 사용하는 헤더 값을 가진 요청은 서비스가 WebSocket 연결을 종료하게 합니다.

#### <a name="x-timestamp-header"></a>X-Timestamp 헤더

클라이언트 애플리케이션이 음성 서비스에 보낸 각 메시지는 *X-Timestamp* 헤더를 포함*해야 합니다*. 이 헤더에 대한 값은 클라이언트가 메시지를 보내는 시간입니다. *X-Timestamp* 헤더가 없거나 잘못된 형식을 사용하는 헤더 값을 가진 요청은 서비스가 WebSocket 연결을 종료하게 합니다.

*X-Timestamp* 헤더 값은 'yyyy'-'MM'-'dd'T'HH':'mm':'ss'.'fffffffZ' 형식이어야 합니다. 단, 'fffffff'는 초의 분수입니다. 예를 들어 '12.5'는 '12 + 5/10초'를 의미하며 '12.526'은 '12 plus 526/1000초'를 의미합니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)를 준수하며 표준 HTTP *Date* 헤더와 달리 밀리초 분해능을 제공할 수 있습니다. 클라이언트 애플리케이션은 타임스탬프를 가장 가까운 밀리초로 반올림할 수 있습니다. 클라이언트 애플리케이션은 [NTP(Network Time Protocol) 서버](https://en.wikipedia.org/wiki/Network_Time_Protocol)를 사용하여 장치 시계가 시간을 정확히 추적하도록 해야 합니다.

### <a name="message-speechconfig"></a>메시지 `speech.config`

음성 서비스가 최상의 예상 음성 인식을 제공하려면 애플리케이션의 특성을 알아야 합니다. 필요한 특성 데이터는 애플리케이션을 구동하는 장치 및 OS에 관한 정보를 포함합니다. 이 정보를 `speech.config` 메시지에 제공합니다.

클라이언트는 음성 서비스에 대한 연결을 설정한 후 `audio` 메시지를 보내기 전에 `speech.config` 메시지를 *보내야 합니다*. `speech.config` 메시지를 연결당 한 번만 보내야 합니다.

| 필드 | 설명 |
|----|----|
| WebSocket 메시지 인코딩 | 텍스트 |
| 본문 | JSON 구조인 페이로드 |

#### <a name="required-message-headers"></a>필요한 메시지 헤더

| 헤더 이름 | 값 |
|----|----|
| path | `speech.config` |
| X-Timestamp | ISO 8601 형식의 UTC 시계 타임스탬프 |
| 콘텐츠 형식 | application/json; charset=utf-8 |

음성 서비스 프로토콜의 모든 클라이언트 시작 메시지와 마찬가지로 `speech.config` 메시지도 메시지를 서비스에 보낸 클라이언트 UTC 시계 시간을 기록하는 *X-Timestamp* 헤더를 포함*해야 합니다*. `speech.config` 메시지는 특정 음성 요청과 연결되지 않으므로 *X-RequestId* 헤더를 요구하지 *않습니다*.

#### <a name="message-payload"></a>메시지 페이로드
`speech.config` 메시지의 페이로드는 애플리케이션에 관한 정보를 포함하는 JSON 구조입니다. 다음 예제는 이 정보를 보여줍니다. 클라이언트 및 디바이스 컨텍스트 정보는 JSON 구조의 *context* 요소에 포함됩니다.

```JSON
{
  "context": {
    "system": {
      "version": "2.0.12341",
    },
    "os": {
      "platform": "Linux",
      "name": "Debian",
      "version": "2.14324324"
    },
    "device": {
      "manufacturer": "Contoso",
      "model": "Fabrikan",
      "version": "7.341"
      }
    },
  }
}
```

##### <a name="system-element"></a>시스템 요소

`speech.config` 메시지의 system.version 요소는 클라이언트 애플리케이션 또는 장치가 사용한 음성 SDK 소프트웨어의 버전을 포함합니다. 이 값은 *major.minor.build.branch* 형식입니다. *branch* 구성 요소는 해당하지 않는 경우 생략할 수 있습니다.

##### <a name="os-element"></a>OS 요소

| 필드 | 설명 | 사용 현황 |
|-|-|-|
| os.platform | 애플리케이션을 호스팅하는 OS 플랫폼. 예: Windows, Android, iOS 또는 Linux |필수 |
| os.name | OS 제품 이름. 예: Debian 또는 Windows 10 | 필수 |
| os.version | *major.minor.build.branch* 형식의 OS 버전 | 필수 |

##### <a name="device-element"></a>디바이스 요소

| 필드 | 설명 | 사용 현황 |
|-|-|-|
| device.manufacturer | 디바이스 하드웨어 제조업체 | 필수 |
| device.model | 디바이스 모델 | 필수 |
| device.version | 디바이스 제조업체가 제공한 디바이스 소프트웨어 버전. 이 값은 제조업체가 추적할 수 있는 디바이스의 버전을 지정합니다. | 필수 |

### <a name="message-audio"></a>메시지 `audio`

음성 지원 클라이언트 애플리케이션은 오디오 스트림을 일련의 오디오 청크로 변환하여 오디오를 음성 서비스에 보냅니다. 각 오디오 청크는 서비스에서 전사할 음성 오디오의 세그먼트를 전달합니다. 단일 오디오 청크의 최대 크기는 8,192바이트입니다. 오디오 스트림 메시지는 *이진 WebSocket 메시지*입니다.

클라이언트는 `audio` 메시지를 사용하여 오디오 청크를 서비스에 보냅니다. 클라이언트는 마이크에서 오디오를 청크 단위로 읽고 이 청크를 전사하기 위해 음성 서비스에 보냅니다. 첫 번째 `audio` 메시지는 오디오가 서비스에서 지원하는 인코딩 형식 중 하나에 적합함을 올바르게 지정하는 잘 구성된 헤더를 포함해야 합니다. 추가 `audio` 메시지는 마이크에서 읽은 이진 오디오 스트림 데이터만 포함합니다.

클라이언트는 선택적으로 0 길이 본문을 가진 `audio` 메시지를 보내도 됩니다. 이 메시지는 사용자가 말하기를 중단하고 발성이 완료되고 마이크가 꺼졌음을 클라이언트가 알고 있다는 것을 서비스에 알립니다.

음성 서비스는 고유한 요청 식별자를 포함하는 첫 번째 `audio` 메시지를 사용하여 새 요청/응답 사이클 또는 *회차*의 시작을 신호합니다. 서비스는 새 요청 식별자를 포함한 `audio` 메시지를 수신한 후 이전 회차와 연결된 큐에 저장되거나 보내지 않은 메시지를 버립니다.

| 필드 | 설명 |
|-------------|----------------|
| WebSocket 메시지 인코딩 | 이진 |
| 본문 | 오디오 청크에 대한 이진 데이터입니다. 최대 크기는 8,192바이트입니다. |

#### <a name="required-message-headers"></a>필요한 메시지 헤더

모든 `audio` 메시지에 대해 다음 헤더가 필요합니다.

| 헤더         |  값     |
| ------------- | ---------------- |
| path | `audio` |
| X-RequestId | "대시 없는" 형식의 UUID |
| X-Timestamp | ISO 8601 형식의 UTC 시계 타임스탬프 |
| 콘텐츠 형식 | 오디오 콘텐츠 형식입니다. 형식은 *audio/x-wav*(PCM) 또는 *audio/silk*(SILK)이어야 합니다. |

#### <a name="supported-audio-encodings"></a>지원되는 오디오 인코딩

이 섹션에서는 음성 서비스에서 지원하는 오디오 코덱을 설명합니다.

##### <a name="pcm"></a>PCM

음성 서비스는 압축되지 않은 PCM(펄스 코드 변조) 오디오를 수용합니다. 오디오는 [WAV](https://en.wikipedia.org/wiki/WAV) 형식으로 서비스에 보내지므로 첫 번째 오디오 청크는 유효한 RIFF([Resource Interchange File Format](https://en.wikipedia.org/wiki/Resource_Interchange_File_Format)) 헤더를 포함*해야 합니다*. 클라이언트가 유효한 RIFF 헤더를 포함하지 *않은* 오디오 청크로 회차를 시작한 경우 서비스는 요청을 거부하고 WebSocket 연결을 종료합니다.

PCM 오디오는 샘플당 16비트 및 채널(*riff-16khz-16bit-mono-pcm*) 한 개를 사용하여 16 kHz 속도로 샘플링*해야 합니다*. 음성 서비스는 스테레오 오디오 스트림을 지원하지 않으며 지정된 비트 전송률, 샘플 속도 또는 채널 수를 사용하지 않은 오디오 스트림을 거부합니다.

##### <a name="opus"></a>Opus

Opus는 공개되고 사용료가 없고 매우 용도가 다양한 오디오 코덱입니다. 음성 서비스는 Opus를 `32000` 또는 `16000`의 일정한 비트 전송률로 지원합니다. `audio/ogg` MIME 형식에서 지정하는 Opus용 `OGG` 컨테이너만이 현재 지원됩니다.

Opus를 사용하려면 [JavaScript 샘플](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/master/samples/browser/Sample.html#L101)을 수정하고 `RecognizerSetup` 메서드를 반환하도록 변경합니다.

```javascript
return SDK.CreateRecognizerWithCustomAudioSource(
            recognizerConfig,
            authentication,
            new SDK.MicAudioSource(
                     new SDK.OpusRecorder(
                     {
                         mimeType: "audio/ogg",
                         bitsPerSecond: 32000
                     }
              )
          ));
```

#### <a name="detect-end-of-speech"></a>음성 끝 검색

사람은 말하기를 마친 시기를 명시적으로 신호하지 않습니다. 음성을 입력으로 수용하는 애플리케이션은 오디오 스트림에서 음성의 끝을 처리하기 위한 두 가지 선택: 서비스 음성 끝 검색 및 클라이언트 음성 끝 검색을 포함합니다. 이 두 선택 중에서 서비스 음성 끝 검색이 일반적으로 더 나은 경험을 제공합니다.

##### <a name="service-end-of-speech-detection"></a>서비스 음성 끝 검색

손이 가지 않는 최적의 음성 경험을 만들기 위해 애플리케이션은 서비스에서 사용자가 말하기를 마친 시기를 검색할 수 있도록 합니다. 클라이언트는 서비스가 무음을 검색하고 `speech.endDetected` 메시지로 다시 응답할 때까지 마이크의 오디오를 *오디오* 청크로 보냅니다.

##### <a name="client-end-of-speech-detection"></a>클라이언트 음성 끝 검색

사용자가 어떤 방법으로든 음성 끝을 신호할 수 있도록 하는 클라이언트 애플리케이션은 서비스에도 해당 신호를 제공할 수 있습니다. 예를 들어 클라이언트 애플리케이션은 사용자가 누를 수 있는 "정지" 또는 "음소거" 단추를 포함할 수 있습니다. 음성 끝을 신호하려면 클라이언트 애플리케이션은 0 길이 본문을 가진 *오디오* 청크를 보냅니다. 음성 서비스는 이 메시지를 수신 오디오 스트림의 끝으로 해석합니다.

### <a name="message-telemetry"></a>메시지 `telemetry`

클라이언트 애플리케이션은 회차에 관한 원격 분석을 음성 서비스에 보내서 각 회차의 끝을 수신 확인*해야 합니다*. 회차 끝 수신 확인을 사용하여 음성 서비스는 요청 완료에 필요한 모든 메시지와 해당 응답이 클라이언트에서 올바르게 수신되도록 할 수 있습니다. 회차 끝 수신 확인을 사용하여 음성 서비스는 클라이언트 애플리케이션이 예상한 대로 수행하고 있는지 확인할 수 있습니다. 이 정보는 음성 지원 애플리케이션의 문제 해결에 도움을 주어야 하는 경우 매우 중요합니다.

클라이언트는 `turn.end` 메시지를 수신한 직후 `telemetry` 메시지를 보내서 회차 끝을 수신 확인해야 합니다. 클라이언트는 가능하면 일찍 `turn.end` 수신 확인을 시도하는 것이 좋습니다. 클라이언트 애플리케이션이 회차 끝을 수신 확인하는 데 실패한 경우 음성 서비스는 오류와 함께 연결을 종료할 수 있습니다. 클라이언트는 *X-RequestId* 값에 의해 식별된 각 요청 및 응답에 대해 `telemetry` 메시지를 한 개만 보내야 합니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `telemetry` |
| X-Timestamp | ISO 8601 형식의 UTC 시계 타임스탬프 |
| 콘텐츠 형식 | `application/json` |
| 본문 | 회차에 관한 클라이언트 정보를 포함하는 JSON 구조 |

`telemetry` 메시지의 본문에 대한 스키마는 [원격 분석 스키마](#telemetry-schema) 섹션에서 정의합니다.

#### <a name="telemetry-for-interrupted-connections"></a>중단된 연결에 대한 원격 분석

네트워크 연결이 회차 중에 어떤 이유로든 실패하고 클라이언트가 서비스에서 `turn.end` 메시지를 수신하지 *않는* 경우 클라이언트는 `telemetry` 메시지를 보냅니다. 이 메시지는 다음에 클라이언트가 서비스에 연결할 때 실패한 요청을 설명합니다. 클라이언트가 `telemetry` 메시지를 보내기 위해 즉시 연결을 시도할 필요는 없습니다. 메시지를 클라이언트에 버퍼링했다가 나중에 사용자가 요청한 연결에 대해 보낼 수 있습니다. 실패한 요청에 대한 `telemetry` 메시지는 실패한 요청에서 온 *X-RequestId* 값을 사용*해야 합니다*. 이 값을 연결이 설정되자마자 서비스에 보낼 수 있으므로 다른 메시지에 대한 보내기 또는 수신을 기다릴 필요가 없습니다.

## <a name="service-originated-messages"></a>서비스 시작 메시지

이 섹션에서는 음성 서비스에서 시작하고 클라이언트에 보내지는 메시지를 설명합니다. 음성 서비스는 클라이언트 기능의 레지스트리를 유지하며 각 클라이언트가 요구하는 메시지를 생성하므로 일부 클라이언트는 여기서 설명하는 일부 메시지를 수신하지 않을 수도 있습니다. 요약하자면 메시지는 *경로* 헤더에 의해 참조됩니다. 예를 들어 *경로* 값 `speech.hypothesis`를 포함한 WebSocket 텍스트 메시지를 speech.hypothesis 메시지라고 합니다.

### <a name="message-speechstartdetected"></a>메시지 `speech.startDetected`

`speech.startDetected` 메시지는 음성 서비스가 오디오 스트림에서 음성을 검색했음을 나타냅니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `speech.startDetected` |
| 콘텐츠 형식 | application/json; charset=utf-8 |
| 본문 | 음성 시작이 검색된 경우 조건에 관한 정보를 포함하는 JSON 구조입니다. 이 구조의 *오프셋* 필드는 스트림의 시작을 기준으로 오디오 스트림에서 음성이 검색된 시간의 오프셋(100나노초 단위)을 지정합니다. |

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: speech.startDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 100000
}
```

### <a name="message-speechhypothesis"></a>메시지 `speech.hypothesis`

음성 인식 중에 음성 서비스는 주기적으로 서비스가 인식한 단어에 관한 가설을 생성합니다. 음성 서비스는 이 가설을 대략 300밀리초마다 클라이언트에 보냅니다. `speech.hypothesis`는 사용자 음성 경험을 향상시키기 *위해서만* 적합합니다. 이러한 메시지의 텍스트 내용 또는 정확도에 의존하지 않아야 합니다.

 `speech.hypothesis` 메시지는 일부 텍스트 렌더링 기능을 포함하고 진행 중인 인식의 실시간에 가까운 피드백을 말하는 사람에게 제공하려는 클라이언트에 적용됩니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `speech.hypothesis` |
| X-RequestId | "대시 없는" 형식의 UUID |
| 콘텐츠 형식 | application/json |
| 본문 | 음성 가설 JSON 구조 |

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: speech.hypothesis
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Text": "this is a speech hypothesis",
  "Offset": 0,
  "Duration": 23600000
}
```

*오프셋* 요소는 오디오 스트림의 시작을 기준으로 구가 인식된 오프셋(100나노초 단위)을 지정합니다.

*지속 시간* 요소는 이 음성 구의 지속 시간(100나노초 단위)을 지정합니다.

클라이언트는 음성 가설에 포함된 주파수, 타이밍 또는 텍스트, 또는 두 음성 가설의 텍스트 일관성에 관하여 가정을 하지 않아야 합니다. 가설은 단지 서비스의 전사 프로세스에 대한 스냅샷일 뿐이며, 전사의 안정된 누적을 나타내지 않습니다. 예를 들어 첫 번째 음성 가설이 단어 "fine fun"을 포함하고 두 번째 가설이 단어 "find funny"를 포함할 수 있습니다. 음성 서비스는 음성 가설의 텍스트에 관하여 사후 처리(예: 대문자화, 문장 부호)를 수행하지 않습니다.

### <a name="message-speechphrase"></a>메시지 `speech.phrase`

음성 서비스가 변경하지 않을 인식 결과를 생성하기에 충분한 정보를 확보했다고 결정하면 서비스는 `speech.phrase` 메시지를 생성합니다. 음성 서비스는 사용자가 문장 또는 구를 마친 것을 검색한 후 이러한 결과를 생성합니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `speech.phrase` |
| 콘텐츠 형식 | application/json |
| 본문 | 음성 구 JSON 구조 |

음성 구 JSON 스키마는 `RecognitionStatus`, `DisplayText`, `Offset` 및 `Duration` 필드를 포함합니다. 이러한 필드에 대한 자세한 내용은 [전사 응답](../concepts.md#transcription-responses)을 참조하세요.

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: speech.phrase
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": 0,
  "Duration": 12300000
}
```

### <a name="message-speechenddetected"></a>메시지 `speech.endDetected`

`speech.endDetected` 메시지는 클라이언트 애플리케이션이 서비스에 대한 오디오 스트리밍을 중단하는 것이 좋음을 지정합니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `speech.endDetected` |
| 본문 | 음성 끝이 검색된 시간의 오프셋을 포함하는 JSON 구조입니다. 오프셋은 인식을 위해 사용한 오디오의 시작에서 100나노초 단위 오프셋으로 나타냅니다. |
| 콘텐츠 형식 | application/json; charset=utf-8 |

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: speech.endDetected
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "Offset": 0
}
```

*오프셋* 요소는 오디오 스트림의 시작을 기준으로 구가 인식된 오프셋(100나노초 단위)을 지정합니다.

### <a name="message-turnstart"></a>메시지 `turn.start`

`turn.start`는 서비스 관점에서 회차의 시작을 신호합니다. `turn.start` 메시지는 언제나 요청에 대해 수신하는 첫 번째 응답 메시지입니다. `turn.start` 메시지를 수신하지 않은 경우 서비스 연결의 시작이 잘못되었다고 가정합니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `turn.start` |
| 콘텐츠 형식 | application/json; charset=utf-8 |
| 본문 | JSON 구조 |

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: turn.start
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000

{
  "context": {
    "serviceTag": "7B33613B91714B32817815DC89633AFA"
  }
}
```

`turn.start` 메시지의 본문은 회차의 시작에 대한 컨텍스트를 포함하는 JSON 구조입니다. *컨텍스트* 요소는 *serviceTag* 속성을 포함합니다. 이 속성은 서비스가 회차와 연결된 태그 값을 지정합니다. 이 값은 Microsoft가 고객의 애플리케이션의 문제 해결을 도와야 하는 경우에 사용할 수 있습니다.

### <a name="message-turnend"></a>메시지 `turn.end`

`turn.end`는 서비스 관점에서 회차의 끝을 신호합니다. `turn.end` 메시지는 언제나 요청에 대해 수신하는 마지막 응답 메시지입니다. 클라이언트는 이 메시지의 수신을 정리 작업 및 유휴 상태로 전환의 신호로 사용할 수 있습니다. `turn.end` 메시지를 수신하지 않은 경우 서비스 연결의 시작이 잘못되었다고 가정합니다. 그러한 경우 서비스에 대한 기존 연결을 닫고 다시 연결합니다.

| 필드 | 설명 |
| ------------- | ---------------- |
| WebSocket 메시지 인코딩 | 텍스트 |
| path | `turn.end` |
| 본문 | 없음 |

#### <a name="sample-message"></a>샘플 메시지

```HTML
Path: turn.end
X-RequestId: 123e4567e89b12d3a456426655440000
```

## <a name="telemetry-schema"></a>원격 분석 스키마

*원격 분석* 메시지의 본문은 회차 또는 시도한 연결에 관한 클라이언트 정보를 포함하는 JSON 구조입니다. 이 구조는 클라이언트 이벤트가 발생하는 시간을 기록하는 클라이언트 타임스탬프로 구성됩니다. 각 타임스탬프는 "X-Timestamp 헤더"라는 제목의 섹션에서 설명한 대로 ISO 8601 형식이어야 합니다. JSON 구조에 일부 필수 필드를 지정하지 않거나 정확한 타임스탬프 형식을 사용하지 않은 *원격 분석* 메시지는 서비스가 클라이언트에 대한 연결을 종료하게 할 수 있습니다. 클라이언트는 모든 필수 필드에 유효한 값을 제공*해야 합니다*. 클라이언트는 해당하는 경우 언제나 선택 필드에 대한 값을 제공*하는 것이 좋습니다*. 이 섹션의 샘플에 나오는 값은 예시용일 뿐입니다.

원격 분석 스키마는 수신된 메시지 타임스탬프와 메트릭 부분으로 나누어집니다. 각 부분의 형식과 사용을 다음 섹션에서 지정합니다.

### <a name="received-message-time-stamps"></a>수신된 메시지 타임스탬프

클라이언트는 서비스에 성공적으로 연결한 후 수신하는 모든 메시지에 대해 수신 시간 값을 포함해야 합니다. 이러한 값은 클라이언트가 네트워크에서 각 메시지를 *수신한* 시간을 기록해야 합니다. 이 값은 다른 시간을 기록하지 않는 것이 좋습니다. 예를 들어 클라이언트는 메시지에 대해 *작업한* 시간을 기록하지 않는 것이 좋습니다. 수신된 메시지 타임스탬프는 *이름:값* 쌍의 배열에 지정됩니다. 쌍의 이름은 메시지의 *경로* 값을 지정합니다. 쌍의 값은 메시지가 수신된 클라이언트 시간을 지정합니다. 또는 지정된 이름의 메시지가 두 개 이상 수신된 경우 쌍의 값은 해당 메시지가 수신된 시간을 나타내는 타임스탬프의 배열입니다.

```JSON
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.172Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ]
```

클라이언트는 서비스에서 보낸 모든 메시지의 타임스탬프를 JSON 본문에 포함하여 해당 메시지의 수신을 확인*해야* 합니다. 클라이언트가 메시지의 수신을 확인하지 않으면 서비스가 연결을 종료할 수 있습니다.

### <a name="metrics"></a>메트릭

클라이언트는 요청의 수명 중에 발생한 이벤트에 관한 정보를 포함해야 합니다. 다음 메트릭이 지원됩니다. `Connection`, `Microphone` 및 `ListeningTrigger`.

### <a name="metric-connection"></a>메트릭 `Connection`

`Connection` 메트릭은 클라이언트의 연결 시도에 관한 세부 정보를 지정합니다. 이 메트릭은 WebSocket 연결이 시작된 시간과 종료된 시간의 타임스탬프를 포함해야 합니다. `Connection` 메트릭은 *첫 번째 연결 회차에 대해서만* 필요합니다. 이후 회차는 이 정보를 포함하지 않아도 됩니다. 클라이언트가 연결이 설정되기 전에 여러 번 연결을 시도한 경우 시도한 *모든* 연결에 관한 정보를 포함하는 것이 좋습니다. 자세한 내용은 [연결 실패 원격 분석](#connection-failure-telemetry)을 참조하세요.

| 필드 | 설명 | 사용 현황 |
| ----- | ----------- | ----- |
| 이름 | `Connection` | 필수 |
| Id | 이 연결 요청에 대한 *X-ConnectionId* 헤더에 사용한 연결 식별자 값 | 필수 |
| 시작 | 클라이언트가 연결 요청을 보낸 시간 | 필수 |
| 끝 | 클라이언트가 연결이 성공적으로 설정된 사실 또는 오류가 발생한 경우 거부, 거절 또는 실패한 사실의 알림을 받은 시간 | 필수 |
| 오류 | 발생한 오류(있는 경우)에 대한 설명. 연결이 성공한 경우 클라이언트는 이 필드를 생략하는 것이 좋습니다. 이 필드의 최대 길이는 50자입니다. | 오류의 경우 필수, 그렇지 않으면 생략됨 |

오류 설명은 50문자 이하로 하는 것이 좋으며 다음 표에 나열한 값 중 하나로 하는 것이 가장 좋습니다. 오류 조건이 이러한 값 중 하나와 일치하지 않는 경우 클라이언트는 [CamelCasing](https://en.wikipedia.org/wiki/Camel_case)을 공백 없이 사용하여 오류 조건에 대한 간결한 설명을 사용할 수 있습니다. 서비스에 연결해야 *원격 분석* 메시지를 보낼 수 있으므로 과도적 또는 일시적 오류 조건만이 *원격 분석* 메시지에 보고될 수 있습니다. 클라이언트가 서비스에 대한 연결을 설정하는 것을 *영구적으로* 차단하는 오류 조건은 클라이언트가 *원격 분석* 메시지를 포함하여 메시지를 서비스에 보내지 못하게 합니다.

| 오류 | 사용 현황 |
| ----- | ----- |
| DNSfailure | 클라이언트가 네트워크 스택의 DNS 실패 때문에 서비스에 연결할 수 없었습니다. |
| NoNetwork | 클라이언트가 연결을 시도했지만 네트워크 스택이 사용 가능한 실제 네트워크가 없다고 보고했습니다. |
| NoAuthorization | 연결에 대한 인증 토큰의 획득을 시도하는 중에 클라이언트 연결이 실패했습니다. |
| NoResources | 클라이언트가 연결을 시도하는 중에 일부 로컬 리소스(예: 메모리)가 부족했습니다. |
| 사용할 수 없음 | 서비스가 WebSocket 업그레이드 요청에 대해 HTTP `403 Forbidden` 상태 코드를 반환했기 때문에 클라이언트가 서비스에 연결할 수 없었습니다. |
| 권한 없음 | 서비스가 WebSocket 업그레이드 요청에 대해 HTTP `401 Unauthorized` 상태 코드를 반환했기 때문에 클라이언트가 서비스에 연결할 수 없었습니다. |
| BadRequest | 서비스가 WebSocket 업그레이드 요청에 대해 HTTP `400 Bad Request` 상태 코드를 반환했기 때문에 클라이언트가 서비스에 연결할 수 없었습니다. |
| ServerUnavailable | 서비스가 WebSocket 업그레이드 요청에 대해 HTTP `503 Server Unavailable` 상태 코드를 반환했기 때문에 클라이언트가 서비스에 연결할 수 없었습니다. |
| ServerError | 서비스가 WebSocket 업그레이드 요청에 대해 `HTTP 500` 내부 오류 상태 코드를 반환했기 때문에 클라이언트가 서비스에 연결할 수 없었습니다. |
| 시간 제한 | 클라이언트 연결 요청이 서비스에서의 응답 없이 시간 초과했습니다. *종료* 필드는 클라이언트가 시간 초과하여 연결 대기를 중단한 시간을 포함합니다. |
| ClientError | 클라이언트가 일부 내부 클라이언트 오류 때문에 연결을 종료했습니다. |

### <a name="metric-microphone"></a>메트릭 `Microphone`

`Microphone` 메트릭은 모든 음성 회차에 대해 필요합니다. 이 메트릭은 오디오 입력이 음성 요청에 대해 활발하게 사용되고 있는 동안 클라이언트에 대한 시간을 측정합니다.

클라이언트 애플리케이션에서 `Microphone` 메트릭에 대한 *시작* 시간 값을 기록하려면 다음 예제를 지침으로 사용하세요.

* 클라이언트 애플리케이션이 사용자가 실제 단추를 눌러 마이크를 시작해야 한다고 요구합니다. 단추를 누른 후 클라이언트 애플리케이션이 마이크에서 입력을 읽고 이를 음성 서비스에 보냅니다. `Microphone` 메트릭에 대한 *시작* 값은 단추를 누른 후 마이크가 초기화되고 입력을 제공할 준비가 되었을 때의 시간을 기록합니다. `Microphone` 메트릭에 대한 *종료* 값은 클라이언트 애플리케이션이 서비스에서 `speech.endDetected` 메시지를 수신한 후 서비스에 대한 오디오 스트리밍을 중단한 시간을 기록합니다.

* 클라이언트 애플리케이션은 "항상" 수신 대기하는 키워드 스포터(keyword spotter)를 사용합니다. 키워드 스포터가 말한 트리거 구를 검색한 후에만 클라이언트 애플리케이션이 마이크에서 입력을 수집하고 이를 음성 서비스에 보냅니다. `Microphone` 메트릭에 대한 *시작* 값은 키워드 스포터가 클라이언트에 대해 마이크에서 오는 입력을 사용하기 시작하라고 알린 시간을 기록합니다. `Microphone` 메트릭에 대한 *종료* 값은 클라이언트 애플리케이션이 서비스에서 `speech.endDetected` 메시지를 수신한 후 서비스에 대한 오디오 스트리밍을 중단한 시간을 기록합니다.

* 클라이언트 애플리케이션은 일정한 오디오 스트림에 대한 액세스 권한을 가지고 *음성 검색 모듈*의 해당 오디오 스트림에 대해 무음/음성 검색을 수행합니다. `Microphone` 메트릭에 대한 *시작* 값은 *음성 검색 모듈*이 클라이언트에 대해 오디오 스트림에서 오는 입력을 사용하기 시작하라고 알린 시간을 기록합니다. `Microphone` 메트릭에 대한 *종료* 값은 클라이언트 애플리케이션이 서비스에서 `speech.endDetected` 메시지를 수신한 후 서비스에 대한 오디오 스트리밍을 중단한 시간을 기록합니다.

* 클라이언트 애플리케이션이 다회차 요청의 2회차를 처리 중인데 서비스 응답 메시지에서 2회차에 대한 입력을 수집하기 위해 마이크를 켜라는 알림을 받습니다. `Microphone` 메트릭에 대한 *시작* 값은 클라이언트 애플리케이션이 마이크를 활성화하고 해당 오디오 소스에서 입력을 사용하기 시작하는 시간을 기록합니다. `Microphone` 메트릭에 대한 *종료* 값은 클라이언트 애플리케이션이 서비스에서 `speech.endDetected` 메시지를 수신한 후 서비스에 대한 오디오 스트리밍을 중단한 시간을 기록합니다.

`Microphone` 메트릭에 대한 *종료* 시간은 클라이언트 애플리케이션이 오디오 입력 스트리밍을 중단한 시간을 기록합니다. 대부분의 상황에서 이 이벤트는 클라이언트가 서비스에서 `speech.endDetected` 메시지를 수신한 직후에 발생합니다. 클라이언트 애플리케이션은 `Microphone` 메트릭에 대한 *종료* 시간 값이 `speech.endDetected` 메시지에 대한 수신 시간 값보다 이후에 발생하는지 확인하여 프로토콜을 적절히 준수했음을 확인할 수 있습니다. 그리고 일반적으로 한 회차의 종료와 다른 회차의 시작 간에 지연이 있으므로 클라이언트는 이후 회차에 대한 `Microphone` 메트릭의 *시작* 시간 값이 클라이언트가 마이크를 사용하여 오디오 입력을 서비스에 스트리밍하기 *시작한* 시간을 정확히 기록하는지 확인하여 프로토콜 적합성을 확인할 수 있습니다.

| 필드 | 설명 | 사용 현황 |
| ----- | ----------- | ----- |
| 이름 | 마이크 | 필수 |
| 시작 | 클라이언트가 마이크 또는 다른 오디오 스트림에서 오는 오디오 입력을 사용하기 시작했거나 키워드 스포터에서 트리거를 수신한 시간 | 필수 |
| 끝 | 클라이언트가 마이크 또는 오디오 스트림 사용을 중단한 시간 | 필수 |
| 오류 | 발생한 오류(있는 경우)에 대한 설명. 마이크 작동이 성공적인 경우 클라이언트는 이 필드를 생략하는 것이 좋습니다. 이 필드의 최대 길이는 50자입니다. | 오류의 경우 필수, 그렇지 않으면 생략됨 |

### <a name="metric-listeningtrigger"></a>메트릭 `ListeningTrigger`
`ListeningTrigger` 메트릭은 사용자가 음성 입력을 시작하는 작업을 실행하는 시간을 측정합니다. `ListeningTrigger` 메트릭은 선택적이지만 이 메트릭을 제공할 수 있는 클라이언트는 그렇게 하는 것이 좋습니다.

클라이언트 애플리케이션에서 `ListeningTrigger` 메트릭에 대한 *시작* 및 *종료* 시간 값을 기록하려면 다음 예제를 지침으로 사용하세요.

* 클라이언트 애플리케이션이 사용자가 실제 단추를 눌러 마이크를 시작해야 한다고 요구합니다. 이 메트릭에 대한 *시작* 값은 단추를 누른 시간을 기록합니다. *종료* 값은 단추 누르기를 마친 시간을 기록합니다.

* 클라이언트 애플리케이션은 "항상" 수신 대기하는 키워드 스포터(keyword spotter)를 사용합니다. 키워드 스포터가 말한 트리거 구를 검색한 후 클라이언트 애플리케이션은 마이크에서 입력을 읽고 이를 음성 서비스에 보냅니다. 이 메트릭에 대한 *시작* 값은 키워드 스포터가 나중에 트리거 구로 검색한 오디오를 수신한 시간을 기록합니다. *종료* 값은 트리거 구의 마지막 단어를 사용자가 말한 시간을 기록합니다.

* 클라이언트 애플리케이션은 일정한 오디오 스트림에 대한 액세스 권한을 가지고 *음성 검색 모듈*의 해당 오디오 스트림에 대해 무음/음성 검색을 수행합니다. 이 메트릭에 대한 *시작* 값은 *음성 검색 모듈*이 나중에 음성으로 검색된 오디오를 수신한 시간을 기록합니다. *종료* 값은 *음성 검색 모듈*이 음성을 검색한 시간을 기록합니다.

* 클라이언트 애플리케이션이 다회차 요청의 2회차를 처리 중인데 서비스 응답 메시지에서 2회차에 대한 입력을 수집하기 위해 마이크를 켜라는 알림을 받습니다. 클라이언트 애플리케이션은 이 회차에 대한 `ListeningTrigger` 메트릭을 포함하지 *않는 것이 좋습니다*.

| 필드 | 설명 | 사용 현황 |
| ----- | ----------- | ----- |
| 이름 | ListeningTrigger | 옵션 |
| 시작 | 클라이언트 수신 대기 트리거가 시작된 시간 | 필수 |
| 끝 | 클라이언트 수신 대기 트리거가 종료된 시간 | 필수 |
| 오류 | 발생한 오류(있는 경우)에 대한 설명. 트리거 작업이 성공한 경우 클라이언트는 이 필드를 생략하는 것이 좋습니다. 이 필드의 최대 길이는 50자입니다. | 오류의 경우 필수, 그렇지 않으면 생략됨 |

#### <a name="sample-message"></a>샘플 메시지

다음 샘플은 ReceivedMessages 부분과 메트릭 부분을 모두 포함한 원격 분석 메시지를 보여줍니다.

```HTML
Path: telemetry
Content-Type: application/json; charset=utf-8
X-RequestId: 123e4567e89b12d3a456426655440000
X-Timestamp: 2016-08-16T15:03:54.183Z

{
  "ReceivedMessages": [
    { "speech.hypothesis": [ "2016-08-16T15:03:48.171Z", "2016-08-16T15:03:48.331Z", "2016-08-16T15:03:48.881Z" ] },
    { "speech.endDetected": "2016-08-16T15:03:49.721Z" },
    { "speech.phrase": "2016-08-16T15:03:50.001Z" },
    { "turn.end": "2016-08-16T15:03:51.021Z" }
  ],
  "Metrics": [
    {
      "Name": "Connection",
      "Id": "A140CAF92F71469FA41C72C7B5849253",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:48.000Z",
    },
    {
      "Name": "ListeningTrigger",
      "Start": "2016-08-16T15:03:48.776Z",
      "End": "2016-08-16T15:03:48.777Z",
    },
    {
      "Name": "Microphone",
      "Start": "2016-08-16T15:03:47.921Z",
      "End": "2016-08-16T15:03:51.921Z",
    },
  ],
}
```

## <a name="error-handling"></a>오류 처리

이 섹션에서는 애플리케이션이 처리해야 하는 오류 메시지 및 조건의 종류를 설명합니다.

### <a name="http-status-codes"></a>HTTP 상태 코드

WebSocket 업그레이드 요청 중에 음성 서비스는 표준 HTTP 상태 코드(예: `400 Bad Request`) 등을 반환할 수 있습니다. 애플리케이션은 이러한 오류 조건을 올바르게 처리해야 합니다.

#### <a name="authorization-errors"></a>권한 부여 오류

WebSocket 업그레이드 중에 잘못된 인증이 제공된 경우 음성 서비스는 HTTP `403 Forbidden` 상태 코드를 반환합니다. 이 오류 코드를 트리거할 수 있는 조건:

* *인증* 헤더 누락

* 잘못된 인증 토큰

* 만료된 인증 토큰

`403 Forbidden` 오류 메시지는 음성 서비스의 문제를 나타내지 않습니다. 이 오류 메시지는 클라이언트 애플리케이션의 문제를 나타냅니다.

### <a name="protocol-violation-errors"></a>프로토콜 위반 오류

음성 서비스가 클라이언트에서 프로토콜 위반을 검색한 경우 서비스는 *상태 코드* 및 종료 *이유*를 반환한 후 WebSocket 연결을 종료합니다. 클라이언트 애플리케이션은 이 정보를 사용하여 위반을 추적하고 해결할 수 있습니다.

#### <a name="incorrect-message-format"></a>잘못된 메시지 형식

클라이언트가 이 사양에 나오는 올바른 형식으로 인코딩되지 않은 텍스트 또는 이진 메시지를 서비스에 보내면 서비스는 *1007 잘못된 페이로드 데이터* 상태 코드와 함께 연결을 닫습니다.

서비스는 다음 예제와 같이 다양한 이유로 이 상태 코드를 반환합니다.

* "잘못된 메시지 형식. 이진 메시지가 잘못된 헤더 크기 접두사를 포함합니다." 클라이언트가 잘못된 헤더 크기 접두사를 포함하는 이진 메시지를 보냈습니다.

* "잘못된 메시지 형식. 이진 메시지의 헤더 크기가 잘못되었습니다." 클라이언트가 잘못된 헤더 크기를 지정한 이진 메시지를 보냈습니다.

* "잘못된 메시지 형식. UTF-8로 이진 메시지 헤더 디코딩이 실패했습니다." 클라이언트가 UTF-8로 올바르게 인코딩되지 않은 헤더를 포함하는 이진 메시지를 보냈습니다.

* "잘못된 메시지 형식. 텍스트 메시지가 데이터를 포함하지 않습니다." 클라이언트가 본문 데이터를 포함하지 않은 텍스트 메시지를 보냈습니다.

* "잘못된 메시지 형식. UTF-8로 텍스트 메시지 디코잉이 실패했습니다." 클라이언트가 UTF-8로 올바르게 인코딩되지 않은 텍스트 메시지를 보냈습니다.

* "잘못된 메시지 형식. 텍스트 메시지가 헤더 구분 기호를 포함하지 않습니다." 클라이언트가 헤더 구분 기호를 포함하지 않았거나 잘못된 헤더 구분 기호를 사용한 텍스트 메시지를 보냈습니다.

#### <a name="missing-or-empty-headers"></a>누락 또는 빈 헤더

클라이언트가 필수 헤더 *X-RequestId* 또는 *경로*를 포함하지 않은 메시지를 보내면 서비스는 *1002 프로토콜 오류* 상태 코드와 함께 연결을 닫습니다. 메시지는 "누락/빈 헤더. {헤더 이름}"입니다.

#### <a name="requestid-values"></a>RequestId 값

클라이언트가 *X-RequestId* 헤더를 잘못된 형식으로 지정하는 메시지를 보내는 경우 서비스는 연결을 닫고 *1002 프로토콜 오류* 상태를 반환합니다. 메시지는 "잘못된 요청. X-RequestId 헤더 값을 대시 없는 UUID 형식으로 지정하지 않았습니다."

#### <a name="audio-encoding-errors"></a>오디오 인코딩 오류

클라이언트가 회차를 시작하는 오디오 청크를 보내는데 오디오 형식 또는 인코딩이 필요한 사양을 준수하지 않는 경우 서비스는 연결을 닫고 *1007 잘못된 페이로드 데이터* 상태 코드를 반환합니다. 이 메시지는 형식 인코딩 오류 원본을 나타냅니다.

#### <a name="requestid-reuse"></a>RequestId 다시 사용

회차가 종료된 후 클라이언트가 해당 회차에서 요청 식별자를 다시 사용하는 메시지를 보내면 서비스는 연결을 닫고 *1002 프로토콜 오류* 상태 코드를 반환합니다. 메시지는 "잘못된 요청. 요청 식별자의 다시 사용은 허용되지 않습니다."

## <a name="connection-failure-telemetry"></a>연결 실패 원격 분석

가장 좋은 예상 사용자 경험을 확보하려면 클라이언트는 *원격 분석* 메시지를 사용하여 연결 내의 중요 검사점에 대한 타임스탬프를 음성 서비스에 알려야 합니다. 클라이언트가 시도했지만 실패한 연결을 서비스에 알리는 것도 마찬가지로 중요합니다.

실패한 각 연결 시도에 대해 클라이언트는 고유한 *X-RequestId* 헤더 값을 사용하여 *원격 분석* 메시지를 만듭니다. 클라이언트는 연결을 설정할 수 없으므로 JSON 본문의 *ReceivedMessages* 필드는 생략할 수 있습니다. *메트릭* 필드에 `Connection` 항목만 포함하면 됩니다. 이 항목은 발견된 오류 조건뿐만 아니라 시작 및 종료 타임스탬프도 포함합니다.

### <a name="connection-retries-in-telemetry"></a>원격 분석의 연결 다시 시도

클라이언트는 연결 시도를 트리거하는 이벤트에 의해 *다시 시도*를 *여러 번 연결 시도*와 구분할 수 있습니다. 사용자 입력 없이 프로그램에 의해 수행된 연결 시도는 다시 시도입니다. 사용자 입력에 응답하여 수행하는 여러 번의 연결 시도는 여러 번 연결 시도입니다. 클라이언트가 각 사용자 트리거 연결 시도에 대해 고유한 *X-RequestId* 및 *원격 분석* 메시지를 제공합니다. 클라이언트가 프로그램에 의한 다시 시도에 대해 *X-RequestId*를 다시 사용합니다. 단일 연결 시도에 대해 여러 번 다시 시도가 이루어진 경우 각 다시 시도는 *원격 분석* 메시지에 `Connection` 항목으로 포함됩니다.

예를 들어 사용자가 연결을 시작하는 키워드 트리거를 말하고 첫 번째 연결 시도가 DNS 오류 때문에 실패한다고 가정해 보겠습니다. 그러나 클라이언트에서 프로그램에 의해 수행한 두 번째 시도는 성공합니다. 클라이언트가 사용자의 추가 입력을 요구하지 않고 연결을 다시 시도했으므로 클라이언트는 `Connection` 항목 여러 개와 함께 단일 *원격 분석* 메시지를 사용하여 연결을 설명합니다.

또 다른 예로, 사용자가 연결을 시작하는 키워드 트리거를 말하는데 이 연결 시도가 세 번 다시 시도 후 실패한다고 가정해 보겠습니다. 그러면 클라이언트는 포기하고 서비스에 연결 시도를 중단하고 사용자에게 무언가 잘못되었다고 알립니다. 그러면 사용자가 키워드 트리거를 다시 말합니다. 이번에는 클라이언트가 서비스에 연결한다고 가정해 보겠습니다. 연결 후 클라이언트는 즉시 연결 실패를 설명하는 `Connection` 항목 세 개를 포함하는 *원격 분석* 메시지를 서비스에 보냅니다. `turn.end` 메시지를 수신한 후 클라이언트는 연결 성공을 설명하는 또 다른 *원격 분석* 메시지를 보냅니다.

## <a name="error-message-reference"></a>오류 메시지 참조

### <a name="http-status-codes"></a>HTTP 상태 코드

| HTTP 상태 코드 | 설명 | 문제 해결 |
| - | - | - |
| 400 잘못된 요청 | 클라이언트가 잘못된 WebSocket 연결 요청을 보냈습니다. | 모든 필수 매개 변수 및 HTTP 헤더를 제공했는지 그리고 값이 올바른지 확인합니다. |
| 401 권한 없음 | 클라이언트가 필수 권한 정보를 포함하지 않았습니다. | WebSocket 연결의 *권한* 헤더를 보냈는지 확인합니다. |
| 403 사용할 수 없음 | 클라이언트가 권한 정보를 보냈지만 해당 정보가 잘못되었습니다. | *권한* 헤더에 만료되거나 잘못된 값을 보내고 있지 않은지 확인합니다. |
| 404 찾을 수 없음 | 클라이언트가 지원되지 않는 URL 경로에 액세스를 시도했습니다. | WebSocket 연결에 올바른 URL을 사용하고 있는지 확인합니다. |
| 500 서버 오류 | 서버가 내부 오류를 발견했으며 요청을 수행할 수 없습니다. | 대부분의 경우 이 오류는 과도적입니다. 요청을 다시 시도하십시오. |
| 503 서비스를 사용할 수 없음 | 서비스가 요청을 처리할 수 없었습니다. | 대부분의 경우 이 오류는 과도적입니다. 요청을 다시 시도하십시오. |

### <a name="websocket-error-codes"></a>WebSocket 오류 코드

| WebSocketsStatus 코드 | 설명 | 문제 해결 |
| - | - | - |
| 1000 기본 닫기 | 서비스가 WebSocket 연결을 오류 없이 닫았습니다. | WebSocket 닫기가 예기치 않은 경우 설명서를 다시 읽고 서비스가 WebSocket 연결을 종료할 수 있는 방법 및 경우를 확인하세요. |
| 1002 프로토콜 오류 | 클라이언트가 프로토콜 요구 사항을 준수하지 않았습니다. | 프로토콜 설명서를 이해하고 있는지 그리고 요구 사항에 대해 명확히 알고 있는지 확인합니다. 오류 이유에 대한 이전 설명서를 읽고 프로토콜 요구 사항을 위반하지 않는지 확인합니다. |
| 1007 잘못된 페이로드 데이터 | 클라이언트가 프로토콜 메시지에 잘못된 페이로드를 보냈습니다. | 서비스에 보낸 마지막 메시지에 오류가 있는지 확인합니다. 페이로드 오류에 관한 이전 설명서를 읽습니다. |
| 1011 서버 오류 | 서버가 내부 오류를 발견했으며 요청을 수행할 수 없습니다. | 대부분의 경우 이 오류는 과도적입니다. 요청을 다시 시도하십시오. |

## <a name="related-topics"></a>관련된 항목

WebSocket 기반 음성 서비스 프로토콜의 구현인 [JavaScript SDK](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)를 참조하세요.
