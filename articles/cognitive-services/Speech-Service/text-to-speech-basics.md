---
title: 음성 합성 기본 사항 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용하여 텍스트를 음성으로 변환하는 방법을 알아봅니다. 이 문서에서는 개체 구성, 지원되는 오디오 출력 형식 및 음성 합성을 위한 사용자 지정 구성 옵션에 대해 알아봅니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: c5ceb76b8dcdad5d487e196cf1780703b7e34f17
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874523"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>음성 합성의 기본 사항 알아보기

이 문서에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다. 먼저 기본 구성 및 합성을 수행하고 다음과 같은 사용자 지정 애플리케이션 개발을 위한 고급 예제로 이동합니다.

* 메모리 내 스트림으로 응답 가져오기
* 출력 샘플 속도 및 비트 전송률 사용자 지정
* SSML(음성 합성 마크업 언어)을 사용하여 합성 요청 제출
* 인공신경망 음성 사용

> [!TIP]
> 아직 빠른 시작 중 하나를 완료하지 않은 분들은 텍스트 음성 변환을 살펴보고 직접 경험해 보실 것을 권장합니다.
> * [스피커로 음성 합성](quickstarts/text-to-speech.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>다음 단계

* [Custom Voice 시작](how-to-custom-voice.md)
* [SSML을 사용하여 합성 향상](speech-synthesis-markup.md)
