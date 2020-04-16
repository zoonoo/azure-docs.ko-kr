---
title: 음성 에서 텍스트에 대한 자동 언어 감지를 사용하는 방법
titleSuffix: Azure Cognitive Services
description: 음성 SDK는 음성 간 텍스트에 대한 자동 언어 검색을 지원합니다. 이 기능을 사용하는 경우 제공된 오디오가 제공된 언어 목록과 비교되며 가장 가능성이 높은 일치가 결정됩니다. 그런 다음 반환된 값을 사용하여 음성 에서 텍스트에 사용되는 언어 모델을 선택할 수 있습니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: fefbe793fa4a6b90ba9bf8d468d42dcbd315759c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402199"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>음성에서 텍스트로 음성을 위한 자동 언어 감지

자동 언어 검색은 제공된 언어 목록과 비교할 때 Speech SDK에 전달된 오디오의 일치율을 결정하는 데 사용됩니다. 자동 언어 검색에 의해 반환되는 값은 음성 에서 텍스트에 대한 언어 모델을 선택하는 데 사용되어 보다 정확한 전사를 제공합니다. 사용 가능한 언어를 보려면 [언어 지원을](language-support.md)참조하십시오.

이 문서에서는 `AutoDetectSourceLanguageConfig` `SpeechRecognizer` 개체를 생성하고 검색된 언어를 검색하는 데 사용하는 방법을 배웁니다.

> [!IMPORTANT]
> 이 기능은 C#, C++, Java 및 파이썬용 음성 SDK에서만 사용할 수 있습니다.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>음성 SDK를 통한 자동 언어 감지

자동 언어 검색에는 현재 검색당 두 언어의 서비스 측 제한이 있습니다. 객체를 구성할 때 `AudoDetectSourceLanguageConfig` 이 제한을 염두에 두십시오. 아래 샘플에서 `AutoDetectSourceLanguageConfig`을 만든 다음 을 사용하여 을 구성합니다. `SpeechRecognizer`

> [!TIP]
> 음성 간 텍스트 수행 시 사용할 사용자 지정 모델을 지정할 수도 있습니다. 자세한 내용은 [자동 언어 검색을 위한 사용자 지정 모델 사용을](#use-a-custom-model-for-automatic-language-detection)참조하십시오.

다음 코드 조각은 앱에서 자동 언어 검색을 사용하는 방법을 보여 줍니다.

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>자동 언어 검색을 위한 사용자 지정 모델 사용

음성 서비스 모델을 사용하는 언어 검색 외에도 향상된 인식을 위한 사용자 지정 모델을 지정할 수 있습니다. 사용자 지정 모델이 제공되지 않으면 서비스는 기본 언어 모델을 사용합니다.

아래 코드 조각은 음성 서비스에 대한 호출에서 사용자 지정 모델을 지정하는 방법을 보여 줍니다. 검색된 언어가 `en-US`은으로 지정되면 기본 모델이 사용됩니다. 검색된 언어가 `fr-FR`다음과 같은 경우 사용자 지정 모델의 끝점이 사용됩니다.

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

## <a name="next-steps"></a>다음 단계

- [음성 SDK 참조 문서](speech-sdk.md)
