---
title: '빠른 시작: Speech Service SDK를 사용하여 브라우저에서 JavaScript로 음성 인식'
titleSuffix: Azure Cognitive Services
description: Speech Service SDK를 사용하여 브라우저에서 JavaScript로 음성을 인식하는 방법 알아보기
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: b01746c20dbef7726f129badac045c1fb440f602
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49467466"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-speech-service-sdk"></a>빠른 시작: Speech Service SDK를 사용하여 브라우저에서 JavaScript로 음성 인식

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

이 문서에서는 음성을 텍스트로 변환하는 Cognitive Services Speech SDK의 JavaScript 바인딩을 사용하여 웹 사이트를 만드는 방법을 설명합니다.
이 응용 프로그램은 Cognitive Services Speech SDK([다운로드 버전 1.0.1](https://aka.ms/csspeech/jsbrowserpackage))를 기반으로 합니다.

## <a name="prerequisites"></a>필수 조건

* Speech Service에 대한 구독 키. [Speech Service를 무료로 체험해보기](get-started.md)를 참조하세요.
* 작동하는 마이크가 있는 PC 또는 Mac.
* 텍스트 편집기.
* 최신 버전의 Chrome 또는 Microsoft Edge.
* 필요에 따라 PHP 스크립트 호스팅을 지원하는 웹 서버.

## <a name="create-a-new-website-folder"></a>새 웹 사이트 폴더 만들기

빈 폴더를 새로 만듭니다. 웹 서버에 샘플을 호스트하려면 웹 서버가 폴더에 액세스할 수 있는지 확인합니다.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>JavaScript용 Speech SDK를 해당 폴더에 압축 해제합니다.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Speech SDK를 [.zip 패키지](https://aka.ms/csspeech/jsbrowserpackage)로 다운로드하여 새로 만든 폴더에 압축 해체합니다. 그러면 압축이 풀린 두 개의 파일(`microsoft.cognitiveservices.speech.sdk.bundle.js` 및 `microsoft.cognitiveservices.speech.sdk.bundle.js.map`)이 생깁니다.
두 번째 파일은 옵션이며, 필요할 때 SDK 코드를 디버깅하는 데 사용됩니다.

## <a name="create-an-indexhtml-page"></a>Index.html 페이지 만들기

폴더에 `index.html`라는 새 파일을 만들고 텍스트 편집기에서 이 파일을 엽니다.

1. 다음 HTML 기본 구조를 만듭니다.

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. 첫 번째 주석 아래에서 다음 UI 코드를 파일에 추가합니다.

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Speech SDK 참조 추가

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. UI 코드에 정의된 인식 단추, 인식 결과 및 구독 관련 필드의 처리기를 연결합니다.

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>토큰 소스 만들기(선택 사항)

웹 서버에 웹 페이지를 호스트하려는 경우 필요에 따라 데모 응용 프로그램의 토큰 소스를 제공하면 됩니다.
이 방식을 사용하면 사용자가 권한 부여 코드를 직접 입력하지 않고 음성 기능을 사용할 수 있으면서도 구독 키가 절대 서버를 벗어나지 않습니다.

1. 이름이 `token.php`인 새 파일을 만듭니다. 이 예제에서는 웹 서버에서 PHP 스크립팅 언어를 지원한다고 가정하겠습니다. 다음 코드를 입력합니다.

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. `index.html` 파일을 편집하고 다음 코드를 파일에 추가합니다.

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> 권한 부여 토큰의 수명은 제한되어 있습니다.
> 이 간단한 예제에서는 권한 부여 토큰을 자동으로 새로 고치는 방법을 보여주지 않습니다. 사용자는 수동으로 페이지를 다시 로드하거나 F5 키를 눌러 새로 고칠 수 있습니다.

## <a name="build-and-run-the-sample-locally"></a>로컬로 샘플을 빌드 및 실행

앱을 시작하려면 index.html 파일을 두 번 클릭하거나 선호하는 웹 브라우저를 사용하여 index.html을 엽니다. 구독 키 및 [영역](regions.md)을 입력하고 마이크로 인식을 트리거할 수 있는 간단한 GUI가 제공됩니다.

## <a name="build-and-run-the-sample-via-a-web-server"></a>웹 서버를 통해 샘플 빌드 및 실행

앱을 시작하려면 선호하는 웹 브라우저를 열고 폴더를 호스트하는 공용 URL을 가리킨 다음, [영역](regions.md)을 입력하고, 마이크를 사용하여 인식을 트리거합니다. 구성된 경우 토큰 소스에서 토큰을 획득합니다.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
이 예제를 `quickstart/js-browser` 폴더에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [샘플 가져오기](speech-sdk.md#get-the-samples)
