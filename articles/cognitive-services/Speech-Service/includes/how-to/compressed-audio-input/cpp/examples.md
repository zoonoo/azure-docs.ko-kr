---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: b565bf3bae61cf92133b957bb75fdae9545c030e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "81421704"
---
압축 오디오 형식을 음성 서비스로 스트리밍하려면 또는를 만듭니다 `PullAudioInputStream` `PushAudioInputStream` . 그런 다음 스트림 `AudioConfig` 클래스의 인스턴스에서를 만들어 스트림의 압축 형식을 지정 합니다.

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