---
title: 음성 텍스트에 자동 언어 검색을 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 음성 텍스트에 대 한 자동 언어 검색을 지원 합니다. 이 기능을 사용 하는 경우 제공 된 오디오가 제공 된 언어 목록과 비교 되며 가장 가능성이 높은 항목이 결정 됩니다. 그러면 반환 된 값을 사용 하 여 음성 텍스트에 사용 되는 언어 모델을 선택할 수 있습니다.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402199"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>음성 텍스트에 대 한 자동 언어 검색

자동 언어 검색은 제공 된 언어 목록과 비교할 때 음성 SDK에 전달 되는 오디오에 대해 가장 가능성이 높은 일치 항목을 확인 하는 데 사용 됩니다. 자동 언어 검색에서 반환 되는 값은 음성 텍스트에 대 한 언어 모델을 선택 하 여 더 정확한 기록을 제공 하는 데 사용 됩니다. 사용할 수 있는 언어를 확인 하려면 [언어 지원](language-support.md)을 참조 하세요.

이 문서에서는를 사용 `AutoDetectSourceLanguageConfig` 하 여 `SpeechRecognizer` 개체를 생성 하 고 검색 된 언어를 검색 하는 방법을 배웁니다.

> [!IMPORTANT]
> 이 기능은 c #, c + +, Java 및 Python 용 Speech SDK 에서만 사용할 수 있습니다.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>음성 SDK를 사용 하 여 자동 언어 검색

자동 언어 검색에는 현재 검색 당 두 가지 언어의 서비스 쪽 제한이 있습니다. 개체를 `AudoDetectSourceLanguageConfig` 생성할 때 이러한 제한을 염두에 두십시오. 아래 샘플에서는를 만든 `AutoDetectSourceLanguageConfig`다음이를 사용 하 여를 `SpeechRecognizer`생성 합니다.

> [!TIP]
> 음성 텍스트를 수행할 때 사용할 사용자 지정 모델을 지정할 수도 있습니다. 자세한 내용은 [자동 언어 검색에 사용자 지정 모델 사용](#use-a-custom-model-for-automatic-language-detection)을 참조 하세요.

다음 코드 조각은 앱에서 자동 언어 검색을 사용 하는 방법을 보여 줍니다.

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

## <a name="use-a-custom-model-for-automatic-language-detection"></a>자동 언어 검색에 사용자 지정 모델 사용

음성 서비스 모델을 사용 하는 언어 검색 외에도, 향상 된 인식을 위한 사용자 지정 모델을 지정할 수 있습니다. 사용자 지정 모델을 제공 하지 않으면 서비스에서 기본 언어 모델을 사용 합니다.

아래 코드 조각은 음성 서비스 호출에서 사용자 지정 모델을 지정 하는 방법을 보여 줍니다. 검색 된 언어가 `en-US`이면 기본 모델이 사용 됩니다. 검색 된 언어가 `fr-FR`이면 사용자 지정 모델의 끝점이 사용 됩니다.

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

- [Speech SDK 참조 설명서](speech-sdk.md)
