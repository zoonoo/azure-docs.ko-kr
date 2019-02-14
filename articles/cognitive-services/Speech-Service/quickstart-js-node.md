---
title: '빠른 시작: 음성 인식, Node.js - Speech Services'
titleSuffix: Azure Cognitive Services
description: 이 가이드에 따라 Node.js용 Speech SDK를 사용하여 음성을 텍스트로 변환하는 콘솔 애플리케이션을 만듭니다. 작업을 마치면 컴퓨터의 마이크를 사용하여 실시간으로 음성을 텍스트로 변환할 수 있습니다.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: fmegen
ms.openlocfilehash: 5fa0b6d2a361fdbf372ba73fed9d8e2c48cb3630
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55871220"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>빠른 시작: Node.js용 Speech SDK를 사용하여 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 음성을 텍스트로 변환하는 Azure Cognitive Services용 Speech SDK의 JavaScript 바인딩을 사용하여 Node.js 프로젝트를 만드는 방법을 보여줍니다.
이 애플리케이션은 [JavaScript용 Speech SDK](https://aka.ms/csspeech/npmpackage)를 기반으로 합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 Azure 구독 키입니다. [무료로 가져올 수 있습니다](get-started.md).
* 현재 버전의 [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>새 프로젝트 만들기

새 폴더를 만들고 프로젝트를 시작합니다.

```sh
npm init -f
```

이 명령은 기본값을 사용하여 **package.json** 파일을 초기화합니다. 나중에 이 파일을 편집할 수도 있습니다.

## <a name="install-the-speech-sdk"></a>Speech SDK 설치하기

Speech SDK를 Node.js 프로젝트에 추가합니다.

```
npm install microsoft-cognitiveservices-speech-sdk
```

이 명령은 **npmjs**에서 최신 버전의 Speech SDK 및 모든 필수 구성 요소를 다운로드하고 설치합니다. SDK는 프로젝트 폴더 내의 `node_modules` 디렉터리에 설치됩니다.

## <a name="use-the-speech-sdk"></a>Speech SDK 사용

폴더에 `index.js`라는 새 파일을 만들고 텍스트 편집기에서 이 파일을 엽니다.

> [!NOTE]
> Node.js에서 Speech SDK는 마이크 또는 **파일** 데이터 형식을 지원하지 않습니다. 브라우저에서만 지원됩니다. 대신 `AudioInputStream.createPushStream()` 또는 `AudioInputStream.createPullStream()`을 통해 Speech SDK에 **Stream** 인터페이스를 사용합니다.

이 예제에서는 `PushAudioInputStream` 인터페이스를 사용합니다.

이 JavaScript 코드를 추가합니다.

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>샘플 실행

앱을 열려면 구성에 `YourSubscriptionKey`, `YourServiceRegion` 및 `YourAudioFile.wav`를 적응합니다. 그런 다음, 이 명령을 호출하여 실행합니다.

```sh
node index.js
```

이는 제공된 파일 이름을 사용하여 인식을 트리거합니다. 그리고 콘솔에 출력을 표시합니다.

이 샘플은 구독 키를 업데이트하고 `whatstheweatherlike.wav` 파일을 사용한 후 `index.js`를 실행할 때의 출력입니다.

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Visual Studio Code를 사용하여 Speech SDK 설치 및 사용

Visual Studio Code에서도 샘플을 실행할 수 있습니다. 다음 단계를 따라 빠른 시작을 설치, 열기 및 실행합니다.

1. Visual Studio Code를 시작합니다. **폴더 열기**를 선택합니다. 그런 다음, 빠른 시작 폴더로 이동합니다.

   ![폴더 열기](media/sdk/qs-js-node-01-open_project.png)

1. Visual Studio Code에서 터미널을 엽니다.

   ![터미널 창](media/sdk/qs-js-node-02_open_terminal.png)

1. `npm`을 실행하여 종속성을 설치합니다.

   ![npm install](media/sdk/qs-js-node-03-npm_install.png)

1. 이제 `index.js`를 열고 중단점을 설정할 준비가 되었습니다.

   ![16줄에 중단점이 있는 index.js](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. 디버깅을 시작하려면 F5 키를 누르거나 메뉴에서 **디버그/디버깅 시작**을 선택합니다.

   ![디버그 메뉴](media/sdk/qs-js-node-05-start_debugging.png)

1. 중단점이 적중되면 호출 스택 및 변수를 검사할 수 있습니다.

   ![디버거](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. 출력이 디버그 콘솔 창에 표시됩니다.

   ![디버그 콘솔](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [GitHub에서 Node.js 샘플 살펴보기](https://aka.ms/csspeech/samples)
