---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 0e8a6a0ffa451337b9e7ea7590cfe614fc1129a1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409696"
---
압축된 오디오 형식으로 음성 서비스로 스트리밍하려면 `PullAudioInputStream` `PushAudioInputStream`를 만들거나 를 만듭니다. 그런 다음 `AudioConfig` 스트림 클래스의 인스턴스에서 스트림의 압축 형식을 지정하여 만듭니다.

입력 스트림 클래스가 라는 `pushStream` 가정하 고 OPUS/OGG를 사용 하는 가정 해 봅시다. 코드는 다음과 같을 수 있습니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

// ... omitted for brevity

var speechConfig =
    SpeechConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");

// Create an audio config specifying the compressed
// audio format and the instance of your input stream class.
var audioFormat =
    AudioStreamFormat.GetCompressedFormat(
        AudioStreamContainerFormat.OGG_OPUS);
var audioConfig =
    AudioConfig.FromStreamInput(
        pushStream,
        audioFormat);

using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
var result = await recognizer.RecognizeOnceAsync();

var text = result.Text;
```