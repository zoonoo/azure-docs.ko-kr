---
title: 음성 SDK를 사용하면 음성 인식 모드 선택
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용할 때 최상의 인식 모드를 선택하는 방법을 알아봅니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: d997cb592d9d648998f2b44d9f61f465f05faeb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79079815"
---
# <a name="choose-a-speech-recognition-mode"></a>음성 인식 모드 선택

음성 간 텍스트 인식 작업을 고려할 때 [음성 SDK는](speech-sdk.md) 음성 처리를 위한 여러 모드를 제공합니다. 개념적으로 인식 *모드라고도*합니다. 이 문서에서는 다양한 인식 모드를 비교합니다.

## <a name="recognize-once"></a>한 번 인식

각 발언을 한 번에 하나씩 "문장"으로 처리하려면 "한 번 인식" 함수를 사용합니다. 이 메서드는 다음 일시 중지까지 감지된 음성의 시작 부분에서 시작 하는 입력에서 인식 된 발언을 감지 합니다. 일반적으로 일시 중지는 문장의 끝또는 생각의 줄을 표시합니다.

인식된 발언이 끝나면 서비스는 해당 요청에서 오디오 처리를 중지합니다. 최대 인식 한도는 20초의 문장 지속 시간입니다.

::: zone pivot="programming-language-csharp"

`RecognizeOnceAsync` 함수 사용에 대한 자세한 내용은 [.NET 음성 SDK 문서를](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechRecognizer_RecognizeOnceAsync)참조하십시오.

```csharp
var result = await recognizer.RecognizeOnceAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`RecognizeOnceAsync` 함수 사용에 대한 자세한 내용은 [C++ 음성 SDK 문서를](https://docs.microsoft.com/cpp/cognitive-services/speech/asyncrecognizer#recognizeonceasync)참조하십시오.

```cpp
auto result = recognize->RecognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

`recognizeOnceAsync` 함수 사용에 대한 자세한 내용은 [Java 음성 SDK 문서를](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechRecognizer.recognizeOnceAsync?view=azure-java-stable)참조하십시오.

```java
SpeechRecognitionResult result = recognizer.recognizeOnceAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

`recognize_once` 함수 사용에 대한 자세한 내용은 [파이썬 음성 SDK 문서를](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechrecognizer?view=azure-python#recognize-once------azure-cognitiveservices-speech-speechrecognitionresult)참조하십시오.

```python
result = speech_recognizer.recognize_once()
```

::: zone-end
::: zone pivot="programming-language-more"

추가 언어는 음성 [SDK 참조 문서를](speech-to-text.md#speech-sdk-reference-docs)참조하십시오.

::: zone-end

## <a name="continuous"></a>연속

장기 실행 인식이 필요한 경우 연속 인식을 위해 시작 및 해당 정지 기능을 사용합니다. 시작 함수는 중지 함수를 호출하거나 침묵의 시간이 너무 오래 경과할 때까지 모든 발언을 시작하고 계속 처리합니다. 연속 모드를 사용하는 경우 발생 시 발생하는 다양한 이벤트에 등록해야 합니다. 예를 들어 음성 인식이 발생하면 "인식된" 이벤트가 발생합니다. 인식을 처리하려면 이벤트 처리기가 있어야 합니다.

::: zone pivot="programming-language-csharp"

```csharp
// Subscribe to event
recognizer.Recognized += (s, e) => 
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result.Text
    }
};

// Start continuous speech recognition
await recognizer.StartContinuousRecognitionAsync();

// Stop continuous speech recognition
await recognizer.StopContinuousRecognitionAsync();
```

::: zone-end
::: zone pivot="programming-language-cpp"

```cpp
// Connect to event
recognizer->Recognized.Connect([] (const SpeechRecognitionEventArgs& e)
{
    if (e.Result->Reason == ResultReason::RecognizedSpeech)
    {
        // Do something with the recognized text
        // e.Result->Text
    }
});

// Start continuous speech recognition
recognizer->StartContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer->StopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-java"

```java
recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        // Do something with the recognized text
        // e.getResult().getText()
    }
});

// Start continuous speech recognition
recognizer.startContinuousRecognitionAsync().get();

// Stop continuous speech recognition
recognizer.stopContinuousRecognitionAsync().get();
```

::: zone-end
::: zone pivot="programming-language-python"

```python
def recognized_cb(evt):
    if evt.result.reason == speechsdk.ResultReason.RecognizedSpeech:
        # Do something with the recognized text
        # evt.result.text

speech_recognizer.recognized.connect(recognized_cb)

# Start continuous speech recognition
speech_recognizer.start_continuous_recognition()

# Stop continuous speech recognition
speech_recognizer.stop_continuous_recognition()
```

::: zone-end
::: zone pivot="programming-language-more"

추가 언어는 음성 [SDK 참조 문서를](speech-to-text.md#speech-sdk-reference-docs)참조하십시오.

::: zone-end

## <a name="dictation"></a>받아쓰기

연속 인식을 사용하는 경우 해당 "받아쓰기 사용" 기능을 사용하여 받아쓰기 처리를 활성화할 수 있습니다. 이 모드는 구두점과 같은 문장 구조의 단어 설명을 해석하는 음성 구성 인스턴스를 발생시게 됩니다. 예를 들어 ,"당신은 마을 물음표에 살고 있습니까"라는 발언은 "마을에 살고 있습니까?"라는 텍스트로 해석됩니다.

::: zone pivot="programming-language-csharp"

`EnableDictation` 함수 사용에 대한 자세한 내용은 [.NET 음성 SDK 문서를](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-dotnet#Microsoft_CognitiveServices_Speech_SpeechConfig_EnableDictation)참조하십시오.

```csharp
// Enable diction
SpeechConfig.EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-cpp"

`EnableDictation` 함수 사용에 대한 자세한 내용은 [C++ 음성 SDK 문서를](https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#enabledictation)참조하십시오.

```cpp
// Enable diction
SpeechConfig->EnableDictation();
```

::: zone-end
::: zone pivot="programming-language-java"

`enableDictation` 함수 사용에 대한 자세한 내용은 [Java 음성 SDK 문서를](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.SpeechConfig.enableDictation?view=azure-java-stable)참조하십시오.

```java
// Enable diction
SpeechConfig.enableDictation();
```

::: zone-end
::: zone pivot="programming-language-python"

`enable_dictation` 함수 사용에 대한 자세한 내용은 [파이썬 음성 SDK 문서를](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#enable-dictation--)참조하십시오.

```python
# Enable diction
SpeechConfig.enable_dictation()
```

::: zone-end
::: zone pivot="programming-language-more"

추가 언어는 음성 [SDK 참조 문서를](speech-to-text.md#speech-sdk-reference-docs)참조하십시오.

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 추가 음성 SDK 샘플 살펴보기](https://aka.ms/csspeech/samples)
