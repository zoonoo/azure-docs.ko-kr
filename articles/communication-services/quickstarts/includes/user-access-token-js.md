---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 77b1e9ab245f668ab81741451a5e032f37bc3625
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945757"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir user-tokens-quickstart && cd user-tokens-quickstart
```

`npm init -y`를 실행하여 기본 설정으로 **package.json** 파일을 만듭니다.

```console
npm init -y
```

### <a name="install-the-package"></a>패키지 설치

`npm install` 명령을 사용하여 JavaScript용 Azure Communication Services 관리 클라이언트 라이브러리를 설치합니다.

```console

npm install @azure/communication-administration --save

```

`--save` 옵션은 라이브러리를 **package.json** 파일의 종속성으로 나열합니다.

## <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 새 텍스트 파일 열기
1. `require` 호출을 추가하여 `CommunicationIdentityClient`를 로드합니다.
1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

시작하려면 다음 코드를 사용합니다.

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');

const main = async () => {
  console.log("Azure Communication Services - User Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 새 파일을 *user-tokens-quickstart* 디렉터리에 **issue-token.js**로 저장합니다.

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 인스턴스화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`main` 메서드에 다음 코드를 추가합니다.

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the user token client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-a-user"></a>사용자 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션의 사용자와 Communication Services에서 생성된 ID 간의 매핑을 유지 관리해야 합니다(예: 애플리케이션 서버의 데이터베이스에 저장).

```javascript
let userResponse = await identityClient.createUser();
console.log(`\nCreated a user with ID: ${userResponse.communicationUserId}`);
```

## <a name="issue-user-access-tokens"></a>사용자 액세스 토큰 발급

`issueToken` 메서드를 사용하여 Communication Services 사용자에 대한 액세스 토큰을 발급합니다. 선택적 `user` 매개 변수를 제공하지 않으면 새 사용자가 만들어지고 토큰과 함께 반환됩니다.

```javascript
// Issue an access token with the "voip" scope for a new user
let tokenResponse = await identityClient.issueToken(userResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued a token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

사용자 액세스 토큰은 사용자에게 서비스 중단이 발생하지 않도록 하기 위해 다시 발급해야 하는 단기 자격 증명입니다. `expiresOn` 응답 속성은 토큰의 수명을 나타냅니다.

## <a name="revoke-user-access-tokens"></a>사용자 액세스 토큰 취소

예를 들어, 사용자가 서비스 인증에 사용하는 암호를 변경하는 경우와 같이 사용자 액세스 토큰을 명시적으로 취소해야 하는 경우도 있습니다. `revokeTokens` 메서드를 사용하여 사용자의 모든 액세스 토큰을 무효화합니다.

```javascript  
await identityClient.revokeTokens(userResponse);
console.log(`\nSuccessfully revoked all tokens for user with Id: ${userResponse.communicationUserId}`);
```

## <a name="delete-a-user"></a>사용자 삭제

사용자를 삭제하면 모든 활성 토큰이 취소되고 ID에 대한 후속 토큰을 발급할 수 없게 됩니다. 또한 사용자와 연결된 모든 지속형 콘텐츠를 제거합니다.

```javascript
await identityClient.deleteUser(userResponse);
console.log(`\nDeleted the user with Id: ${userResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-token.js* 파일이 포함된 디렉터리로 이동한 후, 다음 `node` 명령을 실행하여 앱을 실행합니다.

```console
node ./issue-token.js
```
