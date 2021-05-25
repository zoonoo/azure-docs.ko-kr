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
ms.openlocfilehash: 4571acfc46d9b336be23eaa3287dce5696c707b2
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2021
ms.locfileid: "107930232"
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
