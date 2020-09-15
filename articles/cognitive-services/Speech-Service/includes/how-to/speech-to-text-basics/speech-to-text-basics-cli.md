---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570582"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>마이크에서 음성을 텍스트로 변환

PC 마이크를 연결하여 켜고 마이크를 사용할 수도 있는 앱을 해제합니다. 일부 컴퓨터에는 기본 제공 마이크가 있지만 다른 컴퓨터에는 Bluetooth 디바이스를 구성해야 합니다.

이제 음성 CLI를 실행하여 마이크의 음성을 인식할 준비가 되었습니다. 명령줄에서 Speech CLI 이진 파일이 포함된 디렉터리로 변경하고 다음 명령을 실행합니다.

```bash
spx recognize --microphone
```

> [!NOTE]
> Speech CLI의 기본값은 영어입니다. [음성 텍스트 변환 테이블](../../../../language-support.md)에서 다른 언어를 선택할 수 있습니다.
> 예를 들어 독일어 음성을 인식하도록 `--source de-DE`를 추가합니다.

마이크에 대고 말하면 실시간으로 단어를 텍스트로 전사하는 것을 볼 수 있습니다. 음성 CLI는 일정 시간 음소거 후 또는 ctrl-C를 누를 때 중지됩니다.

## <a name="speech-to-text-from-audio-file"></a>오디오 파일에서 음성을 텍스트로 변환

Speech CLI는 다양한 파일 형식과 자연어로 음성을 인식할 수 있습니다. 이 예제에서는 영어 음성이 포함된 WAV 파일(16kHz 또는 8kHz, 16비트 및 모노 PCM)을 사용할 수 있습니다. 또는 빠른 샘플을 원하는 경우 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> 파일을 다운로드하여 Speech CLI 이진 파일과 동일한 디렉터리에 복사합니다.

이제 다음 명령을 실행하여 오디오 파일에서 찾은 음성을 인식하도록 Speech CLI를 실행할 준비가 되었습니다.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Speech CLI의 기본값은 영어입니다. [음성 텍스트 변환 테이블](../../../../language-support.md)에서 다른 언어를 선택할 수 있습니다.
> 예를 들어 독일어 음성을 인식하도록 `--source de-DE`를 추가합니다.

Speech CLI는 화면에 음성의 텍스트 전사를 표시합니다.