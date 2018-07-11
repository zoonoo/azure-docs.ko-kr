---
title: AudioInputStream 개념 | Microsoft Docs
description: AudioInputStream API 기능에 대한 개요입니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 528356473c4221a815fa68cbec3426866c4cbd23
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2018
ms.locfileid: "35383424"
---
# <a name="about-the-audio-input-stream-api"></a>Audio Input Stream API 정보

**Audio Input Stream** API는 마이크 또는 입력 파일 API를 사용하는 대신 오디오 스트림을 인식기로 스트리밍하는 방법을 제공합니다.

## <a name="api-overview"></a>API 개요

API는 두 개의 구성 요소인 `AudioInputStream`(원시 오디오 데이터) 및 `AudioInputStreamFormat`을 사용합니다.

`AudioInputStreamFormat`은 오디오 데이터의 형식을 정의합니다. Windows의 웨이브 파일에 대한 표준 `WAVEFORMAT` 구조와 비교할 수 있습니다.

  - `FormatTag`

    오디오 형식입니다. 현재 Speech SDK는 `format 1`(PCM - little-endian)만 지원합니다.

  - `Channels`

    채널 수입니다. 현재 Speech Service는 하나의 채널(모노) 오디오 자료만 지원합니다.

  - `SamplesPerSec`

    샘플 속도입니다. 일반적인 마이크 녹음에는 초당 16000개의 샘플이 포함됩니다.

  - `AvgBytesPerSec`

    `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`로 계산된 초당 평균 바이트 수입니다. 변동 비트 전송률을 사용하는 오디오 스트림의 경우 초당 평균 바이트 수가 다를 수 있습니다.

  - `BlockAlign`

    `Channels * ceil(wBitsPerSample, 8)`로 계산된 단일 프레임의 크기입니다. 패딩으로 인해 실제 값이 이 값보다 높을 수 있습니다.

  - `BitsPerSample`

    샘플당 비트 수입니다. 일반 오디오 스트림은 샘플당 16비트(CD 품질)를 사용합니다.

`AudioInputStream` 기본 클래스는 사용자 지정 스트림 어댑터를 통해 재정의됩니다. 이 어댑터는 다음 기능을 구현해야 합니다.

   - `GetFormat()`

     이 함수를 호출하여 오디오 스트림의 형식을 가져옵니다. 이 함수는 AudioInputStreamFormat 버퍼에 대한 포인터를 가져옵니다.

   - `Read()`

     이 함수를 호출하여 오디오 스트림에서 데이터를 가져옵니다. 한 매개 변수는 오디오 데이터를 복사할 버퍼에 대한 포인터입니다. 두 번째 매개 변수는 버퍼의 크기입니다. 이 함수는 버퍼에 복사된 바이트 수를 반환합니다. 반환 값 `0`은 스트림의 끝을 나타냅니다.

   - `Close()`

     이 함수를 호출하여 오디오 스트림을 닫습니다.

## <a name="usage-examples"></a>사용 예제

일반적으로 다음 단계는 오디오 입력 스트림을 사용할 때 수행합니다.

  - 오디오 스트림의 형식을 식별합니다. 형식은 SDK 및 Speech Service에서 지원되어야 합니다. 현재 다음 구성이 지원됩니다.

    오디오 형식 태그(PCM) 1개, 채널 1개, 초당 샘플 16000개, 초당 32000바이트, 블록 정렬 2개(하나의 샘플에 대한 패딩을 포함하여 16비트), 샘플당 16비트

  - 위에서 식별된 사양에 대해 코드가 RAW 오디오 데이터를 제공할 수 있는지 확인합니다. 오디오 원본 데이터가 지원되는 형식과 일치하지 않는 경우, 오디오를 필요한 형식으로 트랜스코딩해야 합니다.

  - `AudioInputStream`에서 사용자 지정 오디오 입력 스트림 클래스를 파생시킵니다. `GetFormat()`, `Read()` 및 `Close()` 작업을 구현합니다. 정확한 함수 시그니처는 언어에 따라 다르지만 코드는 다음 코드 샘플과 유사합니다.

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - 오디오 입력 스트림을 사용합니다.

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - 일부 언어에서는 인식이 완료된 후 `contosoStream`을 명시적으로 삭제해야 합니다. 전체 입력을 읽기 전에 AudioStream을 릴리스할 수 없습니다. `StopContinuousRecognitionAsync` 및 `StopContinuousRecognitionAsync`를 사용하는 시나리오에서는 이 샘플에 설명된 개념이 필요합니다.

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>다음 단계

* [Speech 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-windows.md)
