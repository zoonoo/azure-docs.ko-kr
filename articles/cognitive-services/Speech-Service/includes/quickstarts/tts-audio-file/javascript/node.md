---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 19c054b72f7aa2ed6108d076a48856919a01462d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838767"
---
## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 수행합니다.

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 만들기<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md)

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다. index.js 파일을 만들고 이 코드를 추가합니다.

`subscriptionKey`, `servcieRegion` 및 `filename`에 대한 값을 입력해야 합니다.

```JavaScript
(function() {
  // <code>
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
 
}());
  
```
## <a name="load-the-file-into-an-pullaudiooutputstream"></a>파일을 PullAudioOutputStream에 로드

NodeJS의 경우 Speech SDK는 기본적으로 파일 액세스를 직접 지원하지 않으므로 파일을 열고 `PullAudioOutputStream`을 사용하여 파일에 씁니다.


```JavaScript
// create the push stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and push it to the push stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
```

## <a name="create-a-speech-configuration"></a>음성 구성 만들기

`SpeechSynthesizer` 개체를 초기화하기 전에 구독 키와 구독 지역을 사용하는 구성을 만들어야 합니다. 다음으로 이 코드를 삽입합니다.

> [!NOTE]
> Speech SDK는 기본적으로 언어에 en-us를 사용하여 인식합니다. 원본 언어 선택에 대한 자세한 내용은 [음성 텍스트 변환에 대한 원본 언어 지정](../../../../how-to-specify-source-language.md)을 참조하세요.

 ```JavaScript
   // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
```
## <a name="create-an-audio-configuration"></a>오디오 구성 만들기

이제 `PullAudioOutputStream`을 가리키는 `AudioConfig` 개체를 만들어야 합니다. 음성 구성 바로 아래에 이 코드를 삽입합니다.

```JavaScript
    var audioConfig = sdk.AudioConfig.fromStreamInput(pullStream);
```

## <a name="initialize-a-speechsynthesizer"></a>SpeechSynthesizer 초기화

이제 앞에서 만든 `SpeechConfig` 및 `AudioConfig` 개체를 사용하여 `SpeechSynthesizer` 개체를 만들어 보겠습니다.

```JavaScript
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
```
## <a name="recognize-a-phrase-and-display-results"></a>구문 인식 및 결과 표시

`SpeechSynthesizer` 개체에서 `speakTextAsync()` 메서드를 호출합니다. 이 방법을 사용하면 Speech Service에서 합성을 위해 텍스트를 보내고 있음을 알 수 있습니다.

또한 반환된 결과 또는 오류를 콘솔에 쓰고 마지막으로 신시사이저를 닫습니다.
```JavaScript
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);
```
## <a name="check-your-code"></a>코드 확인
```JavaScript
(function() {
  "use strict";
  
  // pull in the required packages.
  var sdk = require("microsoft-cognitiveservices-speech-sdk");
  var fs = require("fs");
  
  // replace with your own subscription key,
  // service region (e.g., "westus"), and
  // the name of the file you want to run
  // through the speech synthesizer.
  var subscriptionKey = "YourSubscriptionKey";
  var serviceRegion = "YourServiceRegion"; // e.g., "westus"
  var filename = "YourAudioFile.wav"; // 16000 Hz, Mono
  
  // create the pull stream we need for the speech sdk.
  var pullStream = sdk.AudioOutputStream.createPullStream();
  
  // open the file and write it to the pull stream.
  fs.createWriteStream(filename).on('data', function(arrayBuffer) {
    pullStream.read(arrayBuffer.slice());
  }).on('end', function() {
    pullStream.close();
  });
 
  // now create the audio-config pointing to our stream and
  // the speech config specifying the language.
  var speechConfig = sdk.SpeechConfig.fromSubscription(subscriptionKey, serviceRegion);
  
  // setting the recognition language to English.
  speechConfig.speechRecognitionLanguage = "en-US";
  
  var audioConfig = sdk.AudioConfig.fromStreamOutput(pullStream);
  
  // create the speech synthesizer.
  var synthesizer = new sdk.SpeechSynthesizer(speechConfig, audioConfig);
  
 // we are done with the setup
  var text = "Hello World"
  console.log("Now sending text '" + text + "' to: " + filename);
  
  // start the synthesizer and wait for a result.
  synthesizer.speakTextAsync(
    text,
    function (result) {
      console.log(result);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    function (err) {
      console.trace("err - " + err);
  
      synthesizer.close();
      synthesizer = undefined;
    },
    filename);

}());
```
## <a name="run-the-sample-locally"></a>로컬에서 샘플 실행

NodeJs를 사용하여 코드 실행
```bash
node index.js
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [Speech synthesis basics](../../text-to-speech-next-steps.md)]