---
title: 포함 파일
description: 포함 파일
services: Communication Services
author: shahen
manager: anvalent
ms.service: Communication Services
ms.subservice: Communication Services
ms.date: 05/20/2021
ms.topic: include
ms.custom: include file
ms.author: shahen
ms.openlocfilehash: 118aa3bfcc35b0250b13ea3446e41e8f4b9f06d8
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026935"
---
## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir relay-token-quickstart && cd relay-token-quickstart
```
`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services ID 클라이언트 라이브러리 및 Network Traversal 라이브러리를 설치합니다.

```console
npm install @azure/communication-identity --save
npm install @azure/communication-network-traversal --save
```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 새 텍스트 파일 열기
2. Azure Communication Identity SDK 및 Network Traversal SDK를 사용하려면 `require` 지시문을 파일 맨 위에 추가합니다.
3. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

   시작하려면 다음 코드를 사용합니다.

   ```javascript
   const { CommunicationIdentityClient } = require("@azure/communication-identity");
   const { CommunicationRelayClient } = require("@azure/communication-network-traversal");;

   const main = async () => {
     console.log("Azure Communication Services - Relay Token Quickstart")
  
     // Quickstart code goes here
   };

   main().catch((error) => {
     console.log("Encountered and error");
     console.log(error);
   })
   ```

4. 새 파일을 *user-tokens-quickstart* 디렉터리에 **relay-token.js** 로 저장합니다.

### <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`main` 메서드에 다음 코드를 추가합니다.

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>ID 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션 사용자에게 매핑하여 수신된 ID를 저장합니다. 예를 들어 애플리케이션 서버의 데이터베이스에 저장합니다. ID는 나중에 액세스 토큰을 발급하는 데 필요합니다.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

### <a name="exchange-the-user-access-token-for-a-relay-token"></a>릴레이 토큰에 대한 사용자 액세스 토큰 교환

Azure Communication 토큰 서비스를 호출하여 TURN 서비스 토큰에 대한 사용자 액세스 토큰을 교환합니다.

```javascript
    const relayClient = new CommunicationRelayClient(connectionString);
    console.log("Getting relay configuration");

    const config = await relayClient.getRelayConfiguration(identityResponse);
    console.log("RelayConfig", config);
```

### <a name="use-the-token-on-the-client-as-an-ice-candidate"></a>클라이언트의 토큰을 ICE 후보로 사용

이제 클라이언트 브라우저에서 WebRTC를 사용하여 토큰을 deserialized 및 추가하고 ICE 후보를 추가할 수 있습니다.

```javascript  
var configuration = { iceServers: iceServers };
var peerConnection = new RTCPeerConnection(configuration);
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-token.js* 파일이 포함된 디렉터리로 이동한 후, 다음 `node` 명령을 실행하여 앱을 실행합니다.

```console
node ./relay-token.js
```
