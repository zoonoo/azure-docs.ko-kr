---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: badc3cd372ca992a83678ef8d32ac76966ba9b94
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92499161"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [Java 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 음성 SDK를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java 런타임 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다. 키와 지역을 사용하여 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 만듭니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요.

```java
import java.util.concurrent.Future;
import com.microsoft.cognitiveservices.speech.*;

SpeechConfig config = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

[`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone"></a>마이크에서 인식

디바이스 마이크를 사용하여 음성을 인식하려면 `fromDefaultMicrophoneInput()`을 사용하여 `AudioConfig`를 만듭니다. 그런 다음, [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable)을 초기화하고 `audioConfig` 및 `config`를 전달합니다.

```java
AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);

System.out.println("Speak into your microphone.");
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
System.out.println("RECOGNIZED: Text=" + result.getText());
```

*특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식

마이크를 사용하는 대신 오디오 파일에서 음성을 인식하려는 경우에도 `AudioConfig`를 만들어야 합니다. 그러나 [`AudioConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-java-stable)을 만들 때 `fromDefaultMicrophoneInput()`을 호출하는 대신 `fromWavFileInput()`을 호출하고 파일 경로를 전달합니다.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);

Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
System.out.println("RECOGNIZED: Text=" + result.getText());
```

## <a name="recognize-speech"></a>음성 인식

Java용 음성 SDK의 [인식기 클래스](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true)는 음성 인식에 사용할 수 있는 몇 가지 메서드를 공개합니다.

### <a name="single-shot-recognition"></a>단일 샷 인식

단일 샷 인식은 단일 발화를 비동기적으로 인식합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다. 다음은 [`recognizeOnceAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizeonceasync?view=azure-java-stable)를 사용하는 비동기 단일 샷 인식의 예입니다.

```java
Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = task.get();
```

결과를 처리하는 코드를 작성해야 합니다. 이 샘플은 [`result.getReason()`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.resultreason?view=azure-java-stable&preserve-view=true)을 평가합니다.

* 인식 결과 `ResultReason.RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `ResultReason.NoMatch`를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `ResultReason.Canceled`를 출력합니다.

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

### <a name="continuous-recognition"></a>연속 인식

연속 인식은 단일 샷 인식보다 약간 더 복잡합니다. `recognizing`, `recognized` 및 `canceled` 이벤트를 구독하여 인식 결과를 얻어야 합니다. 인식을 중지하려면 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync)를 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

먼저 입력을 정의하고 [`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true)를 초기화하겠습니다.

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

다음으로, 음성 인식 상태를 관리하는 변수를 만들어 보겠습니다. 시작하기 위해, 클래스 범위에서 `Semaphore`를 선언합니다.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

[`SpeechRecognizer`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer?view=azure-java-stable&preserve-view=true)에서 전송된 이벤트를 구독합니다.

* [`recognizing`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing?view=azure-java-stable&preserve-view=true): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized?view=azure-java-stable&preserve-view=true): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`sessionStopped`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped?view=azure-java-stable&preserve-view=true): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled?view=azure-java-stable&preserve-view=true): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

모든 설정이 끝났으면 [`startContinuousRecognitionAsync`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync)를 호출할 수 있습니다.

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable&preserve-view=true)에서 [`enableDictation`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation?view=azure-java-stable&preserve-view=true) 메서드를 사용합니다.

```java
config.enableDictation();
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 프랑스어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechConfig`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig?view=azure-java-stable&preserve-view=true)를 찾아서 바로 아래에 다음 줄을 추가합니다.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage?view=azure-java-stable&preserve-view=true)는 문자열을 인수로 사용하는 매개 변수입니다. 지원되는 [로캘/언어](../../../language-support.md) 목록에 원하는 값을 입력할 수 있습니다.

## <a name="improve-recognition-accuracy"></a>인식 정확도 향상

음성 SDK를 사용하여 인식 정확도를 향상하는 몇 가지 방법이 있습니다. 구 목록을 살펴보겠습니다. 구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다. 단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 전체 구와 정확히 일치하는 항목이 오디오에 포함된 경우 구 목록의 항목이 사용됩니다. 구와 정확히 일치하는 항목을 찾을 수 없는 경우 인식이 지원되지 않습니다.

> [!IMPORTANT]
> 구 목록 기능은 영어로만 제공됩니다.

구 목록을 사용하려면 [`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable&preserve-view=true) 개체를 만든 다음, [`AddPhrase`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase?view=azure-java-stable#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar?view=azure-java-stable&preserve-view=true)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

구 목록을 지워야 하는 경우: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../how-to-custom-speech.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
