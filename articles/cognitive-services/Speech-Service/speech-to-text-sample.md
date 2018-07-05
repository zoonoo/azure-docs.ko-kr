---
title: 음성 텍스트 변환 샘플 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 다음은 음성 텍스트 변환 샘플입니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030253"
---
# <a name="sample-for-speech-to-text"></a>음성 텍스트 변환 샘플

> [!NOTE]
> 이 샘플 및 기타 샘플을 다운로드하기 위한 지침은 [Speech SDK용 샘플](samples.md)을 참조하세요.

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> 아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>마이크를 사용하여 음성 인식

아래 코드 조각에서는 마이크의 음성 입력을 기본 언어(`en-US`)로 인식하는 방법을 보여 줍니다.

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>파일에서 음성 인식

다음 코드 조각에서는 오디오 파일의 음성 입력을 기본 언어(`en-US`)로 인식합니다. 지원되는 형식은 샘플링 속도가 16KHz인 단일 채널(모노) WAV/PCM입니다.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>사용자 지정된 모델을 사용하여 음성 인식

[CRIS(Custom Speech Service)](https://www.cris.ai/)를 사용하여 응용 프로그램에 맞게 Microsoft의 음성 텍스트 변환 엔진을 사용자 지정할 수 있습니다. 아래 코드 조각에서는 CRI 모델을 사용하여 마이크에서 음성을 인식하고, 실행 전에 CRI 구독 키 및 사용자 고유의 배포 ID를 입력하는 방법을 보여 줍니다.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>연속 음성 인식

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>샘플 소스 코드

최신 버전의 샘플 및 좀 더 수준 높은 샘플은 전용 [GitHub 리포지토리](https://github.com/Azure-Samples/cognitive-services-speech-sdk)에 제공됩니다.

## <a name="next-steps"></a>다음 단계

- [의도 인식](./intent.md)

- [번역](./translation.md)
