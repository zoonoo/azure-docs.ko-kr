---
title: 음성 SDK 메모리 사용량을 추적하는 방법 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스 SDK는 음성 변환과 함께 음성-텍스트 변환및 텍스트 변환을 위한 다양한 프로그래밍 언어를 지원합니다. 이 문서에서는 SDK에 기본제공된 메모리 관리 도구에 대해 설명합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: da5103317a2215aca68cec14ba8a0951258c9b89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75462386"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>음성 SDK 메모리 사용량을 추적하는 방법

Speech SDK는 일련의 상호 운용성 계층을 통해 여러 프로그래밍 언어로 투영되는 기본 코드 베이스를 기반으로 합니다. 각 언어별 프로젝션에는 개체 수명 주기를 관리하기 위한 관용적으로 올바른 기능이 있습니다. 또한 Speech SDK에는 개체 로깅 및 개체 제한을 사용하여 리소스 사용량을 추적하는 메모리 관리 도구가 포함되어 있습니다. 

## <a name="how-to-read-object-logs"></a>개체 로그를 읽는 방법

[Speech SDK 로깅이 활성화된](how-to-use-logging.md)경우 추적 태그가 내보내져 기록 개체 관찰이 활성화됩니다. 이러한 태그는 다음과 같습니다. 

* `TrackHandle` 또는 `StopTracking` 
* 개체 유형
* 개체 의 형식을 추적하는 현재 개체 수와 추적중인 현재 번호입니다.

다음은 샘플 로그입니다. 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>경고 임계값 설정

경고 임계값을 만들 수 있는 옵션이 있으며 해당 임계값을 초과하면(로깅이 활성화되었다고 가정) 경고 메시지가 기록됩니다. 경고 메시지에는 해당 개수와 함께 존재하는 모든 개체의 덤프가 포함됩니다. 이 정보는 문제를 더 잘 이해하는 데 사용할 수 있습니다. 

경고 임계값을 사용하려면 `SpeechConfig` 개체에 지정해야 합니다. 이 개체는 새 인식기만 만들 때 검사됩니다. 다음 예제에서는 `SpeechConfig` 다음과 `config`같은 인스턴스를 만들었다고 가정해 보겠습니다.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 10,000입니다.

## <a name="set-an-error-threshold"></a>오류 임계값 설정 

음성 SDK를 사용하여 지정된 시간에 허용되는 최대 개체 수를 설정할 수 있습니다. 이 설정을 사용하도록 설정하면 최대 수가 적중되면 새 인식기 개체를 만들려는 시도가 실패합니다. 기존 개체는 계속 작동합니다.

다음은 샘플 오류입니다.

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

오류 임계값을 사용하려면 `SpeechConfig` 개체에 지정해야 합니다. 이 개체는 새 인식기만 만들 때 검사됩니다. 다음 예제에서는 `SpeechConfig` 다음과 `config`같은 인스턴스를 만들었다고 가정해 보겠습니다.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name(“SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> 이 속성의 기본값은 데이터 형식에 대한 `size_t` 플랫폼별 최대값입니다. 일반적인 인식은 7~10개의 내부 개체를 사용합니다.

## <a name="next-steps"></a>다음 단계

* [음성 서비스 평가판 구독 받기](get-started.md)
* [마이크를 사용하여 음성을 인식하는 방법 알아보기](quickstarts/speech-to-text-from-microphone.md)