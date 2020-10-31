---
title: 음성 SDK를 발음 평가에 사용 하는 방법
titleSuffix: Azure Cognitive Services
description: Speech SDK는 정확도, 능숙, 완전성 등의 표시기를 사용 하 여 음성 입력의 발음 품질을 평가 하는 발음 평가를 지원 합니다.
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-set-nineteen
ms.openlocfilehash: 245a00acb07d1c0e769a243413fccdf64d544f5a
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133686"
---
# <a name="pronunciation-assessment"></a>발음 평가

발음 평가는 음성 발음을 평가 하 고 음성 오디오의 정확성과 능숙에 대 한 발표자 피드백을 제공 합니다.
발음 평가를 통해 언어 학습자는 사용자의 의견을 얻고 자신 있게 의견을 주고 받을 수 있도록 음성을 향상할 수 있습니다.
교육자는이 기능을 사용 하 여 여러 스피커의 발음을 실시간으로 평가할 수 있습니다.

이 문서에서는 `PronunciationAssessmentConfig` SPEECH SDK를 사용 하 여를 설정 하 고 검색 하는 방법을 알아봅니다 `PronunciationAssessmentResult` .

> [!NOTE]
> 발음 평가 기능은 현재 지역 및 에서만 사용할 수 `westus` `eastasia` `centralindia` 있으며 언어만 지원 합니다 `en-US` .

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>음성 SDK를 사용한 발음 평가

아래 샘플에서는를 만든 `PronunciationAssessmentConfig` 다음에 적용 `SpeechRecognizer` 합니다.

다음 코드 조각은 앱에서 자동 언어 검색을 사용 하는 방법을 보여 줍니다.

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>발음 평가 구성 매개 변수

다음 표에서는 발음 평가에 대 한 구성 매개 변수를 나열 합니다.

| 매개 변수 | 설명 | 필수/선택 |
|-----------|-------------|---------------------|
| ReferenceText | 발음이 계산 될 텍스트입니다. | 필수 |
| GradingSystem | 점수 보정의 시점 시스템입니다. 허용되는 값은 `FivePoint` 및 `HundredMark`입니다. 기본 설정은 `FivePoint`입니다. | 선택 사항 |
| 세분성 | 평가 세분성입니다. 허용 되는 값은 전체 텍스트의 점수를 표시 하는 전체 텍스트, word 및 음소 수준에 대 한 점수를 표시 하는입니다 .이 값은 전체 텍스트 `Phoneme` `Word` `FullText` 수준 에서만 점수를 표시 합니다. 기본 설정은 `Phoneme`입니다. | 선택 사항 |
| EnableMiscue | Miscue 계산을 사용 합니다. 이 기능을 사용 하도록 설정 하면 단어를 참조 텍스트와 비교 하 여 비교에 따라 생략/삽입으로 표시 됩니다. 허용되는 값은 `False` 및 `True`입니다. 기본 설정은 `False`입니다. | 선택 사항 |
| ScenarioId | 사용자 지정 된 지점 시스템을 나타내는 GUID입니다. | 선택 사항 |

### <a name="pronunciation-assessment-result-parameters"></a>발음 평가 결과 매개 변수

다음 표에서는 발음 평가의 결과 매개 변수를 보여 줍니다.

| 매개 변수 | 설명 |
|-----------|-------------|
| `AccuracyScore` | 음성의 발음 정확도입니다. 정확도는 음소가 네이티브 스피커의 발음에 얼마나 근접 하 게 일치 하는지를 나타냅니다. 단어 및 전체 텍스트 수준 정확도 점수는 음소 수준 정확도 점수에서 집계 됩니다. |
| `FluencyScore` | 지정 된 음성의 능숙입니다. 능숙는 음성이 단어 사이에서 기본 스피커의 자동 나누기 사용과 일치 하는지 여부를 나타냅니다. |
| `CompletenessScore` | 음성의 완전성으로, 텍스트 입력을 참조 하는 단어를 발음 하는 비율을 계산 하 여 결정 됩니다. |
| `PronunciationScore` | 지정 된 음성의 발음 품질을 나타내는 전체 점수입니다. 이는에서 `AccuracyScore` , 가중치를 사용 하 여 집계 됩니다 `FluencyScore` `CompletenessScore` . |
| `ErrorType` | 이 값은와 비교 하 여 단어를 생략 하거나, 삽입 하거나, 잘못 표시 하는지 여부를 나타냅니다 `ReferenceText` . 가능한 값은 `None` (이 단어에 오류가 없음을 의미 함) `Omission` , `Insertion` 및 `Mispronunciation` 입니다. |

## <a name="next-steps"></a>다음 단계

<!-- TODO: update the sample links after release -->

<!-- ::: zone pivot="programming-language-csharp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-cpp"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-java"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-python"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_synthesis_sample.py#L434) on GitHub for automatic language detection
::: zone-end

::: zone pivot="programming-language-objectivec"
* See the [sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L494) on GitHub for automatic language detection
::: zone-end -->

* [Speech SDK 참조 설명서](speech-sdk.md)
