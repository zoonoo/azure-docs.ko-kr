---
title: 텍스트 음성 변환 컨테이너 엔드포인트 쿼리
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622258"
---
컨테이너는 [REST 기반 엔드포인트 API](../rest-text-to-speech.md)를 제공합니다. 플랫폼, 프레임워크 및 사용 가능한 언어 변형에 대한 많은 [샘플 소스 코드 프로젝트](https://github.com/Azure-Samples/Cognitive-Speech-TTS)가 있습니다.

표준 또는 인공신경망 텍스트 음성 변환 컨테이너를 사용하여 다운로드한 이미지 태그의 로캘과 음성을 사용해야 합니다. 예를 들어 `latest` 태그를 다운로드한 경우 기본 로캘은 `en-US` 및 `AriaNeural` 음성입니다. 그러면 `{VOICE_NAME}` 인수는 [`en-US-AriaNeural`](../language-support.md#neural-voices)가 됩니다. 아래 SSML 예제를 참조하세요.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

그러나 *사용자 지정 텍스트 음성 변환* 의 경우 [사용자 지정 음성 포털](https://aka.ms/custom-voice-portal)에서 **음성/모델** 을 가져와야 합니다. 사용자 지정 모델 이름은 음성 이름과 동의어입니다. **학습** 페이지로 이동하여 `{VOICE_NAME}` 인수로 사용할 **음성/모델** 을 복사합니다.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="사용자 지정 음성 모델 - 음성 이름":::

아래 SSML 예제를 참조하세요.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

몇 개의 헤더와 데이터 페이로드를 제공하는 HTTP POST 요청을 구성해 보겠습니다. `{VOICE_NAME}` 자리 표시자를 고유한 값으로 바꿉니다.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


이 명령은 다음을 수행합니다.

* `speech/synthesize/cognitiveservices/v1` 엔드포인트에 대한 HTTP POST 요청을 생성합니다.
* `audio/*`의 `Accept` 헤더를 지정합니다.
* `application/ssml+xml`의 `Content-Type` 헤더를 지정합니다. 자세한 내용은 [요청 본문](../rest-text-to-speech.md#request-body)을 참조하세요.
* `riff-16khz-16bit-mono-pcm`의 `X-Microsoft-OutputFormat` 헤더를 지정합니다. 추가 옵션은 [오디오 출력](../rest-text-to-speech.md#audio-outputs)을 참조하세요.
* `{VOICE_NAME}`이 지정된 [SSML(peech Synthesis Markup Language)](../speech-synthesis-markup.md) 요청을 엔드포인트에 보냅니다.
