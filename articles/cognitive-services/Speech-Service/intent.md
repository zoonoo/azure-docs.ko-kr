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
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045013"
---
# <a name="sample-for-intent-recognition"></a>의도 인식용 샘플

> [!NOTE]
> 이 샘플 및 기타 샘플을 다운로드하기 위한 지침은 [Speech SDK용 샘플](samples.md)을 참조하세요.

> [!NOTE]
> 먼저 구독 키를 구입하세요. Cognitive Service Speech SDK에서 지원하는 기타 서비스와 달리, 의도 인식 서비스에는 특정 구독 키가 필요합니다. [여기](https://www.luis.ai)에서 등록 키를 획득하는 방법에 대한 정보 뿐만 아니라 의도 인식 기술에 대한 추가 정보를 찾을 수 있습니다. 샘플의 해당 위치에서 의도 모델의 사용자 고유 구독 키, 서비스 지역 및 AppId를 대체합니다.

> [!NOTE]
> 아래의 모든 샘플에서 다음 최상위 선언이 적용되어야 합니다.
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>마이크를 사용한 의도 인식

아래 코드 조각에서는 마이크 입력의 의도를 기본 언어(`en-US`)로 인식하는 방법을 보여 줍니다.

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>지정된 언어로 마이크를 사용한 의도 인식

아래 코드 조각에서는 마이크 입력의 의도를 지정된 언어(이 경우는 독일어 `de-de`)로 인식하는 방법을 보여 줍니다.

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>파일에서 의도 인식

다음 코드 조각에서는 오디오 파일의 의도를 기본 언어(`en-US`)로 인식합니다. 지원되는 형식은 샘플링 속도가 16KHz인 단일 채널(모노) WAV/PCM입니다.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>이벤트를 사용한 의도 인식

이 코드 조각에서는 의도를 연속 방식으로 인식하는 방법을 보여 줍니다. 이 코드에서는 중간 결과와 같은 추가 정보에 액세스할 수 있습니다. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>샘플 소스 코드

샘플의 최신 집합은 [Cognitive Services Speech SDK 샘플 GitHub 리포지토리](https://aka.ms/csspeech/samples)를 참조합니다.

## <a name="next-steps"></a>다음 단계

- [음성 인식](./speech-to-text-sample.md)

- [번역](./translation.md)
