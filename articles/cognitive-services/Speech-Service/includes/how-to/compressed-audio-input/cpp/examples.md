---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: lajanuar
ms.openlocfilehash: c2e5df326c158a8b7677acec9a44b55aba78f12b
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122261982"
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
