---
title: 음성 텍스트에 대 한 소스 언어를 지정 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용 하면 음성을 텍스트로 변환할 때 원본 언어를 지정할 수 있습니다. 이 문서에서는 FromConfig 및 SourceLanguageConfig 메서드를 사용 하 여 음성 서비스에서 소스 언어를 인식 하 고 사용자 지정 모델 대상을 제공 하는 방법을 설명 합니다.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 07ca4c519a04910e7e126d3481afb17d2b5dc350
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82977722"
---
# <a name="specify-source-language-for-speech-to-text"></a>음성 텍스트에 대 한 소스 언어 지정

이 문서에서는 음성 인식을 위해 음성 SDK에 전달 되는 오디오 입력의 원본 언어를 지정 하는 방법에 대해 알아봅니다. 또한 사용자 지정 음성 모델을 지정 하는 예제 코드를 제공 하 여 인식 기능을 향상 시킬 수 있습니다.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>C에서 원본 언어를 지정 하는 방법 #

이 예제에서 소스 언어는 생성자를 사용 하 여 `SpeechRecognizer` 매개 변수로 명시적으로 제공 됩니다.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어를 제공 합니다. 그런 다음 `sourceLanguageConfig` 가 `SpeechRecognizer` 생성할 매개 변수로 전달 됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공 합니다. 그런 다음 `sourceLanguageConfig` 가 `SpeechRecognizer` 생성할 매개 변수로 전달 됩니다.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage`및 `EndpointId` set 메서드는 c #의 `SpeechConfig` 클래스에서 더 이상 사용 되지 않습니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 `SpeechRecognizer`.을 생성할 때 사용 하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>C + +에서 소스 언어를 지정 하는 방법

이 예제에서 소스 언어는 메서드를 `FromConfig` 사용 하 여 매개 변수로 명시적으로 제공 됩니다.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어를 제공 합니다. 그런 다음를 `sourceLanguageConfig` 만들 `FromConfig` 때가 매개 변수로 전달 됩니다 `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공 합니다. 는 `sourceLanguageConfig` 를 만들 `FromConfig` 때 매개 변수로 전달 됩니다 `recognizer`.

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage`및 `SetEndpointId` 는 c + + 및 `SpeechConfig` Java의 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 `SpeechRecognizer`.을 생성할 때 사용 하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Java에서 원본 언어를 지정 하는 방법

이 예제에서 소스 언어는 새 `SpeechRecognizer`를 만들 때 명시적으로 제공 됩니다.

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어를 제공 합니다. 그런 다음 새 `sourceLanguageConfig` `SpeechRecognizer`을 만들 때가 매개 변수로 전달 됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공 합니다. 그런 다음 새 `sourceLanguageConfig` `SpeechRecognizer`을 만들 때가 매개 변수로 전달 됩니다.

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage`및 `setEndpointId` 는 c + + 및 `SpeechConfig` Java의 클래스에서 사용 되지 않는 메서드입니다. 이러한 메서드는 사용 하지 않는 것이 좋습니다 `SpeechRecognizer`.을 생성할 때 사용 하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Python에서 원본 언어를 지정 하는 방법

이 예제에서 소스 언어는 생성자를 사용 하 여 `SpeechRecognizer` 매개 변수로 명시적으로 제공 됩니다.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어를 제공 합니다. 그런 다음 `SourceLanguageConfig` 가 `SpeechRecognizer` 생성할 매개 변수로 전달 됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

이 예제에서는를 사용 하 여 `SourceLanguageConfig`소스 언어 및 사용자 지정 끝점을 제공 합니다. 그런 다음 `SourceLanguageConfig` 가 `SpeechRecognizer` 생성할 매개 변수로 전달 됩니다.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language`및 `endpoint_id` 속성은 Python의 `SpeechConfig` 클래스에서 더 이상 사용 되지 않습니다. 이러한 속성을 사용 하는 것은 권장 되지 않으며을 `SpeechRecognizer`생성할 때 사용 하지 않아야 합니다.

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="how-to-specify-source-language-in-javascript"></a>JavaScript에서 소스 언어를 지정 하는 방법

첫 번째 단계는 `SpeechConfig`다음을 만드는 것입니다.

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

다음으로 오디오의 원본 언어를 지정 합니다 `speechRecognitionLanguage`.

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 다음을 사용 `endpointId`하 여 끝점을 지정할 수 있습니다.

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```
::: zone-end

::: zone pivot="programming-language-objectivec"

## <a name="how-to-specify-source-language-in-objective-c"></a>목표에서 원본 언어를 지정 하는 방법-C

첫 번째 단계는 `speechConfig`다음을 만드는 것입니다.

```Objective-C
SPXSpeechConfiguration *speechConfig = [[SPXSpeechConfiguration alloc] initWithSubscription:@"YourSubscriptionkey" region:@"YourRegion"];
```

다음으로 오디오의 원본 언어를 지정 합니다 `speechRecognitionLanguage`.

```Objective-C
speechConfig.speechRecognitionLanguage = @"de-DE";
```

사용자 지정 모델을 인식 하기 위해 사용 하는 경우 다음을 사용 `endpointId`하 여 끝점을 지정할 수 있습니다.

```Objective-C
speechConfig.endpointId = @"The Endpoint ID for your custom model.";
```

::: zone-end

## <a name="see-also"></a>참조

* 음성 텍스트에 대해 지원 되는 언어 및 로캘 목록은 [언어 지원](language-support.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Speech SDK 참조 설명서](speech-sdk.md)
