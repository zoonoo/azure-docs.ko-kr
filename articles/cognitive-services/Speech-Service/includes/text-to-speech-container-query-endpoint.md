---
title: 텍스트 음성 변환 컨테이너 끝점 쿼리
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "73491118"
---
컨테이너는 [REST 기반 엔드포인트 API를](../rest-text-to-speech.md)제공합니다. 플랫폼, 프레임워크 및 언어 변형에 대한 많은 [샘플 소스 코드 프로젝트를](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) 사용할 수 있습니다.

표준 *텍스트 음성 변환* 컨테이너를 사용하면 다운로드한 이미지 태그의 로캘과 음성에 의존해야 합니다. 예를 들어 태그를 다운로드한 `latest` 경우 기본 `en-US` 로캘과 음성입니다. `JessaRUS` 그런 `{VOICE_NAME}` 다음 인수는 . [`en-US-JessaRUS`](../language-support.md#standard-voices) 아래 의 예제 SSML을 참조하십시오.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

그러나 *사용자 지정 텍스트 음성 변환의* 경우 사용자 지정 음성 [포털에서](https://aka.ms/custom-voice-portal) **음성/모델을** 가져와야 합니다. 사용자 지정 모델 이름은 음성 이름과 동의어입니다. **학습** 페이지로 이동하여 `{VOICE_NAME}` **음성/모델을** 복사하여 인수로 사용합니다.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="사용자 정의 음성 모델 - 음성 이름":::

아래 의 예제 SSML을 참조하십시오.

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

몇 가지 헤더와 데이터 페이로드를 제공하는 HTTP POST 요청을 구성해 보겠습니다. 자리 `{VOICE_NAME}` 표시자를 사용자 고유의 값으로 바꿉습니다.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

이 명령은 다음을 수행합니다.

* `speech/synthesize/cognitiveservices/v1` 끝점에 대한 HTTP POST 요청을 생성합니다.
* 헤더를 지정합니다. `Accept``audio/*`
* 에 대한 `Content-Type` 자세한 `application/ssml+xml`내용은 [요청 본문](../rest-text-to-speech.md#request-body)참조의 헤더를 지정합니다.
* 더 많은 `X-Microsoft-OutputFormat` 옵션은 `riff-16khz-16bit-mono-pcm` [오디오 출력을](../rest-text-to-speech.md#audio-outputs)참조하십시오.
* 끝점에 주어진 `{VOICE_NAME}` [음성 합성 태그 언어(SSML)](../speech-synthesis-markup.md) 요청을 보냅니다.