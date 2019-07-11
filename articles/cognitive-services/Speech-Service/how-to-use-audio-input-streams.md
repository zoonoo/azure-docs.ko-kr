---
title: 음성 SDK 오디오 입력 스트림 개념
titleSuffix: Azure Cognitive Services
description: 음성 SDK의 오디오 입력 스트림 API 기능에 대한 개요입니다.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 06b69da7f7435ce8a1e32150b7abe161ebdf527c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606495"
---
# <a name="about-the-speech-sdk-audio-input-stream-api"></a>음성 SDK 오디오 입력 스트림 API 정보

음성 SDK의 **오디오 입력 스트림** API는 마이크 또는 입력 파일 API를 사용하는 대신 오디오 스트림을 인식기로 스트리밍하는 방법을 제공합니다.

다음 단계는 오디오 입력 스트림을 사용할 때 필요합니다.

- 오디오 스트림의 형식을 식별합니다. 형식은 음성 SDK 및 Speech Service에서 지원되어야 합니다. 현재는 다음 구성만 지원됩니다.

  PCM 형식의 오디오 샘플, 채널 1개, 초당 샘플 16000개, 초당 32000바이트, 블록 정렬 2개(하나의 샘플에 대한 패딩을 포함하여 16비트), 샘플당 16비트.

  오디오 형식을 만들기 위한 SDK의 해당 코드는 다음과 같습니다.

  ```
  byte channels = 1;
  byte bitsPerSample = 16;
  int samplesPerSecond = 16000;
  var audioFormat = AudioStreamFormat.GetWaveFormatPCM(samplesPerSecond, bitsPerSample, channels);
  ```

- 코드가 이 사양에 따라 RAW 오디오 데이터를 제공할 수 있는지 확인합니다. 오디오 원본 데이터가 지원되는 형식과 일치하지 않는 경우, 오디오를 필요한 형식으로 트랜스코딩해야 합니다.

- `PullAudioInputStreamCallback`에서 파생된 고유한 오디오 입력 스트림 클래스를 만듭니다. `Read()` 및 `Close()` 멤버를 구현합니다. 정확한 함수 시그니처는 언어에 따라 다르지만 코드는 다음 코드 샘플과 유사합니다.

  ```
   public class ContosoAudioStream : PullAudioInputStreamCallback {
      ContosoConfig config;

      public ContosoAudioStream(const ContosoConfig& config) {
          this.config = config;
      }

      public int Read(byte[] buffer, uint size) {
          // returns audio data to the caller.
          // e.g. return read(config.YYY, buffer, size);
      }

      public void Close() {
          // close and cleanup resources.
      }
   };
  ```

- 오디오 형식 및 입력 스트림을 기반으로 오디오 구성을 만듭니다. 인식기를 만들 때 일반 음성 구성과 오디오 입력 구성을 둘 다 전달합니다. 예:

  ```
  var audioConfig = AudioConfig.FromStreamInput(new ContosoAudioStream(config), audioFormat);

  var speechConfig = SpeechConfig.FromSubscription(...);
  var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

  // run stream through recognizer
  var result = await recognizer.RecognizeOnceAsync();

  var text = result.GetText();
  ```

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
