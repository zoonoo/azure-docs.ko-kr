---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5545b10a4228448d49849e7cd52728febf14ce2d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400035"
---
:::row:::
    :::column span="3":::
        JavaScript Speech sdk는 npm 패키지로 사용할 수 있습니다. <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">cognitiveservices account <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -a s i-a s i-a s <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span>a. </a>
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript Speech SDK는 npm 패키지로 사용할 수 있으므로 클라이언트 웹 브라우저와 node.js는 모두 사용할 수 있습니다. 각 환경에서 다양 한 아키텍처의 영향을 고려 합니다. 예를 들어 클라이언트 쪽 응용 프로그램에서 <a href="https://nodejs.org/api/fs.html" target="_blank">파일 시스템 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 을 사용할 수 없는 경우와 마찬가지로 서버 쪽 응용 프로그램에서는 <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">DOM (문서 개체 모델) <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 을 사용할 수 없습니다.

### <a name="nodejs-package-manager-npm"></a>Node.js 패키지 관리자 (NPM)

JavaScript Speech SDK를 설치 하려면 아래 `npm install` 명령을 실행 합니다.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 스크립트 태그

또는 HTMLs `<script>` `<head>` 요소에 태그를 직접 포함 하 여 <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM 게시 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>를 사용할 수 있습니다.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

자세한 내용은 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">웹 브라우저 음성 SDK 퀵 스타트 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>를 참조 하세요.
