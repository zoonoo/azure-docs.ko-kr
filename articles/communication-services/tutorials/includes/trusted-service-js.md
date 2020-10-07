---
title: 신뢰할 수 있는 서비스 JavaScript
description: Communication Services에 대해 신뢰할 수 있는 서비스를 만드는 JavaScript 버전입니다.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d4ef8baa123f805d380b14fa24abff65903cb41d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945256"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나인 [Visual Studio Code](https://code.visualstudio.com/).
- [Node.js](https://nodejs.org/), 활성 LTS 및 유지 관리 LTS 버전(10.14.1 권장). `node --version` 명령을 사용하여 버전을 확인합니다. 
- Visual Studio Code용 [Azure Functions 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 
- 활성 Communication Services 리소스 및 연결 문자열. [Communication Services 리소스를 만듭니다](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>개요

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="신뢰할 수 있는 서비스 아키텍처에 대한 다이어그램":::

이 자습서에서는 신뢰할 수 있는 토큰 프로비저닝 서비스로 제공되는 Azure 함수를 만듭니다. 이 자습서를 사용하여 사용자 고유의 토큰 프로비저닝 서비스를 부트스트랩할 수 있습니다.

이 서비스는 Azure Communication Services에 대한 사용자 인증을 수행합니다. Communication Services 애플리케이션 사용자가 채팅 스레드 및 VoIP 통화에 참여하려면 `Access Token`이 필요합니다. Azure 함수는 사용자와 Communication Services 간에 신뢰할 수 있는 중개자로 작동합니다. 이렇게 하면 사용자에게 리소스 연결 문자열을 공개하지 않고도 액세스 토큰을 프로비저닝할 수 있습니다.

자세한 내용은 [클라이언트-서버 아키텍처](../../concepts/client-and-server-architecture.md) 및 [인증 및 권한 부여](../../concepts/authentication.md) 개념 설명서를 참조하세요.

## <a name="setting-up"></a>설치

### <a name="azure-functions-set-up"></a>Azure Functions 설정

먼저 Azure 함수에 대한 기본 구조를 설정해 보겠습니다. 설정에 대한 단계별 지침은 [Visual Studio Code를 사용하여 함수 만들기](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)에서 확인할 수 있습니다.

Azure 함수에 필요한 구성은 다음과 같습니다.

- 언어: JavaScript
- 템플릿: HTTP 트리거
- 권한 부여 수준: 익명(다른 권한 부여 모델을 원하는 경우 나중에 전환할 수 있음)
- 함수 이름: 사용자 정의

위의 구성과 함께 [Azure Functions 지침](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript)을 수행한 후에는 함수 자체가 포함된 `index.js` 파일을 사용하는 프로젝트가 Azure 함수에 대한 Visual Studio Code에 있습니다. 이 파일의 내부 코드는 다음과 같습니다.

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

이제 Azure Communication Services 라이브러리 설치를 진행합니다.

### <a name="install-communication-services-libraries"></a>Communication Services 라이브러리 설치

`Administration` 라이브러리를 사용하여 `User Access Tokens`를 생성합니다.

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 관리 클라이언트 라이브러리를 설치합니다.

```console

npm install @azure/communication-administration --save

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

`index.js` 파일의 위쪽에서 `CommunicationIdentityClient`에 대한 인터페이스를 가져옵니다.

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>액세스 토큰 생성

Azure 함수에서 `User Access Tokens`를 생성할 수 있도록 하려면 먼저 Communication Services 리소스에 대한 연결 문자열을 사용해야 합니다.

연결 문자열을 검색하는 방법에 대한 자세한 내용은 [리소스 프로비저닝 빠른 시작](../../quickstarts/create-communication-resource.md)을 참조하세요.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

다음으로, `User Access Tokens`를 생성하도록 원래 함수를 수정합니다. 

`User Access Tokens`는 `createUser` 메서드에서 사용자를 만들어 생성됩니다. 사용자가 만들어지면 `issueToken` 메서드를 사용하여 Azure 함수에서 반환하는 해당 사용자에 대한 토큰을 생성할 수 있습니다.

이 예제에서는 토큰 범위를 `voip`로 구성합니다. 애플리케이션에 다른 범위가 필요할 수 있습니다. [범위](../../quickstarts/access-tokens.md)에 대해 자세히 알아보세요.

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    const response = {
        "User" : userToken.user,
        "Token": userToken.token,
        "ExpiresOn": userToken.expiresOn
    }

    context.res = {
        body: response
    };
}
```

기존 Communication Services `CommunicationUser`의 경우 만들기 단계를 건너뛰고 액세스 토큰만 생성하면 됩니다. 자세한 내용은 [사용자 액세스 토큰 만들기 빠른 시작](../../quickstarts/access-tokens.md)을 참조하세요.

## <a name="test-the-azure-function"></a>Azure 함수 테스트

`F5` 키를 사용하여 Azure 함수를 로컬로 실행합니다. 이렇게 하면 Azure 함수가 로컬로 초기화되고 `http://localhost:7071/api/FUNCTION_NAME`을 통해 액세스할 수 있습니다. [로컬 실행](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-locally)에 대한 추가 설명서를 확인하세요.

브라우저에서 URL을 엽니다. 그러면 통신 사용자 ID, 토큰 및 토큰 만료 날짜가 포함된 응답 본문이 표시됩니다.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="신뢰할 수 있는 서비스 아키텍처에 대한 다이어그램":::

## <a name="deploy-the-function-to-azure"></a>Azure에 Function 배포

Azure 함수를 배포하려면 [단계별 지침](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#sign-in-to-azure)을 따를 수 있습니다.

일반적으로 다음을 수행해야 합니다.
1. Visual Studio에서 Azure에 로그인합니다.
2. 프로젝트를 Azure 계정에 게시합니다. 여기서는 기존 구독을 선택해야 합니다.
3. Visual Studio 마법사를 사용하여 새 Azure 함수 리소스를 만들거나 기존 리소스를 사용합니다. 새 리소스의 경우 원하는 지역, 런타임 및 고유 식별자로 구성해야 합니다.
4. 배포가 완료될 때까지 기다림
5. 함수 실행 🎉

## <a name="run-azure-function"></a>Azure 함수 실행

`http://<function-appn-ame>.azurewebsites.net/api/<function-name>` URL을 사용하여 Azure 함수 실행

URL은 Visual Studio Code에서 마우스 오른쪽 단추로 함수를 클릭하고 Function URL을 복사하여 확인할 수 있습니다.

자세한 내용은 [Azure 함수 실행](https://docs.microsoft.com/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-javascript#run-the-function-in-azure)을 참조하세요.
