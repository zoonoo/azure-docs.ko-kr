---
title: '자습서: 몰입형 판독기(Node.js) 시작'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 몰입형 판독기를 시작하는 Node.js 애플리케이션을 만듭니다.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ac90496c950d8a563bf8794b4c1bb105b6c12232
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444072"
---
# <a name="tutorial-launch-the-immersive-reader-nodejs"></a>자습서: 몰입형 판독기(Node.js) 시작

[개요](./overview.md)에서는 몰입형 판독기란 무엇이며 어떻게 언어 학습자, 신흥 독자 및 학습 차이가 있는 사람들의 독해력 향상을 위해 입증된 기술을 구현하는지에 대해 알아보았습니다. 이 자습서에서는 몰입형 판독기를 시작하는 Node.js 애플리케이션을 만드는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Express에서 Node.js 웹앱 만들기
> * 액세스 토큰 획득
> * 샘플 콘텐츠를 사용하여 몰입형 판독기 시작
> * 콘텐츠 언어 지정
> * 몰입형 판독기 인터페이스의 언어 지정
> * 수학 콘텐츠를 사용하여 몰입형 판독기 시작

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

* 몰입형 판독기에 대한 구독 키 [이 지침](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)에 따라 확보합니다.
* [Node.js](https://nodejs.org/) 및 [Yarn](https://yarnpkg.com)
* [Visual Studio Code](https://code.visualstudio.com/)와 같은 IDE

## <a name="create-a-nodejs-web-app-with-express"></a>Express에서 Node.js 웹앱 만들기

`express-generator` 도구로 Node.js를 만듭니다.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Yarn 종속성을 설치하고, 자습서에서 나중에 사용하게 될 `request` 및 `dotenv` 종속성을 추가합니다.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-access-token"></a>액세스 토큰 획득

이제 구독 키를 사용하여 액세스 토큰을 검색하는 백엔드 API를 작성합니다. 다음 단계에는 구독 키 및 엔드포인트가 필요합니다. Azure Portal에서 몰입형 판독기 리소스의 키 페이지에서 구독 키를 찾을 수 있습니다. 개요 페이지에서 엔드포인트를 찾을 수 있습니다.

구독 키와 엔드포인트가 있으면 이름이 _.env_인 새 파일을 만들고 다음 코드를 그 안에 붙여 넣어 `{YOUR_SUBSCRIPTION_KEY}` 및 `{YOUR_ENDPOINT}`를 각각 구독 키와 엔드포인트로 바꿉니다.

```text
SUBSCRIPTION_KEY={YOUR_SUBSCRIPTION_KEY}
ENDPOINT={YOUR_ENDPOINT}
```

이 파일에는 공개되어서는 안 되는 비밀이 있으므로 이 파일을 소스 제어에 커밋하지 않아야 합니다.

이제 _app.js_를 열고 파일 맨 위에 다음을 추가합니다. 이것은 구독 키와 엔드포인트를 Node에 환경 변수로 로드합니다.

```javascript
require('dotenv').config();
```

_routes\index.js_ 파일을 열고 파일 맨 위에서 다음 가져오기를 엽니다.

```javascript
var request = require('request');
```

이제 다음 코드를 이 줄 바로 아래에 추가합니다. 이 코드는 구독 키를 사용하여 액세스 토큰을 획득한 다음, 이 토큰을 반환하는 API 엔드포인트를 만듭니다.

```javascript
router.get('/token', function(req, res, next) {
  request.post({
    headers: {
        'Ocp-Apim-Subscription-Key': process.env.SUBSCRIPTION_KEY,
        'content-type': 'application/x-www-form-urlencoded'
    },
    url: process.env.ENDPOINT
  },
  function(err, resp, token) {
    return res.send(token);
  });
});
```

이 API 엔드포인트는 어떤 인증의 형태(예: [OAuth](https://oauth.net/2/))로 보호되어야 합니다. 이 자습서에서는 이 범위를 다루지 않습니다.

## <a name="launch-the-immersive-reader-with-sample-content"></a>샘플 콘텐츠를 사용하여 몰입형 판독기 시작

1. _views\layout.pug_를 열고 `head` 태그 아래, `body` 태그 앞에 다음 코드를 추가합니다. 이 `script` 태그는 [몰입형 판독기 SDK](https://github.com/Microsoft/immersive-reader-sdk) 및 jQuery를 로드합니다.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. _views\index.pug_를 열고 콘텐츠를 다음 코드로 바꿉니다. 이 코드는 일부 샘플 콘텐츠로 페이지를 입력하고, 몰입형 판독기를 시작하는 단추를 추가합니다.

    ```pug
    extends layout

    block content
      h2(id='title') Geography
      p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
      div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
      script.
        function launchImmersiveReader() {
          // First, get a token using our /token endpoint
          $.ajax('/token', { success: token => {
            // Second, grab the content from the page
            const content = {
              title: document.getElementById('title').innerText,
              chunks: [ {
                content: document.getElementById('content').innerText + '\n\n',
                lang: 'en'
              } ]
            };

            // Third, launch the Immersive Reader
            ImmersiveReader.launchAsync(token, content);
          }});
        }
    ```

3. 이제 웹앱이 준비되었습니다. 다음을 실행하여 앱을 실행합니다.

    ```bash
    npm start
    ```

4. 브라우저를 열고, _http://localhost:3000_ 으로 이동합니다. 위 콘텐츠가 페이지에 표시됩니다. **몰입형 판독기** 단추를 클릭하여 콘텐츠로 몰입형 판독기를 시작합니다.

## <a name="specify-the-language-of-your-content"></a>콘텐츠 언어 지정

몰입형 판독기는 여러 언어를 지원합니다. 아래의 단계에 따라 콘텐츠 언어를 지정할 수 있습니다.

1. _views\index.pug_를 열고 이전 단계에서 추가한 `p(id=content)` 태그 아래에 다음 코드를 추가합니다. 이 코드는 페이지에 스페인어 콘텐츠를 추가합니다.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. JavaScript 코드에서 `ImmersiveReader.launchAsync` 호출 위에 다음을 추가합니다. 이 코드는 몰입형 판독기에 스페인어 콘텐츠를 전달합니다.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. _http://localhost:3000_ 으로 다시 이동합니다. 페이지에 스페인어 텍스트가 표시되며 **몰입형 판독기**를 클릭하면 몰입형 판독기도 표시됩니다.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>몰입형 판독기 인터페이스의 언어 지정

기본적으로 몰입형 판독기 인터페이스의 언어는 브라우저의 언어 설정과 일치합니다. 다음 코드를 통해 몰입형 판독기 인터페이스의 언어를 지정할 수도 있습니다.

1. _views\index.pug_에서 `ImmersiveReader.launchAsync(token, content)` 호출을 아래 코드로 바꿉니다.

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, content, options);
    ```

2. _http://localhost:3000_ 으로 이동합니다. 몰입형 판독기를 시작하면 인터페이스가 프랑스어로 표시됩니다.

## <a name="launch-the-immersive-reader-with-math-content"></a>수학 콘텐츠를 사용하여 몰입형 판독기 시작

[MathML](https://developer.mozilla.org/en-US/docs/Web/MathML)을 사용하여 몰입형 판독기에 수학 콘텐츠를 포함시킬 수 있습니다.

1. `ImmersiveReader.launchAsync` 호출 위에 다음 코드를 포함하여 _views\index.pug_를 수정합니다.

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. _http://localhost:3000_ 으로 이동합니다. 몰입형 판독기를 시작하고 아래쪽으로 스크롤하면 수학 수식이 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [몰입형 판독기 SDK](https://github.com/Microsoft/immersive-reader-sdk) 및 [몰입형 판독기 SDK 참조](./reference.md) 살펴보기
* [GitHub](https://github.com/microsoft/immersive-reader-sdk/samples/advanced-csharp)에서 코드 샘플 보기