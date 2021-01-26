---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: c770ca3d619ea443fd5a320f91f3bfae56732bac
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792390"
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
> 여기에 표시 된 예제에서는 `SpeechConfig` 인증 컨텍스트에 개체가 필요 하지 않으며 백 엔드에 연결 하지 않기 때문에 로컬 키워드 인식을 사용 합니다. 그러나 [직접 백 엔드 연결을 활용](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword)하 여 키워드 인식 및 확인을 모두 실행할 수 있습니다.

### <a name="continuous-recognition"></a>연속 인식

음성 SDK의 다른 클래스는 키워드 인식을 통해 음성 및 의도 인식 모두에 대해 연속 인식을 지원 합니다. 이렇게 하면 `.table` 키워드 모델에 대 한 파일을 참조 하는 기능을 사용 하 여 일반적으로 연속 인식에 사용 하는 것과 동일한 코드를 사용할 수 있습니다.

음성 텍스트의 경우 [빠른](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) 시작에 표시 된 것과 동일한 디자인 패턴을 따라 연속 인식을 설정 합니다. 그런 다음에 대 한 호출을로 바꾸고 `recognizer.StartContinuousRecognitionAsync()` `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` 개체를 전달 `KeywordRecognitionModel` 합니다. 발견 키워드를 사용 하 여 연속 인식을 중지 하려면 대신을 사용 `recognizer.StopKeywordRecognitionAsync()` `recognizer.StopContinuousRecognitionAsync()` 합니다.

의도 인식에서는 및 함수와 동일한 패턴을 [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) 사용 [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) 합니다.