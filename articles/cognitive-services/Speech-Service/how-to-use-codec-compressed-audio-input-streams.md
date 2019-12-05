---
title: 음성 SDK-음성 서비스를 사용 하 여 코덱 압축 오디오 스트리밍
titleSuffix: Azure Cognitive Services
description: 음성 SDK를 사용 하 여 음성 서비스에 압축 된 오디오를 스트리밍하는 방법에 대해 알아봅니다. , C#및 C++Linux 용 java, Android의 java 및 iOS의 목적-C에 사용할 수 있습니다.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: amishu
ms.openlocfilehash: 36e4506ea290d6109e1d1ae874b7e0f7c11bf50d
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805827"
---
# <a name="using-codec-compressed-audio-input-with-the-speech-sdk"></a>Speech SDK를 통해 코덱 압축 오디오 입력 사용

Speech SDK의 **압축 오디오 입력 스트림** API는 PullStream 또는 pushstream을 사용 하 여 압축 오디오를 음성 서비스로 스트리밍하는 방법을 제공 합니다.

> [!IMPORTANT]
> 스트리밍 압축 입력 오디오는 현재 Linux의 C++, C#및 Java (ubuntu 16.04, ubuntu 18.04, Debian 9)에서 지원 됩니다. [Android의 Java](how-to-use-codec-compressed-audio-input-streams-android.md) 및 [IOS 플랫폼의 목적-C](how-to-use-codec-compressed-audio-input-streams-ios.md) 에서도 지원 됩니다.
> Speech SDK 버전 1.7.0 이상이 필요 합니다.

Wav/PCM에 대해서는 메인 라인 음성 설명서를 참조하십시오.  Wav/PCM을 제외하고, 다음과 같은 압축 코덱 입력 형식이 지원됩니다.

- MP3
- OPUS/OGG
- FLAC
- Wav 컨테이너의 형식이 ALAW
- Wav 컨테이너의 MULAW

## <a name="prerequisites"></a>전제 조건

압축 된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)을 사용 하 여 구현 됩니다. 라이선스 이유로 Gstreamer 이진이 컴파일되지 않고 speech SDK로 연결 되지 않습니다. 따라서 응용 프로그램 개발자는 압축 된 입력 오디오를 사용 하도록 18.04, 16.04 및 Debian 9에 다음을 설치 해야 합니다.

```sh
sudo apt install libgstreamer1.0-0 gstreamer1.0-plugins-base gstreamer1.0-plugins-good gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly
```

## <a name="example-code-using-codec-compressed-audio-input"></a>코덱 압축 오디오 입력을 사용하는 예제 코드

압축 오디오 형식을 음성 서비스로 스트리밍하려면 `PullAudioInputStream` 또는 `PushAudioInputStream`를 만듭니다. 그런 다음, 스트림 클래스의 인스턴스에서 `AudioConfig`를 생성하여 스트림의 압축 형식을 지정합니다.

`myPushStream`이라고 하는 입력 스트림 클래스가 있고 OPUS/OGG를 사용하고 있다고 가정하겠습니다. 코드는 다음과 비슷할 수 있습니다.

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
* [Java에서 음성을 인식 하는 방법을 참조 하세요.](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java)
