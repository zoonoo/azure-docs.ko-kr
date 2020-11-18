---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: af5af26a8970409b07eda6195b0853c3fa931b3f
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506256"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) 활성 LTS 및 유지 관리 LTS 버전(8.11.1 및 10.14.1 권장).
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 해당 디렉터리로 이동합니다.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
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
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered and error");
  console.log(error);
})
```

1. 새 파일을 *access-tokens-quickstart* 디렉터리에 **issue-access-token.js** 로 저장합니다.

## <a name="authenticate-the-client"></a>클라이언트 인증

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

## <a name="issue-access-tokens"></a>액세스 토큰 발급

`issueToken` 메서드를 사용하여 이미 존재하는 Communication Services ID에 대한 액세스 토큰을 발급합니다. 매개 변수 `scopes`는 이 액세스 토큰에 권한을 부여하는 기본 형식 세트를 정의합니다. [지원되는 작업 목록](../../concepts/authentication.md)을 참조하세요. 매개 변수 `communicationUser`의 새 인스턴스는 Azure Communication Service ID의 문자열 표현에 따라 구성될 수 있습니다.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

액세스 토큰은 다시 발급해야 하는 단기 자격 증명입니다. 이렇게 하지 않으면 애플리케이션의 사용자 환경이 중단될 수 있습니다. `expiresOn` 응답 속성은 액세스 토큰의 수명을 나타냅니다.


## <a name="refresh-access-tokens"></a>액세스 토큰 새로 고침

액세스 토큰을 새로 고치려면 `CommunicationUser` 개체를 사용하여 다시 발급합니다.

```javascript  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
identityResponse = new CommunicationUser(existingIdentity);
tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>액세스 토큰 취소

경우에 따라 액세스 토큰을 명시적으로 취소할 수 있습니다. 예를 들어 애플리케이션의 사용자가 서비스에 인증하는 데 사용하는 암호를 변경하는 경우입니다. 메서드 `revokeTokens`는 ID에 발급된 모든 활성 액세스 토큰을 무효화합니다.

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with Id: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>ID 삭제

ID를 삭제하면 모든 활성 액세스 토큰이 취소되고 ID에 대한 액세스 토큰을 발급할 수 없게 됩니다. 또한 ID와 연결된 모든 지속형 콘텐츠를 제거합니다.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with Id: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>코드 실행

콘솔 프롬프트에서 *issue-access-token.js* 파일이 포함된 디렉터리로 이동한 후, 다음 `node` 명령을 실행하여 앱을 실행합니다.

```console
node ./issue-access-token.js
```
