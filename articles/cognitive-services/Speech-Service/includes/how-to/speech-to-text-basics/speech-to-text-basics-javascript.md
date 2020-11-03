---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: f16b0fb5cf241604c627925a7cd905c1683399fd
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92886651"
---
Speech Service의 핵심 기능 중 하나는 사람의 음성을 인식하여 글로 바꾸는 기능입니다(종종 음성 텍스트 변환이라고도 함). 이 빠른 시작에서는 앱 및 제품에서 Speech SDK를 사용하여 고품질 음성을 텍스트로 변환하는 방법을 알아봅니다.

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [JavaScript 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서는 여러분에게 Azure 계정 및 음성 서비스 구독이 있다고 가정합니다. 계정 및 구독이 없는 경우 [음성 서비스 평가판을 사용해 보세요](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

작업을 수행하려면 먼저 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">JavaScript용 Speech SDK<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 설치해야 합니다. 사용하는 플랫폼에 따라 다음 중 적절한 지침을 따릅니다.

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">웹 브라우저 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

또한 대상 환경에 따라 다음 중 하나를 사용합니다.

# <a name="script"></a>[스크립트](#tab/script)

<a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">JavaScript용 Speech SDK <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* 파일을 다운로드하여 압축을 풀고 HTML 파일에 액세스할 수 있는 폴더에 저장합니다.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> 웹 브라우저를 대상으로 지정하고 `<script>` 태그를 사용하는 경우 클래스를 참조할 때 `sdk` 접두사가 필요하지 않습니다. `sdk` 접두사는 `require` 모듈의 이름을 지정하는 데 사용되는 별칭입니다.

# <a name="require"></a>[필수](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require`에 대한 자세한 내용은 <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">필요한 항목<span class="docon docon-navigate-external x-hidden-focus"></span></a>을 참조하세요.

---

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다. 키와 지역을 사용하여 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true)를 만듭니다. 지역 식별자를 찾으려면 [지역 지원](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) 페이지를 참조하세요.

```javascript
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

[`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true)를 초기화할 수 있는 몇 가지 다른 방법이 있습니다.

* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

## <a name="recognize-from-microphone"></a>마이크에서 인식

디바이스 마이크를 사용하여 음성을 인식하려면 `fromDefaultMicrophoneInput()`을 사용하여 `AudioConfig`를 만듭니다. 그런 다음, [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true)를 초기화하고 `speechConfig` 및 `audioConfig`를 전달합니다.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

function fromMic() {
    let audioConfig = sdk.AudioConfig.fromDefaultMicrophoneInput();
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    console.log('Speak into your microphone.');
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromMic();
```

*특정* 오디오 입력 디바이스를 사용하려면 `AudioConfig`에서 디바이스 ID를 지정해야 합니다. 오디오 입력 디바이스의 [디바이스 ID를 가져오는 방법](../../../how-to-select-audio-input-devices.md)을 알아봅니다.

## <a name="recognize-from-file"></a>파일에서 인식 

# <a name="browser"></a>[브라우저](#tab/browser)

브라우저 기반 JavaScript 환경에서 오디오 파일의 음성을 인식하려면 `fromWavFileInput()` 함수를 사용하여 [`AudioConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest&preserve-view=true)를 만듭니다. `fromWavFileInput()` 함수는 JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) 개체를 매개 변수로 예상합니다.

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

function fromFile() {
    // wavByteContent should be a byte array of the raw wav content
    let file = new File([wavByteContent]);
    let audioConfig = sdk.AudioConfig.fromWavFileInput(file);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
    });
}
fromFile();
```

# <a name="nodejs"></a>[Node.JS](#tab/node)

Node.js의 오디오 파일에서 음성을 인식하려면 node.js 런타임에 JavaScript [`File`](https://developer.mozilla.org/en-US/docs/Web/API/File/File) 개체를 사용할 수 없으므로 푸시 스트림을 사용하는 대체 디자인 패턴을 사용해야 합니다. 코드는 다음과 같습니다.

* `createPushStream()`을 사용하여 푸시 스트림을 만듭니다.
* 읽기 스트림을 만들어 `.wav` 파일을 열고 푸시 스트림에 씁니다.
* 푸시 스트림을 사용하여 오디오 구성을 만듭니다.

```javascript
const fs = require('fs');
const sdk = require("microsoft-cognitiveservices-speech-sdk");
const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

function fromFile() {
    let pushStream = sdk.AudioInputStream.createPushStream();

    fs.createReadStream("YourAudioFile.wav").on('data', function(arrayBuffer) {
        pushStream.write(arrayBuffer.slice());
    }).on('end', function() {
        pushStream.close();
    });
 
    let audioConfig = sdk.AudioConfig.fromStreamInput(pushStream);
    let recognizer = new sdk.SpeechRecognizer(speechConfig, audioConfig);
    recognizer.recognizeOnceAsync(result => {
        console.log(`RECOGNIZED: Text=${result.text}`);
        recognizer.close();
    });
}
fromFile();
```

푸시 스트림을 입력으로 사용하면 오디오 데이터가 원시 PCM이라고 가정합니다(예: 헤더 건너뛰기).
헤더를 건너뛰지 않는 경우에도 API는 특정 경우에 계속 작동하지만, 최상의 결과를 위해 `fs`가 *오디오 데이터의 시작* 에서 시작되도록 헤더를 읽는 논리를 구현하는 것이 좋습니다.

---

## <a name="error-handling"></a>오류 처리

이전 예제에서는 단순히 `result.text`에서 인식된 텍스트를 가져오지만 오류 및 기타 응답을 처리하려면 결과를 처리하는 코드를 작성해야 합니다. 다음 코드는 [`result.reason`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognitionresult?view=azure-node-latest&preserve-view=true#reason) 속성을 평가합니다.

* 인식 결과 `ResultReason.RecognizedSpeech`를 출력합니다.
* 일치하는 인식이 없는 경우 사용자에게 `ResultReason.NoMatch`를 알립니다.
* 오류가 발생하는 경우 오류 메시지 `ResultReason.Canceled`를 출력합니다.

```javascript
switch (result.reason) {
    case ResultReason.RecognizedSpeech:
        console.log(`RECOGNIZED: Text=${result.text}`);
        break;
    case ResultReason.NoMatch:
        console.log("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        const cancellation = CancellationDetails.fromResult(result);
        console.log(`CANCELED: Reason=${cancellation.reason}`);

        if (cancellation.reason == CancellationReason.Error) {
            console.log(`CANCELED: ErrorCode=${cancellation.ErrorCode}`);
            console.log(`CANCELED: ErrorDetails=${cancellation.errorDetails}`);
            console.log("CANCELED: Did you update the subscription info?");
        }
        break;
    }
```

## <a name="continuous-recognition"></a>연속 인식

이전 예제에서는 단일 발화를 인식하는 단일 샷 인식을 사용합니다. 단일 발화의 끝은 끝에서 무음을 수신하거나 최대 15초의 오디오가 처리될 때까지 대기하여 결정됩니다.

이와 대조적으로, 인식을 중지할 때 **제어** 하려는 경우에는 연속 인식이 사용됩니다. `Recognizing`, `Recognized` 및 `Canceled` 이벤트를 구독하여 인식 결과를 얻어야 합니다. 인식을 중지하려면 [`stopContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#stopcontinuousrecognitionasync)를 호출해야 합니다. 다음은 오디오 입력 파일에서 연속 인식이 수행되는 방식을 보여주는 예입니다.

입력을 정의하고 [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true)를 초기화하여 시작합니다.

```javascript
const recognizer = new sdk.SpeechRecognizer(speechConfig);
```

그런 다음, [`SpeechRecognizer`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true)에서 보낸 이벤트를 구독합니다.

* [`recognizing`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognizing): 중간 인식 결과가 포함된 이벤트에 대한 신호입니다.
* [`recognized`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#recognized): 최종 인식 결과가 포함된 이벤트에 대한 신호입니다(성공적인 인식 시도를 나타냄).
* [`sessionStopped`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#sessionstopped): 인식 세션(작업)의 끝을 나타내는 이벤트에 대한 신호입니다.
* [`canceled`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#canceled): 취소된 인식 결과가 포함된 이벤트에 대한 신호입니다(직접 취소 요청이나 전송 또는 프로토콜 오류로 인해 취소된 인식 시도를 나타냄).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`RECOGNIZING: Text=${e.result.text}`);
};

recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`RECOGNIZED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be recognized.");
    }
};

recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);

    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }

    recognizer.stopContinuousRecognitionAsync();
};

recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

모든 설정이 완료되면 [`startContinuousRecognitionAsync`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest&preserve-view=true#startcontinuousrecognitionasync)를 호출하여 인식을 시작합니다.

```javascript
recognizer.startContinuousRecognitionAsync();

// make the following call at some point to stop recognition.
// recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>받아쓰기 모드

연속 인식을 사용하면 해당하는 "받아쓰기 사용" 함수를 사용하여 받아쓰기 처리를 사용하도록 설정할 수 있습니다. 이 모드에서는 음성 구성 인스턴스가 문장 부호와 같은 문장 구조의 단어 설명을 해석합니다. 예를 들어 "도시에 살고 계신가요 물음표"라는 발화는 "도시에 살고 계신가요?"라는 텍스트로 해석됩니다.

받아쓰기 모드를 사용하도록 설정하려면 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true)에서 [`enableDictation`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#enabledictation--) 메서드를 사용합니다.

```javascript
speechConfig.enableDictation();
```

## <a name="change-source-language"></a>소스 언어 변경

음성 인식에 대한 일반적인 작업 중 하나는 입력(또는 소스) 언어를 지정하는 것입니다. 입력 언어를 이탈리아어로 변경하는 방법을 살펴보겠습니다. 코드에서 [`SpeechConfig`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true)를 찾아서 바로 아래에 다음 줄을 추가합니다.

```javascript
speechConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#speechrecognitionlanguage) 속성에는 언어 로캘 형식 문자열이 필요합니다. 지원되는 [로캘/언어](../../../language-support.md) 목록의 **로캘** 열에 원하는 값을 입력할 수 있습니다.

## <a name="improve-recognition-accuracy"></a>인식 정확도 향상

음성을 사용하여 인식 정확도를 향상하는 몇 가지 방법이 있습니다. 구 목록을 살펴보겠습니다. 구 목록은 오디오 데이터에서 사람의 이름이나 특정 위치처럼 알려진 문구를 식별하는 데 사용됩니다. 단일 단어 또는 전체 구를 구 목록에 추가할 수 있습니다. 인식 중에 전체 구와 정확히 일치하는 항목이 오디오에 포함된 경우 구 목록의 항목이 사용됩니다. 구와 정확히 일치하는 항목을 찾을 수 없는 경우 인식이 지원되지 않습니다.

> [!IMPORTANT]
> 구 목록 기능은 영어로만 제공됩니다.

구 목록을 사용하려면 [`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true) 개체를 만든 다음, [`addPhrase`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true#addphrase-string-)를 사용하여 특정 단어와 구를 추가합니다.

[`PhraseListGrammar`](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/phraselistgrammar?view=azure-node-latest&preserve-view=true)를 변경하면 그 다음 인식부터 또는 음성 서비스에 다시 연결한 후에 변경 내용이 적용됩니다.

```javascript
const phraseList = sdk.PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

구 목록을 지워야 하는 경우:

```javascript
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>인식 정확도를 높이는 다른 옵션

구 목록은 인식 정확도를 높이는 여러 옵션 중 하나일 뿐입니다. 다음도 가능합니다. 

* [Custom Speech를 사용하여 정확도 향상](../../../how-to-custom-speech.md)
* [테넌트 모델을 사용하여 정확도 향상](../../../tutorial-tenant-model.md)
