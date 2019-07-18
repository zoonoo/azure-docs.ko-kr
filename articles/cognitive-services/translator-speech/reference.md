---
title: Translator Speech API 참조
titleSuffix: Azure Cognitive Services
description: Translator Speech API에 대한 참조 문서입니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: reference
ms.date: 05/18/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0f083a6ca3079128aad4aba3a53013df378a6106
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446898"
---
# <a name="translator-speech-api"></a>Translator Speech API

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

이 서비스는 한 언어의 대화 음성을 다른 언어의 텍스트로 기록하기 위한 스트리밍 API를 제공합니다. API는 또한 번역된 된 텍스트를 다시 사용 하는 텍스트 음성 변환 기능을 통합 합니다. Translator Speech API는 Skype Translator에서 볼 수 있는 것처럼 실시간 대화 번역과 같은 시나리오도 허용합니다.

Translator Speech API를 사용하여 클라이언트 애플리케이션은 음성 오디오를 서비스로 스트리밍하고 텍스트 기반의 결과를 돌려받습니다. 여기에는 인식된 텍스트를 소스 언어로, 해당 번역을 대상 언어로 포함합니다. 텍스트 결과는 심층 신경망에 의해 작동하는 ASR(Automatic Speech Recognition)을 들어오는 오디오 스트리밍에 적용하여 생성합니다. 원시 ASR 출력은 사용자 의도를 더 밀접하게 반영하기 위해 TrueText라는 새 기술로 더욱 개선되었습니다. 예를 들어 TrueText는 말더듬(으흠 및 콜록) 표현을 제거하고, 적절한 구두점 및 대문자 사용을 복원합니다. 비속어를 제외하거나 마스킹하는 기능도 포함됩니다. 인식 및 번역 엔진은 특히 대화형 음성 처리를 학습합니다. 음성 번역 서비스는 무음 감지 기능을 사용하여 발언의 마지막을 확인합니다. 음성 활동을 잠시 멈춘 뒤 서비스는 완성된 발언에 대한 최종 결과를 다시 스트리밍합니다. 서비스는 부분 결과를 돌려보내 진행 중인 발언에 대한 중간 인식과 번역을 제공할 수도 있습니다. 최종 결과를 위해 이 서비스는 대상 언어로 구어체 텍스트에서 음성을 합성하는 기능(텍스트를 음성으로 변환)을 제공합니다. 텍스트를 음성으로 변환하는 오디오는 클라이언트에서 지정한 형식으로 생성됩니다. WAV 및 MP3 형식을 사용할 수 있습니다.

Translator Speech API는 WebSocket 프로토콜을 활용하여 클라이언트 및 서버 간 전이중 통신 채널을 제공합니다. 애플리케이션은 이 서비스를 사용하기 위해 다음 단계를 거쳐야 합니다.

## <a name="1-getting-started"></a>1. 시작
Translator Text API에 액세스하려면 [Microsoft Azure에 등록](translator-speech-how-to-signup.md)해야 합니다.

## <a name="2-authentication"></a>2. Authentication

등록 키를 사용하여 인증을 받습니다. Translator Speech API는 다음 두 가지 인증 모드를 지원합니다.

* **액세스 토큰 사용:** 애플리케이션에서 토큰 서비스의 액세스 토큰을 가져옵니다. Translator Speech API 구독 키를 사용하여 Azure Cognitive Services 인증 서비스에서 액세스 토큰을 가져옵니다. 액세스 토큰은 10분 동안 유효합니다. 10분 간격으로 새 액세스 토큰을 획득하고, 이러한 10분 내에 반복되는 요청에 대해 동일한 액세스 토큰을 계속 사용합니다.

* **구독 키 직접 사용:** 애플리케이션에서 구독 키를 `Ocp-Apim-Subscription-Key` 헤더에 값으로 제공합니다.

구독 키와 액세스 토큰을 보이지 않게 숨겨야 하는 비밀로 취급합니다.

## <a name="3-query-languages"></a>3. 쿼리 언어
**지원되는 언어의 현재 집합에서 언어 리소스를 쿼리합니다.** [언어 리소스](languages-reference.md)는 음성 인식, 텍스트 번역 및 텍스트 음성 변환에 사용할 수 있는 언어 집합을 노출합니다. 각 언어 또는 음성에 Translator Speech API가 같은 언어 또는 음성을 식별하는 데 사용하는 식별자가 제공됩니다.

## <a name="4-stream-audio"></a>4. 오디오 스트리밍
**연결을 열고 서비스에 대한 오디오 스트리밍을 시작합니다.** 서비스 URL은 `wss://dev.microsofttranslator.com/speech/translate`입니다. 매개 변수 및 서비스에 필요한 오디오 형식은 아래의 `/speech/translate` 작업에 설명되어 있습니다. 매개 변수 중 하나는 위 2 단계의 액세스 토큰을 전달하는 데 사용됩니다.

## <a name="5-process-the-results"></a>5. 결과 처리
**서비스에서 다시 스트리밍된 결과를 처리합니다.** 부분 결과, 최종 결과 및 텍스트 음성 변환 오디오 세그먼트의 형식은 아래 `/speech/translate` 작업의 설명서에 설명되어 있습니다.

Translator Speech API의 사용 방식을 보여 주는 코드 샘플은 [Microsoft Translator GitHub 사이트](https://github.com/MicrosoftTranslator)에서 사용할 수 있습니다.

## <a name="implementation-notes"></a>구현 참고 사항

GET /speech/translate: 음성 번역에 대한 세션을 설정합니다.

### <a name="connecting"></a>Connecting
서비스에 연결하기 전에 이 섹션의 뒷부분에 제공된 매개 변수 목록을 검토합니다. 예제 요청은 다음과 같습니다.

`GET wss://dev.microsofttranslator.com/speech/translate?from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa&api-version=1.0`
`Ocp-Apim-Subscription-Key: {subscription key}`
`X-ClientTraceId: {GUID}`

요청은 영어 음성이 서비스로 스트리밍된 후 이탈리아어로 번역되도록 지정합니다. 각 최종 인식 결과는 Elsa라는 여성 음성을 사용한 텍스트 음성 변환 오디오 응답으로 생성됩니다. 요청의 `Ocp-Apim-Subscription-Key header`에는 자격 증명이 포함되어 있습니다. 또한 이 요청은 헤더 `X-ClientTraceId`에 전역 고유 식별자를 설정하여 모범 사례를 따릅니다. 클라이언트 애플리케이션은 문제가 발생할 때 해결하는 데 사용할 수 있도록 추적 ID를 로깅해야 합니다.

### <a name="sending-audio"></a>오디오 보내기
연결이 설정되면 클라이언트는 서비스로의 오디오 스트리밍을 시작합니다. 클라이언트는 청크 단위로 오디오를 보냅니다. 각 청크는 이진 유형의 Websocket 메시지를 사용하여 전송됩니다.

오디오 입력은 Waveform 오디오 파일 형식(WAVE 또는 파일 이름 확장명 때문에 WAV로도 더 많이 알려져 있음)을 갖습니다. 클라이언트 애플리케이션은 16kHz에서 샘플링된 단일 채널의 부호 있는 16비트 PCM 오디오를 스트리밍해야 합니다. 클라이언트에서 스트리밍되는 첫 번째 바이트 집합에는 WAV 헤더가 포함됩니다. 16kHz에서 샘플링된 단일 채널의 부호 있는 16비트 PCM 스트림에 대한 44바이트 헤더는 다음과 같습니다.

|Offset|값|
|:---|:---|
|0 - 3|"RIFF"|
|4 - 7|0|
|8 - 11|"WAVE"|
|12 - 15|"fmt"|
|16 - 19|16|
|20 - 21|1|
|22 - 23|1|
|24 - 27|16000|
|28 - 31|32000|
|32 - 33|2|
|34 - 35|16|
|36 - 39|"data"|
|40 - 43|0|

총 파일 크기(4-7바이트) 및 "data" 크기(40-43바이트)가 0으로 설정됩니다. 이것은 총 크기를 반드시 미리 알아야 할 필요는 없는 스트리밍 시나리오에서는 문제가 되지 않습니다.

클라이언트는 WAV(RIFF) 헤더를 보낸 후 오디오 데이터의 청크를 보냅니다. 클라이언트는 일반적으로 고정 기간을 나타내는 고정된 크기의 청크를 스트리밍합니다(예: 한 번에 100ms의 오디오 스트리밍).

### <a name="signal-the-end-of-the-utterance"></a>발언의 마지막 신호
Translator Speech API는 오디오를 전송할 때 오디오 스트림의 대본과 번역을 반환합니다. 최종 대본, 최종 번역 및 번역된 오디오는 발언이 끝난 후에만 반환됩니다. 일부 경우에는 발언을 강제로 종료하려고 할 수 있습니다. 발언을 강제로 종료하려면 2.5초 동안 무음을 보내 주세요.

### <a name="final-result"></a>최종 결과
최종 음성 인식 결과는 발언의 끝에 생성됩니다. 결과는 Text 형식의 WebSocket 메시지를 사용하여 서비스에서 클라이언트로 전송됩니다. 메시지 콘텐츠는 다음 속성을 갖는 개체의 JSON 직렬화입니다.

* `type`: 결과의 형식을 식별하기 위한 문자열 상수입니다. 이 값은 최종 결과를 위한 최종 값입니다.
* `id`: 인식 결과에 할당되는 문자열 식별자입니다.
* `recognition`: 원본 언어의 인식된 텍스트입니다. 이 텍스트는 잘못 인식될 경우 빈 문자열일 수 있습니다.
* `translation`: 대상 언어로 번역된 인식된 텍스트입니다.
* `audioTimeOffset`: 인식이 시작된 시간 오프셋을 틱 단위로 나타낸 것입니다(1개 틱 = 100나노초). 이 오프셋은 스트리밍의 시작 부분에 상대적입니다.
* `audioTimeSize`: 인식 기간(틱 단위=100나노초)입니다.
* `audioStreamPosition`: 인식 시작의 바이트 오프셋입니다. 이 오프셋은 스트리밍의 시작 부분에 상대적입니다.
* `audioSizeBytes`: 인식의 크기(바이트)입니다.

오디오 스트림에서 인식의 위치는 기본적으로 결과에 포함되지 않습니다. `TimingInfo` 기능은 클라이언트가 선택해야 합니다(`features` 매개 변수 참조).

샘플 최종 결과는 다음과 같습니다.

```
{
  type: "final"
  id: "23",
  recognition: "what was said",
  translation: "translation of what was said",
  audioStreamPosition: 319680,
  audioSizeBytes: 35840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 21900000
}
```

### <a name="partial-result"></a>부분 결과
부분 또는 중간 음성 인식 결과는 기본적으로 클라이언트로 스트리밍되지 않습니다. 클라이언트는 features 쿼리 매개 변수를 사용하여 이러한 결과를 요청할 수 있습니다.

부분 결과는 Text 형식의 WebSocket 메시지를 사용하여 서비스에서 클라이언트로 전송됩니다. 메시지 콘텐츠는 다음 속성을 갖는 개체의 JSON 직렬화입니다.

* `type`: 결과의 형식을 식별하기 위한 문자열 상수입니다. 이 값은 부분 결과에 대한 부분적인 값입니다.
* `id`: 인식 결과에 할당되는 문자열 식별자입니다.
* `recognition`: 원본 언어의 인식된 텍스트입니다.
* `translation`: 대상 언어로 번역된 인식된 텍스트입니다.
* `audioTimeOffset`: 인식이 시작된 시간 오프셋을 틱 단위로 나타낸 것입니다(1개 틱 = 100나노초). 이 오프셋은 스트리밍의 시작 부분에 상대적입니다.
* `audioTimeSize`: 인식 기간(틱 단위=100나노초)입니다.
* `audioStreamPosition`: 인식 시작의 바이트 오프셋입니다. 이 오프셋은 스트리밍의 시작 부분에 상대적입니다.
* `audioSizeBytes`: 인식의 크기(바이트)입니다.

오디오 스트림에서 인식의 위치는 기본적으로 결과에 포함되지 않습니다. TimingInfo 기능은 클라이언트가 선택해야 합니다(features 매개 변수 참조).

샘플 최종 결과는 다음과 같습니다.

```
{
  type: "partial"
  id: "23.2",
  recognition: "what was",
  translation: "translation of what was",
  audioStreamPosition: 319680,
  audioSizeBytes: 25840,
  audioTimeOffset: 2731600000,
  audioTimeSize: 11900000
}
```

### <a name="text-to-speech"></a>텍스트 음성 변환
텍스트 음성 변환 기능이 사용되도록 설정되면(아래의 `features` 매개 변수 참조) 최종 결과 다음에, 번역된 텍스트의 음성 오디오가 나옵니다. 오디오 데이터는 청크로 구성되며 Binary 형식의 Websocket 메시지 시퀀스로서 서비스에서 클라이언트로 전송됩니다. 클라이언트는 각 메시지의 FIN 비트를 확인하여 스트림 끝을 검색할 수 있습니다. 마지막 이진 메시지의 FIN 비트는 스트림의 끝임을 나타내기 위해 1로 설정됩니다. 스트림 형식은 `format` 매개 변수의 값에 따라 다릅니다.

### <a name="closing-the-connection"></a>연결 닫기
클라이언트 애플리케이션은 오디오 스트리밍을 완료하고 마지막 최종 결과를 수신하면 WebSocket 닫기 핸드셰이크를 시작하여 연결을 닫아야 합니다. 서버가 연결을 종료하는 상황도 있을 수 있습니다. 클라이언트에는 다음과 같은 WebSocket 종료 코드가 수신될 수 있습니다.

* `1003 - Invalid Message Type`: 서버가 수신된 데이터 형식을 수락할 수 없으므로 연결을 종료합니다. 이 문제는 들어오는 오디오가 적절한 헤더로 시작되지 않을 때 일반적으로 발생합니다.
* `1000 - Normal closure`: 요청이 처리된 후 연결이 닫혔습니다. 서버는 장기간 클라이언트에서 오디오가 수신되지 않을 때, 장기간 무음이 스트리밍될 때, 세션이 허용되는 최대 기간(약 90분)에 도달할 때 연결을 닫습니다.
* `1001 - Endpoint Unavailable`: 서버를 사용할 수 없음을 나타냅니다. 클라이언트 애플리케이션은 제한된 재시도 횟수에 따라 다시 연결하려고 시도할 수 있습니다.
* `1011 - Internal Server Error`: 서버 오류로 인해 서버에서 연결이 닫힙니다.

### <a name="parameters"></a>매개 변수

|매개 변수|값|설명|매개 변수 형식|데이터 형식|
|:---|:---|:---|:---|:---|
|api-version|1.0|클라이언트에서 요청한 API 버전입니다. 허용되는 값은 `1.0`입니다.|query   |문자열|
|from|(empty)   |들어오는 음성의 언어를 지정합니다. 이 값은 Languages API의 응답에서 `speech` 범위의 언어 식별자 중 하나입니다.|query|문자열|
|to|(empty)|기록된 텍스트를 번역할 언어를 지정합니다. 이 값은 Languages API의 응답에서 `text` 범위의 언어 식별자 중 하나입니다.|query|문자열|
|기능|(empty)   |클라이언트가 선택한 쉼표로 구분된 기능 집합입니다. 사용 가능한 기능은 다음과 같습니다.<ul><li>`TextToSpeech`: 서비스가 번역된 최종 문장의 번역된 오디오를 반환해야 함을 지정합니다.</li><li>`Partial`: 서비스가 오디오가 스트리밍되는 동안 중간 인식 결과를 반환해야 함을 지정합니다.</li><li>`TimingInfo`: 서비스가 각 인식과 연결된 타이밍 정보를 반환해야 함을 지정합니다.</li></ul>예를 들어, 클라이언트는 타이밍 정보 없이 부분 결과 및 텍스트 음성 변환을 수신하도록 `features=partial,texttospeech`를 지정합니다. 최종 결과는 항상 클라이언트로 스트리밍됩니다.|query|문자열|
|voice|(empty)|번역된 텍스트의 텍스트 음성 변환 렌더링에 사용할 음성을 식별합니다. 이 값은 Languages API의 응답에서 tts 범위의 음성 식별자 중 하나입니다. 음성을 지정하지 않으면 시스템은 텍스트 음성 변환 기능이 사용되도록 설정될 경우 음성 하나를 자동으로 선택합니다.|query|문자열|
|format|(empty)|서비스에서 반환되는 텍스트 음성 변환 오디오 스트림의 형식을 지정합니다. 사용 가능한 옵션은 다음과 같습니다.<ul><li>`audio/wav`: 파형 오디오 스트림. 클라이언트는 오디오 형식을 적절하게 해석하기 위해 WAV 헤더를 사용해야 합니다. 텍스트 음성 변환을 위한 WAV 오디오는 샘플링 주기가 24kHz 또는 16kHz인 16비트, 단일 채널 PCM입니다.</li><li>`audio/mp3`: MP3 오디오 스트림.</li></ul>기본값은 `audio/wav`입니다.|query|문자열|
|ProfanityAction    |(empty)    |서비스가 음성에서 인식된 욕설을 처리하는 방식을 지정합니다. 유효한 작업은 다음과 같습니다.<ul><li>`NoAction`: 욕설을 있는 그대로 둡니다.</li><li>`Marked`: 욕설을 표식으로 바꿉니다. `ProfanityMarker` 매개 변수를 참조하세요.</li><li>`Deleted`: 욕설이 삭제됩니다. 예를 들어, 단어 `"jackass"`가 욕설로 취급되면 구 `"He is a jackass."`는 `"He is a .".`가 됩니다.</li></ul>기본값은 Marked입니다.|query|문자열|
|ProfanityMarker|(empty)    |`ProfanityAction`이 `Marked`로 설정될 때 검색된 욕설이 처리되는 방식을 지정합니다. 유효한 옵션은 다음과 같습니다.<ul><li>`Asterisk`: 욕설을 문자열 `***`로 바꿉니다. 예를 들어, 단어 `"jackass"`가 욕설로 취급되면 구 `"He is a jackass."`는 `"He is a ***.".`가 됩니다.</li><li>`Tag`: 욕설을 profanity XML 태그로 묶습니다. 예를 들어, 단어 `"jackass"`가 욕설로 취급되면 구 `"He is a jackass."`는 `"He is a <profanity>jackass</profanity>."`가 됩니다.</li></ul>기본값은 `Asterisk`입니다.|query|문자열|
|권한 부여|(empty)  |클라이언트의 전달자 토큰 값을 지정합니다. 접두사 `Bearer`와 인증 토큰 서비스에서 반환된 `access_token` 값을 차례로 사용합니다.|머리글   |문자열|
|Ocp-Apim-Subscription-Key|(empty)|`Authorization` 헤더를 지정하지 않은 경우 필수입니다.|머리글|문자열|
|access_token|(empty)   |유효한 OAuth 액세스 토큰을 전달하는 대체 방법입니다. 전달자 토큰은 일반적으로 헤더 `Authorization`과 함께 제공됩니다. 일부 websocket 라이브러리는 클라이언트 코드가 헤더를 설정하도록 허용하지 않습니다. 이러한 경우, 클라이언트는 `access_token` 쿼리 매개 변수를 사용하여 유효한 토큰을 전달할 수 있습니다. 액세스 토큰을 사용하여 인증을 받으려는 경우 `Authorization` 헤더를 설정하지 않았으면 `access_token`을 설정해야 합니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다. 클라이언트는 하나의 방법만 사용해서 토큰을 전달합니다.|query|문자열|
|구독 키|(empty)   |구독 키를 전달할 수 있는 대체 방법입니다. 일부 websocket 라이브러리는 클라이언트 코드가 헤더를 설정하도록 허용하지 않습니다. 이러한 경우, 클라이언트는 `subscription-key` 쿼리 매개 변수를 사용하여 유효한 구독 키를 전달할 수 있습니다. 구독 키를 사용하여 인증을 받으려는 경우 `Ocp-Apim-Subscription-Key` 헤더를 설정하지 않았으면 구독 키를 설정해야 합니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다. 클라이언트는 하나의 방법만 사용해서 `subscription key`를 전달합니다.|query|문자열|
|X-ClientTraceId    |(empty)    |요청을 추적하는 데 사용되는 클라이언트 생성 GUID입니다. 적절한 문제 해결을 위해, 클라이언트는 각 요청에 새 값을 제공하고 로깅해야 합니다.<br/>헤더를 사용하는 대신, 이 값을 쿼리 매개 변수 `X-ClientTraceId`에 전달할 수 있습니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다.|머리글|문자열|
|X-CorrelationId|(empty)    |대화에서 여러 채널 간 상관 관계를 지정하는 데 사용하는 클라이언트 생성 식별자입니다. 사용자 간에 대화를 사용하도록 설정하기 위해 여러 음성 번역 세션을 만들 수 있습니다. 이러한 시나리오에서는 모든 음성 번역 세션은 동일한 상관 관계 ID를 사용하여 채널을 함께 연결합니다. 이를 통해 추적 및 진단이 용이해집니다. 식별자는 `^[a-zA-Z0-9-_.]{1,64}$`를 준수해야 합니다.<br/>헤더를 사용하는 대신, 이 값을 쿼리 매개 변수 `X-CorrelationId`에 전달할 수 있습니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다.|머리글|문자열|
|X-ClientVersion|(empty)    |클라이언트 애플리케이션의 버전을 식별합니다. 예제: "2.1.0.123".<br/>헤더를 사용하는 대신, 이 값을 쿼리 매개 변수 `X-ClientVersion`에 전달할 수 있습니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다.|머리글|문자열|
|X-OsPlatform|(empty)   |클라이언트 애플리케이션에서 실행되는 운영 체제의 이름 및 버전을 식별합니다. 예제: "Android 5.0", "iOs 8.1.3", "Windows 8.1".<br/>헤더를 사용하는 대신, 이 값을 쿼리 매개 변수 `X-OsPlatform`에 전달할 수 있습니다. 헤더와 쿼리 매개 변수를 둘 다 설정한 경우 쿼리 매개 변수는 무시됩니다.|머리글|문자열|

### <a name="response-messages"></a>응답 메시지

|HTTP 상태 코드|이유|응답 모델|헤더|
|:--|:--|:--|:--|
|101    |WebSocket 업그레이드.|모델 예제 값 <br/> 개체 {}|X-RequestId<br/>문제 해결을 위해 요청을 식별하는 값입니다.<br/>문자열|
|400    |잘못된 요청입니다. 입력 매개 변수가 유효한지 확인합니다. 응답 개체에는 좀 더 자세한 오류 설명이 포함되어 있습니다.|||
|401    |권한이 없습니다. 자격 증명이 설정되어 있는지, 유효한지, Azure Data Market 구독의 사용 가능한 잔액이 충분한지 확인합니다.|||
|500    |오류가 발생했습니다. 오류가 계속 발생하는 경우 클라이언트 추적 식별자(X-ClientTraceId) 또는 요청 식별자(X-RequestId)를 사용하여 보고하세요.|||
|503    |서버를 일시적으로 사용할 수 없습니다. 요청을 다시 시도하세요. 오류가 계속 발생하는 경우 클라이언트 추적 식별자(X-ClientTraceId) 또는 요청 식별자(X-RequestId)를 사용하여 보고하세요.|||
