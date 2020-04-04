---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: 1f1512fbd766faed3922e4bdf8a47dba0de69864
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656612"
---
:::row:::
    :::column span="3":::
        자바 스크립트 음성 SDK는 npm 패키지로 사용할 수 있습니다, <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">마이크로 <span class="docon docon-navigate-external x-hidden-focus"></span> 소프트 인지 서비스 음성 sdk를</a> 참조하고 그것의 동반자 GitHub 리포지토리 <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">인지 서비스 - 음성 - sdk-js <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="JavaScript" src="https://docs.microsoft.com/media/logos/logo_js.svg"  width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> JavaScript 음성 SDK는 npm 패키지로 사용할 수 있지만 따라서 클라이언트 웹 브라우저와 Node.js 모두 사용할 수 있습니다 - 각 환경의 다양한 아키텍처 영향을 고려하십시오. 예를 들어 파일 <a href="https://nodejs.org/api/fs.html" target="_blank">시스템을 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 클라이언트 쪽 응용 프로그램에서 사용할 수 없는 것처럼 <a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">문서 개체 모델(DOM)은 <span class="docon docon-navigate-external x-hidden-focus"></span> </a> 서버 쪽 응용 프로그램에 사용할 수 없습니다.

### <a name="nodejs-package-manager-npm"></a>노드.js 패키지 관리자(NPM)

자바 스크립트 음성 SDK를 설치하려면 `npm install` 아래 명령을 실행하십시오.

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

### <a name="html-script-tag"></a>HTML 스크립트 태그

또는 <a href="https://www.jsdelivr.com/package/npm/microsoft-cognitiveservices-speech-sdk" target="_blank"> **JSDelivr** NPM 신디케이트에 <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> `<head>` 의존하여 HTML 요소에 `<script>` 태그를 직접 포함할 수 있습니다.

```html
<script src="https://cdn.jsdelivr.net/npm/microsoft-cognitiveservices-speech-sdk@1.10.1/distrib/lib/microsoft.cognitiveservices.speech.sdk.min.js">
</script>
```

자세한 내용은 웹 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser" target="_blank">브라우저 음성 SDK <span class="docon docon-navigate-external x-hidden-focus"> </span>빠른 시작을 </a>참조하십시오.
