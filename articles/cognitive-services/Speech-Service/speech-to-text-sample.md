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
ms.openlocfilehash: b08b461ceccbdf5e79d7bb4320bdc15d09c4b859
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114245"
---
# <a name="sample-for-speech-to-text"></a>음성 텍스트 변환 샘플

[!include[Get a Subscription Key](../../../includes/cognitive-services-speech-service-get-subscription-key.md)]

## <a name="top-level-declarations"></a>최상위 선언

아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#toplevel)]

## <a name="speech-recognition-using-the-microphone"></a>마이크를 사용하여 음성 인식

아래 코드 조각에서는 마이크의 음성 입력을 기본 언어(`en-US`)로 인식하는 방법을 보여 줍니다.

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!code-java[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionWithMicrophone)]

## <a name="speech-recognition-using-a-customized-model"></a>사용자 지정된 모델을 사용하여 음성 인식

[CRIS(Custom Speech Service)](https://www.cris.ai/)를 사용하여 응용 프로그램에 맞게 Microsoft의 음성 텍스트 변환 엔진을 사용자 지정할 수 있습니다. 아래 코드 조각에서는 CRI 모델을 사용하여 마이크에서 음성을 인식하고, 실행 전에 CRI 구독 키 및 사용자 고유의 배포 ID를 입력하는 방법을 보여 줍니다.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!code-java[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionCustomized)]

## <a name="continuous-speech-recognition-from-a-file"></a>파일에서 연속 음성 인식

다음 코드 조각에서는 오디오 파일의 음성 입력을 기본 언어(`en-US`)로 연속적으로 인식합니다. 지원되는 형식은 샘플링 속도가 16KHz인 단일 채널(모노) WAV/PCM입니다.

[!include[Sample Audio](../../../includes/cognitive-services-speech-service-sample-audio.md)]

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#recognitionContinuousWithFile)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!code-java[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#recognitionContinuousWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

- [의도 인식](./intent.md)

- [번역](./translation.md)
