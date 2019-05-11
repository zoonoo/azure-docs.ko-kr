---
title: Speech SDK-음성 서비스를 사용 하 여 Stream 압축 코덱 오디오
titleSuffix: Azure Cognitive Services
description: Speech SDK를 사용 하 여 음성 서비스를 Azure에 압축 된 오디오 스트림 하는 방법에 알아봅니다. 에 사용할 수 있는 C++, C#, 및 Linux 용 Java 합니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: amishu
ms.openlocfilehash: 41a55eca321cbe1bfa23a889b8e3ce7c701ce769
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468315"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK를 사용 하 여 오디오 입력 압축 코덱을 사용 하 여

Speech SDK **압축 된 오디오 입력 Stream** API PullStream 또는 PushStream를 사용 하 여 압축 된 오디오 음성 서비스를 스트림 하는 방법을 제공 합니다.

> [!IMPORTANT]
> 압축 된 오디오 스트리밍에 대해서만 지원 됩니다 C++, C#, 및 Java linux (Ubuntu 16.04, Ubuntu 18.04 Debian 9).

Wav/PCM 메인 라인 음성 설명서를 참조 하십시오.  Wav/PCM을 외부에서 다음과 같은 압축 코덱이 입력된 형식이 지원 됩니다.

- MP3
- 저작/OGG

## <a name="prerequisites-to-using-codec-compressed-audio-input"></a>오디오 입력을 압축 코덱을 사용 하 여 필수 구성 요소

Speech SDK를 사용 하 여 압축 된 오디오 입력을 사용 하 여 Linux에 대 한 이러한 추가 종속성을 설치 합니다.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>입력 오디오를 압축 코덱을 사용 하 여 예제 코드

만들기를 음성 서비스에 압축 된 오디오 형식으로 스트리밍하려면 `PullAudioInputStream` 또는 `PushAudioInputStream`합니다. 그런 다음 만들기를 `AudioConfig` stream 클래스의 인스턴스에서 스트림의 압축 형식을 지정 하 합니다.

라고 하는 입력된 스트림 클래스 있다고 가정해 보겠습니다 `myPushStream` 저작/OGG를 사용 하 고 있습니다. 코드는이 처럼 보일 수 있습니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var speechConfig = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

// Create an audio config specifying the compressed audio format and the instance of your input stream class.
var audioFormat = AudioStreamFormat.GetCompressedFormat(AudioStreamContainerFormat.OGG_OPUS);
var audioConfig = AudioConfig.FromStreamInput(myPushStream, audioFormat);

var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

var result = await recognizer.RecognizeOnceAsync();

var text = result.GetText();
```

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
