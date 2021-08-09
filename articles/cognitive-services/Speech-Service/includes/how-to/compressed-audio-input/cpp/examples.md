---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 2fbdd0f1d6657bee75584eec0e0629001cedb262
ms.sourcegitcommit: 1b19b8d303b3abe4d4d08bfde0fee441159771e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/11/2021
ms.locfileid: "109805135"
---
압축된 오디오 입력을 허용하도록 Speech SDK를 구성하려면 `PullAudioInputStream` 또는 `PushAudioInputStream`을 만듭니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 만들어 스트림의 압축 형식을 지정합니다.  [Speech SDK 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)에서 관련 샘플 코드를 찾습니다.

`pushStream`이라는 입력 스트림 클래스가 있고 OPUS/OGG를 사용하고 있다고 가정해 보겠습니다. 코드는 다음과 같이 표시될 수 있습니다.

```cpp
using namespace Microsoft::CognitiveServices::Speech;
using namespace Microsoft::CognitiveServices::Speech::Audio;

// ... omitted for brevity

 auto config =
    SpeechConfig::FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion"
    );

auto audioFormat =
    AudioStreamFormat::GetCompressedFormat(
        AudioStreamContainerFormat::OGG_OPUS
    );
auto audioConfig =
    AudioConfig::FromStreamInput(
        pushStream,
        audioFormat
    );

auto recognizer = SpeechRecognizer::FromConfig(config, audioConfig);
auto result = recognizer->RecognizeOnceAsync().get();

auto text = result->Text;
```
