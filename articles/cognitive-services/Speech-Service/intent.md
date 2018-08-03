---
title: 의도 인식용 샘플 | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: 다음은 의도 인식용 샘플입니다.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213205"
---
# <a name="sample-for-intent-recognition"></a>의도 인식용 샘플

먼저 구독 키를 구입하세요. Cognitive Service Speech SDK에서 지원하는 기타 서비스와 달리, 의도 인식 서비스에는 특정 구독 키가 필요합니다. [여기](https://www.luis.ai)에서 등록 키를 획득하는 방법에 대한 정보 뿐만 아니라 의도 인식 기술에 대한 추가 정보를 찾을 수 있습니다. 샘플의 해당 위치에서 의도 모델의 자체 Language Understanding 구독 키, [구독 키의 지역](regions.md) 및 AppId를 대체합니다.

## <a name="top-level-declarations"></a>최상위 선언

아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>마이크를 사용한 의도 인식

아래 코드 조각에서는 마이크 입력의 의도를 기본 언어(`en-US`)로 인식하는 방법을 보여 줍니다.

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>지정된 언어로 마이크를 사용한 의도 인식

아래 코드 조각에서는 마이크 입력의 의도를 지정된 언어(이 경우는 독일어 `de-de`)로 인식하는 방법을 보여 줍니다.

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>파일에서 의도 인식, 이벤트 사용

이 코드 조각에서는 의도를 연속 방식으로 기본 언어(`en-US`)로 인식하는 방법을 보여 줍니다. 이 코드에서는 중간 결과와 같은 추가 정보에 액세스할 수 있습니다. 입력은 오디오 파일에서 가져오고, 지원되는 형식은 샘플링 속도가 16KHz인 단일 채널(모노) WAV/PCM입니다.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>다음 단계

- [음성 인식](./speech-to-text-sample.md)

- [번역](./translation.md)
