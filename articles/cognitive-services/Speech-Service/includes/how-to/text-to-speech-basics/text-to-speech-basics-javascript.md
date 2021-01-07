---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/15/2020
ms.author: trbye
ms.custom: devx-track-js
ms.openlocfilehash: f0335e28b3f0fd2d339aa329b92203ddb8d3216c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425004"
---
이 빠른 시작에서는 Speech SDK를 사용하여 텍스트 음성 변환을 수행하기 위한 일반적인 디자인 패턴에 대해 알아봅니다. 먼저 기본 구성 및 합성을 수행하고 다음과 같은 사용자 지정 애플리케이션 개발을 위한 고급 예제로 이동합니다.

* 메모리 내 스트림으로 응답 가져오기
* 출력 샘플 속도 및 비트 전송률 사용자 지정
* SSML(음성 합성 마크업 언어)을 사용하여 합성 요청 제출
* 인공신경망 음성 사용

## <a name="skip-to-samples-on-github"></a>GitHub의 샘플로 건너뛰기

샘플 코드로 바로 건너뛰려면 GitHub의 [JavaScript 빠른 시작 샘플](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)을 참조하세요.

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
> 웹 브라우저를 대상으로 지정하고 `<script>` 태그를 사용하는 경우 `sdk` 접두사는 필요하지 않습니다. `sdk` 접두사는 `require` 모듈의 이름을 지정하는 데 사용되는 별칭입니다.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

`import`에 대한 자세한 내용은 <a href="https://javascript.info/import-export" target="_blank">내보내기 및 가져오기<span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

# <a name="require"></a>[필수](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require`에 대한 자세한 내용은 <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">필요한 항목<span class="docon docon-navigate-external x-hidden-focus"></span></a>을 참조하세요.

---


## <a name="create-a-speech-configuration"></a>음성 구성 만들기

음성 SDK를 사용하여 음성 서비스를 호출하려면 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)를 만들어야 합니다. 이 클래스에는 키 및 연결된 지역, 엔드포인트, 호스트 또는 권한 부여 토큰과 같은 구독에 대한 정보가 포함됩니다.

> [!NOTE]
> 음성 인식, 음성 합성, 번역 또는 의도 인식을 수행하고 있는지 여부에 관계없이 항상 구성을 만들게 됩니다.

[`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)를 초기화하는 다음과 같은 방법이 있습니다.

* 구독 사용: 키 및 연결된 영역을 전달합니다.
* 엔드포인트 사용: 음성 서비스 엔드포인트를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 호스트 사용: 호스트 주소를 전달합니다. 키 또는 권한 부여 토큰은 선택 사항입니다.
* 권한 부여 토큰 사용: 권한 부여 토큰 및 연결된 영역을 전달합니다.

이 예제에서는 구독 키와 지역을 사용하여 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest)를 만듭니다. [음성 서비스 무료로 사용해 보기](../../../overview.md#try-the-speech-service-for-free)의 단계를 따라 이러한 자격 증명을 가져오세요. 또한 이 문서의 나머지 부분에 사용할 몇 가지 기본 상용구 코드를 만들 수 있습니다. 이 문서의 나머지 부분에서는 사용자 지정을 위해 수정합니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
}

synthesizeSpeech();
```

## <a name="synthesize-speech-to-a-file"></a>음성을 파일로 합성

다음으로, 텍스트 음성 변환을 실행하고 스피커, 파일 또는 기타 출력 스트림으로 출력하는 [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?preserve-view=true&view=azure-node-latest) 개체를 만듭니다. [`SpeechSynthesizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer?preserve-view=true&view=azure-node-latest)는 이전 단계에서 만든 [`SpeechConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?preserve-view=true&view=azure-node-latest) 개체와 출력 결과를 처리하는 방법을 지정하는 [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?preserve-view=true&view=azure-node-latest) 개체를 매개 변수로 수락합니다.

시작하려면 `AudioConfig`를 만들어 `fromAudioFileOutput()` 정적 함수를 사용하여 출력을 `.wav` 파일에 자동으로 작성합니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path/to/file.wav");
}
```

다음으로, `speechConfig` 개체와 `audioConfig` 개체를 매개 변수로 전달하는 `SpeechSynthesizer`를 인스턴스화합니다. 그러면 음성 합성을 실행하고 파일에 쓰는 작업이 텍스트 문자열을 사용하여 `speakTextAsync()`를 실행하는 것만큼 간단합니다. 결과 콜백은 실제로 `synthesizer.close()`를 호출하는 데 적합합니다. 이 호출은 합성이 제대로 작동하기 위해 필요합니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromAudioFileOutput("path-to-file.wav");

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "A simple test to write to a file.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

프로그램이 실행되면 합성된 `.wav` 파일을 지정한 위치에 씁니다. 이는 가장 기본적인 사용법의 좋은 예이지만, 다음으로 출력을 사용자 지정하고 출력 응답을 메모리 내 스트림으로 처리하여 사용자 지정 시나리오를 사용하는 방법을 살펴봅니다.

## <a name="synthesize-to-speaker-output"></a>스피커 출력으로 합성

경우에 따라 합성된 음성을 스피커로 직접 출력할 수도 있습니다. 이렇게 하려면 `fromDefaultSpeakerOutput()` 정적 함수를 사용하여 `AudioConfig`를 인스턴스화합니다. 현재 활성 출력 디바이스로 출력됩니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const audioConfig = AudioConfig.fromDefaultSpeakerOutput();

    const synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
    synthesizer.speakTextAsync(
        "Synthesizing directly to speaker output.",
        result => {
            if (result) {
                console.log(JSON.stringify(result));
            }
            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

## <a name="get-result-as-an-in-memory-stream"></a>결과를 메모리 내 스트림으로 가져오기

음성 애플리케이션을 개발하는 많은 시나리오에서는 파일에 직접 쓰지 않고 결과 오디오 데이터가 메모리 내 스트림으로 필요할 수 있습니다. 이렇게 하면 다음과 같은 사용자 지정 동작을 빌드할 수 있습니다.

* 결과 바이트 배열을 사용자 지정 다운스트림 서비스에 대한 검색 가능한 스트림으로 추상화합니다.
* 결과를 다른 API 또는 서비스와 통합합니다.
* 오디오 데이터를 수정하고, 사용자 지정 `.wav` 헤더 등을 작성합니다.

이전 예제에서 이 변경을 수행하는 것은 간단합니다. 먼저, 제어를 향상시키기 위해 이 시점부터 출력 동작을 수동으로 관리하므로 `AudioConfig` 블록을 제거합니다. 그런 다음, `SpeechSynthesizer` 생성자에서 `AudioConfig`에 대한 `undefined`을 전달합니다. 

> [!NOTE]
> 위의 스피커 출력 예제와 같이 생략하는 대신 `AudioConfig`에 대해 `undefined`을 전달하면 현재 활성 출력 디바이스에서 기본적으로 오디오가 재생되지 않습니다.

이번에는 결과를 [`SpeechSynthesisResult`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisresult?preserve-view=true&view=azure-node-latest) 변수에 저장합니다. `SpeechSynthesisResult.audioData` 속성은 출력 데이터의 `ArrayBuffer`를 반환합니다. 이 `ArrayBuffer`를 수동으로 작업할 수 있습니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig);

    synthesizer.speakTextAsync(
        "Getting the response as an in-memory stream.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

여기서 결과 `ArrayBuffer` 개체를 사용하여 사용자 지정 동작을 구현할 수 있습니다.

## <a name="customize-audio-format"></a>오디오 형식 사용자 지정

다음 섹션에서는 아래와 같은 오디오 출력 특성을 사용자 지정하는 방법을 보여 줍니다.

* 오디오 파일 형식
* 샘플 속도
* 비트 수준

오디오 형식을 변경하려면 `SpeechConfig` 개체에서 `speechSynthesisOutputFormat` 속성을 사용합니다. 이 속성에는 출력 형식을 선택하는 데 사용하는 [`SpeechSynthesisOutputFormat`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest) 형식의 `enum`이 필요합니다. 사용 가능한 [오디오 형식 목록](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesisoutputformat?preserve-view=true&view=azure-node-latest)은 참조 문서를 참조하세요.

요구 사항에 따라 다양한 파일 형식에 대한 다양한 옵션이 있습니다. 정의에 따라 `Raw24Khz16BitMonoPcm`과 같은 원시 형식에는 오디오 헤더가 포함되지 않습니다. 다운스트림 구현에서 원시 비트 스트림을 디코딩할 수 있음을 것을 알고 있거나 비트 수준, 샘플 속도, 채널 수 등에 따라 헤더를 수동으로 작성하려는 경우에만 원시 형식을 사용합니다.

다음 예제에서는 `SpeechConfig` 개체에서 `speechSynthesisOutputFormat`을 설정하여 고화질 RIFF 형식인 `Riff24Khz16BitMonoPcm`을 지정합니다. 이전 섹션의 예제와 마찬가지로 오디오 `ArrayBuffer` 데이터를 가져와 상호 작용합니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");

    // Set the output format
    speechConfig.speechSynthesisOutputFormat = SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm;

    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);
    synthesizer.speakTextAsync(
        "Customizing audio output format.",
        result => {
            // Interact with the audio ArrayBuffer data
            const audioData = result.audioData;
            console.log(`Audio data byte size: ${audioData.byteLength}.`)

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

프로그램이 다시 실행되면 `.wav` 파일을 지정한 경로에 씁니다.

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML을 사용하여 음성 특성 사용자 지정

SSML(Speech Synthesis Markup Language)을 사용하면 XML 스키마에서 요청을 제출하여 피치, 발음, 말하기 속도, 볼륨 및 더 많은 텍스트 음성 변환 출력을 미세 조정할 수 있습니다. 이 섹션에서는 몇 가지 실제 사용 예제를 보여 주지만, 더 자세한 내용은 [SSML 방법 문서](../../../speech-synthesis-markup.md)를 참조하세요.

SSML을 사용자 지정에 사용하려면 음성을 전환하는 간단한 변경 작업을 수행합니다.
먼저, SSML 구성에 대한 새 XML 파일(이 예제에서는 `ssml.xml`)을 루트 프로젝트 디렉터리에 만듭니다. 루트 요소는 항상 `<speak>`이며, 텍스트를 `<voice>` 요소에 래핑하면 `name` 매개 변수를 사용하여 음성을 변경할 수 있습니다. 다음 예제에서는 음성을 남성 영어(영국) 음성으로 변경합니다. 이 음성은 **표준** 음성이며, 가격 책정 및 가용성이 **인공신경망** 음성과 다릅니다. 지원되는 **표준** 음성에 대한 [전체 목록](../../../language-support.md#standard-voices)을 참조하세요.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

다음으로, XML 파일을 참조하도록 음성 합성 요청을 변경해야 합니다. 요청은 거의 같지만 `speakTextAsync()` 함수를 사용하는 대신 `speakSsmlAsync()`을 사용합니다. 이 함수에는 XML 문자열이 필요하므로, 먼저 XML 파일을 로드하고 문자열로 반환하는 함수를 만듭니다.

```javascript
function xmlToString(filePath) {
    const xml = readFileSync(filePath, "utf8");
    return xml;
}
```

`readFileSync`에 대한 자세한 내용은 <a href="https://nodejs.org/api/fs.html#fs_fs_readlinksync_path_options" target="_blank">Node.js 파일 시스템<span class="docon docon-navigate-external x-hidden-focus"></span></a>을 참조하세요. 여기서 결과 개체는 이전 예제와 정확히 동일합니다.

```javascript
function synthesizeSpeech() {
    const speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
    const synthesizer = new sdk.SpeechSynthesizer(speechConfig, undefined);

    const ssml = xmlToString("ssml.xml");
    synthesizer.speakSsmlAsync(
        ssml,
        result => {
            if (result.errorDetails) {
                console.error(result.errorDetails);
            } else {
                console.log(JSON.stringify(result));
            }

            synthesizer.close();
        },
        error => {
            console.log(error);
            synthesizer.close();
        });
}
```

출력은 작동하지만 보다 자연스럽게 들리도록 몇 가지 간단한 추가 변경 사항이 있습니다. 전반적인 말하기 속도가 너무 빠르므로 `<prosody>` 태그를 추가하고 속도를 기본 속도의 **90%** 로 줄입니다. 또한 문장에서 쉼표 뒤의 일시 중지는 너무 짧고 부자연스러운 소리입니다. 이 문제를 해결하려면 `<break>` 태그를 추가하여 음성을 지연시키고 시간 매개 변수를 **200ms** 로 설정합니다. 합성을 다시 실행하여 이러한 사용자 지정이 출력에 미치는 영향을 확인합니다.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>인공신경망 음성

인공신경망은 심층 신경망에서 구동되는 음성 합성 알고리즘입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

인공신경망 음성으로 전환하려면 `name`을 [인공신경망 음성 옵션](../../../language-support.md#neural-voices) 중 하나로 변경합니다. 그런 다음, `mstts`에 대한 XML 네임스페이스를 추가하고, `<mstts:express-as>` 태그에서 텍스트를 래핑합니다. `style` 매개 변수를 사용하여 말하기 스타일을 사용자 지정합니다. 다음 예제에서는 `cheerful`을 사용하지만, 말하기 스타일의 차이를 확인하려면 `customerservice` 또는 `chat`으로 설정해 보세요.

> [!IMPORTANT]
> 인공신경망 음성은 *미국 동부* , *동남아시아* , *서유럽* 지역에서 만든 **음성 리소스에 대해서만** 지원됩니다.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
    xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```