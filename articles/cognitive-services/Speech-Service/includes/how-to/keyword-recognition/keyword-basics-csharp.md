---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509516"
---
먼저을 반환 하는 정적 함수를 사용 하 여 키워드 모델 파일을 로드 `FromFile()` `KeywordRecognitionModel` 합니다. `.table`Speech Studio에서 다운로드 한 파일의 경로를 사용 합니다. 또한 `AudioConfig` 기본 마이크를 사용 하 여를 만든 다음, `KeywordRecognizer` 오디오 구성을 사용 하 여 새를 인스턴스화합니다.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

다음으로, 모델 개체를 전달 하 여에 대 한 호출로 키워드 인식을 실행 `RecognizeOnceAsync()` 합니다. 그러면 키워드가 인식 될 때까지 지속 되는 키워드 인식 세션이 시작 됩니다. 따라서 일반적으로 다중 스레드 응용 프로그램에서이 디자인 패턴을 사용 하거나, 절전 모드 해제 단어를 무기한 대기할 수 있는 사용 사례에서이 디자인 패턴을 사용 합니다.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> 여기에 표시 된 예제에서는 `SpeechConfig` 인증 컨텍스트에 개체가 필요 하지 않으며 백 엔드에 연결 하지 않기 때문에 로컬 키워드 인식을 사용 합니다. 그러나 [연속 백 엔드 연결을 활용](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)하 여 키워드 인식 및 확인을 모두 실행할 수 있습니다.