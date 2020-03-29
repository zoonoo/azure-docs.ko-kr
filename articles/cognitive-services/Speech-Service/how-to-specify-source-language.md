---
title: 텍스트 음성에 대한 소스 언어를 지정하는 방법
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용하면 음성을 텍스트로 변환할 때 소스 언어를 지정할 수 있습니다. 이 문서에서는 FromConfig 및 SourceLanguageConfig 메서드를 사용하여 음성 서비스가 소스 언어를 알리고 사용자 지정 모델 대상을 제공하는 방법을 설명합니다.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: f0723534d9d2187593cb73f058ffea62473b80a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235953"
---
# <a name="specify-source-language-for-speech-to-text"></a>음성에서 텍스트로 의 원 언어 지정

이 문서에서는 음성 인식을 위해 음성 SDK에 전달된 오디오 입력의 소스 언어를 지정하는 방법을 배웁니다. 또한 향상된 인식을 위해 사용자 지정 음성 모델을 지정하는 예제 코드가 제공됩니다.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>C에서 소스 언어를 지정하는 방법 #

이 예제에서는 소스 언어가 구문 생성을 사용하는 `SpeechRecognizer` 매개 변수로 명시적으로 제공됩니다.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`원본 언어를 제공합니다. 그런 다음 `sourceLanguageConfig` 을 생성할 `SpeechRecognizer` 매개 변수로 전달됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공합니다. 그런 다음 `sourceLanguageConfig` 을 생성할 `SpeechRecognizer` 매개 변수로 전달됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`설정 `EndpointId` 메서드는 C#의 `SpeechConfig` 클래스에서 더 이상 사용되지 않습니다. 이러한 메서드를 사용하는 것은 권장되지 않으며 `SpeechRecognizer`을 생성할 때 사용해서는 안 됩니다.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>C++에서 원본 언어를 지정하는 방법

이 예제에서는 원본 언어가 `FromConfig` 메서드를 사용하는 매개 변수로 명시적으로 제공됩니다.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`원본 언어를 제공합니다. 그런 다음 `sourceLanguageConfig` 을 만들 `FromConfig` 때 매개 `recognizer`변수로 전달됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공합니다. 을 `sourceLanguageConfig` 만들 `FromConfig` 때 매개 변수로 `recognizer`전달됩니다.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage``SetEndpointId` C++ 및 Java의 `SpeechConfig` 클래스에서 더 이상 사용되지 않는 메서드입니다. 이러한 메서드를 사용하는 것은 권장되지 않으며 `SpeechRecognizer`을 생성할 때 사용해서는 안 됩니다.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java에서 원본 언어를 지정하는 방법

이 예제에서는 원본 언어를 새 `SpeechRecognizer`을 만들 때 명시적으로 제공 됩니다.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`원본 언어를 제공합니다. 그런 다음 `sourceLanguageConfig` 새 `SpeechRecognizer`을 만들 때 매개 변수로 전달됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공합니다. 그런 다음 `sourceLanguageConfig` 새 `SpeechRecognizer`을 만들 때 매개 변수로 전달됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage``setEndpointId` C++ 및 Java의 `SpeechConfig` 클래스에서 더 이상 사용되지 않는 메서드입니다. 이러한 메서드를 사용하는 것은 권장되지 않으며 `SpeechRecognizer`을 생성할 때 사용해서는 안 됩니다.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>파이썬에서 소스 언어를 지정하는 방법

이 예제에서는 소스 언어가 구문 생성을 사용하는 `SpeechRecognizer` 매개 변수로 명시적으로 제공됩니다.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`원본 언어를 제공합니다. 그런 다음 `SourceLanguageConfig` 을 생성할 `SpeechRecognizer` 매개 변수로 전달됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

이 예제에서는 을 사용하여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공합니다. 그런 다음 `SourceLanguageConfig` 을 생성할 `SpeechRecognizer` 매개 변수로 전달됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`속성은 `endpoint_id` 파이썬의 `SpeechConfig` 클래스에서 더 이상 사용되지 않습니다. 이러한 속성의 사용은 권장되지 않으며 `SpeechRecognizer`을 생성할 때 사용해서는 안 됩니다.

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>자바 스크립트에서 소스 언어를 지정하는 방법

첫 번째 단계는 `SpeechConfig`다음을 만드는 것입니다.

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

다음으로 다음을 통해 `speechRecognitionLanguage`오디오의 소스 언어를 지정합니다.

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

인식을 위해 사용자 지정 모델을 사용하는 경우 다음을 사용하여 `endpointId`끝점을 지정할 수 있습니다.

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Objective-C에서 소스 언어를 지정하는 방법

첫 번째 단계는 `speechConfig`다음을 만드는 것입니다.

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

다음으로 다음을 통해 `speechRecognitionLanguage`오디오의 소스 언어를 지정합니다.

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

인식을 위해 사용자 지정 모델을 사용하는 경우 다음을 사용하여 `endpointId`끝점을 지정할 수 있습니다.

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>참조

* 음성 간 텍스트에 대한 지원되는 언어 및 로캘 목록은 [언어 지원을](language-support.md)참조하십시오.

## <a name="next-steps"></a>다음 단계

* [음성 SDK 참조 문서](speech-sdk.md)
