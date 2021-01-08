---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: ad32204739d728006362ef55657a2f433be7aefc
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97978878"
---
압축 된 오디오 입력을 허용 하도록 Speech SDK를 구성 하려면 또는을 만듭니다 `PullAudioInputStream` `PushAudioInputStream` . 그런 다음 스트림 `AudioConfig` 클래스의 인스턴스에서를 만들어 스트림의 압축 형식을 지정 합니다.

이라는 입력 스트림 클래스가 있고 `pushStream` OPUS/OGG를 사용 하 고 있다고 가정해 보겠습니다. 코드는 다음과 같습니다.

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
