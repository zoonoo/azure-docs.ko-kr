---
title: 번역 샘플 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 음성 번역에 대한 샘플은 다음과 같습니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 66d26181334a71578f1a94000cb942a6a87398bc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070794"
---
# <a name="sample-for-translation"></a>번역 샘플

[!include[Get a Subscription Key](../../../includes/cognitive-services-speech-service-get-subscription-key.md)]

## <a name="top-level-declarations"></a>최상위 선언

아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#toplevel)]

## <a name="translation-using-the-microphone"></a>마이크를 사용하여 번역

아래 코드 조각은 음성 입력을 영어에서 독일어로 번역하고 번역된 텍스트의 음성 출력을 제공하는 방법을 보여 줍니다. 이 기능은 마이크를 사용합니다.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithMicrophoneAsync)]

[!code-cpp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/translation_samples.cpp#TranslationWithMicrophone)]

[!code-java [Translation Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithMicrophoneAsync)]

## <a name="translation-using-file-input"></a>파일 입력을 사용하여 번역

아래 코드 조각은 음성 입력을 영어에서 독일어 및 프랑스어로 번역하는 방법을 보여 줍니다.
파일을 입력으로 사용합니다.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/translation_samples.cs#TranslationWithFileAsync)]

[!code-java [Translation Using File Input](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/TranslationSamples.java#TranslationWithFileAsync)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

- [음성 인식](./speech-to-text-sample.md)

- [의도 인식](./intent.md)
