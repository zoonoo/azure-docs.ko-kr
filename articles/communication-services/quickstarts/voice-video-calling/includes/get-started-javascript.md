---
title: 빠른 시작 - Azure Communication Services를 사용하여 웹앱에 VOIP 통화 추가
description: 이 자습서에서는 JavaScript용 Azure Communication Services 통화 클라이언트 라이브러리를 사용하는 방법을 알아봅니다.
author: ddematheu
ms.author: nimag
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b66ee8117e5326a8ed8c1a1ad973fb13e942e0c7
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761982"
---
이 빠른 시작에서는 JavaScript용 Azure Communication Services 통화 클라이언트 라이브러리를 사용하여 통화를 시작하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장)
- 활성 Communication Services 리소스 [Communication Services 리소스를 만듭니다](../../create-communication-resource.md).
- 호출 클라이언트를 인스턴스화하기 위한 사용자 액세스 토큰입니다. [사용자 액세스 토큰 만들기 및 관리](../../access-tokens.md) 방법에 대해 알아봅니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir calling-quickstart && cd calling-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 통화 클라이언트 라이브러리를 설치합니다.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

이 빠른 시작에서는 webpack을 사용하여 애플리케이션 자산을 번들로 묶습니다. 다음 명령을 실행하여 webpack, webpack-cli 및 webpack-dev-server npm 패키지를 설치하고 **package.json**에서 개발 종속성으로 나열합니다.

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

프로젝트의 루트 디렉터리에 **index.html** 파일을 만듭니다. 이 파일을 사용하여 사용자가 Azure Communications Bot에 대한 호출을 수행할 수 있도록 하는 기본 레이아웃을 구성합니다.

코드는 다음과 같습니다.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

이 빠른 시작에 대한 애플리케이션 로직을 포함하기 위해 **client.js**라는 프로젝트의 루트 디렉터리에 파일을 만듭니다. 다음 코드를 추가하여 통화 클라이언트를 가져오고, 비즈니스 논리를 연결할 수 있도록 DOM 요소에 대한 참조를 가져옵니다. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from '@azure/communication-common';

let call;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");

// quickstart code goes here
```

## <a name="object-model"></a>개체 모델

Azure Communication Services 통화 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

| 이름                             | 설명                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient는 통화 클라이언트 라이브러리의 주 진입점입니다.                                                                       |
| CallAgent                        | CallAgent는 통화를 시작하고 관리하는 데 사용됩니다.                                                                                            |
| AzureCommunicationUserCredential | AzureCommunicationUserCredential 클래스는 CallAgent를 인스턴스화하는 데 사용되는 CommunicationUserCredential 인터페이스를 구현합니다. |


## <a name="authenticate-the-client"></a>클라이언트 인증

`<USER_ACCESS_TOKEN>`을 리소스에 대한 유효한 사용자 액세스 토큰으로 바꾸어야 합니다. 사용할 수 있는 토큰이 아직 없는 경우 [사용자 액세스 토큰](../../access-tokens.md) 설명서를 참조하세요. `CallClient`를 사용하여 호출을 수행하고 받을 수 있도록 하는 `CommunicationUserCredential`을 사용하여 `CallAgent` 인스턴스를 초기화합니다. **client.js**에 다음 코드를 추가합니다.

```javascript
const callClient = new CallClient();
const tokenCredential = new AzureCommunicationUserCredential("<USER ACCESS TOKEN>");
let callAgent;

callAgent = await callClient.createCallAgent(tokenCredential);
callButton.disabled = false;
```

## <a name="start-a-call"></a>호출 시작

`callButton`을 클릭하면 호출을 시작하는 이벤트 처리기를 추가합니다.

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.call(
        [{ communicationUserId: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>호출 종료

`hangUpButton`을 클릭하면 현재 호출을 종료하는 이벤트 수신기를 추가합니다.

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

`forEveryone` 속성은 모든 호출 참가자에 대한 호출을 종료합니다.

## <a name="run-the-code"></a>코드 실행

`webpack-dev-server`를 사용하여 앱을 빌드하고 실행합니다. 다음 명령을 실행하여 로컬 웹 서버에 애플리케이션 호스트를 번들로 묶습니다.

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

브라우저를 열고 http://localhost:8080/로 이동합니다. 다음이 표시되어야 합니다.

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="완성된 JavaScript 애플리케이션의 스크린샷":::

텍스트 필드에서 사용자 ID를 제공하고 **호출 시작** 단추를 클릭하여 아웃바운드 VOIP 호출을 수행할 수 있습니다. `8:echo123`을 호출하면 에코 봇과 연결되며, 오디오 디바이스를 시작하고 작동하는지 확인하는 데 유용합니다.
