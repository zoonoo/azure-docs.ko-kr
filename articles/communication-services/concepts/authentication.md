---
title: Azure 통신 서비스에 인증
titleSuffix: An Azure Communication Services concept document
description: 응용 프로그램 또는 서비스가 통신 서비스에 인증할 수 있는 다양 한 방법에 대해 알아봅니다.
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 939c36cd62dab4362232aef0da8701b34a88c6ff
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202959"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure 통신 서비스에 인증

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

이 문서에서는 *액세스 키* 및 *사용자 액세스 토큰*을 사용 하 여 Azure Communication Services에 대 한 클라이언트 인증에 대 한 정보를 제공 합니다. 모든 클라이언트와 Azure 통신 서비스 간의 상호 작용을 인증 해야 합니다.

다음 표에서는 Azure Communication Services 클라이언트 라이브러리에서 지원 되는 인증 옵션을 설명 합니다.

| 클라이언트 라이브러리 | 액세스 키    | 사용자 액세스 토큰 |
| -------------- | ------------- | ------------------ |
| 관리 | 지원됨     | 지원되지 않음      |
| SMS            | 지원됨     | 지원되지 않음      |
| 채팅           | 지원되지 않음 | 지원됨          |
| 호출        | 지원되지 않음 | 지원됨          |

각 권한 부여 옵션에 대 한 간략 한 설명은 다음과 같습니다.

- SMS 및 관리 작업에 대 한 **액세스 키** 인증 액세스 키 인증은 신뢰할 수 있는 서비스 환경에서 실행 되는 응용 프로그램에 적합 합니다. 액세스 키를 사용 하 여 인증 하기 위해 클라이언트는 [HMAC (해시 기반 메시지 인증 코드)](https://en.wikipedia.org/wiki/HMAC) 를 생성 하 고 `Authorization` 각 HTTP 요청의 헤더 내에 포함 합니다. 자세한 내용은 [액세스 키를 사용 하 여 인증](#authenticate-with-an-access-key)을 참조 하세요.
- 채팅 및 호출에 대 한 **사용자 액세스 토큰** 인증입니다. 사용자 액세스 토큰을 통해 클라이언트 응용 프로그램에서 Azure Communication Services에 대해 직접 인증할 수 있습니다. 이러한 토큰은 사용자가 만든 서버 쪽 토큰 프로 비전 서비스에 생성 됩니다. 그런 다음 토큰을 사용 하 여 채팅을 초기화 하 고 클라이언트 라이브러리를 호출 하는 클라이언트 장치에 제공 됩니다. 자세한 내용은 [사용자 액세스 토큰을 사용 하 여 인증](#authenticate-with-a-user-access-token)을 참조 하세요.

## <a name="authenticate-with-an-access-key"></a>액세스 키를 사용 하 여 인증

액세스 키 인증은 공유 암호 키를 사용 하 여 SHA256 알고리즘을 사용 하 여 계산 된 각 HTTP 요청에 대해 HMAC를 생성 하 고 `Authorization` 스키마를 사용 하 여 헤더에 보냅니다 `HMAC-SHA256` .

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

액세스 키 인증을 사용 하는 Azure Communication Services 클라이언트 라이브러리는 리소스의 연결 문자열을 사용 하 여 초기화 해야 합니다. 클라이언트 라이브러리를 사용 하지 않는 경우 리소스의 액세스 키를 사용 하 여 프로그래밍 방식으로 HMACs을 생성할 수 있습니다. 연결 문자열에 대해 자세히 알아보려면 [리소스 프로 비전 빠른](../quickstarts/create-communication-resource.md)시작을 참조 하세요.

### <a name="sign-an-http-request"></a>HTTP 요청 서명

클라이언트 라이브러리를 사용 하 여 Azure Communication Services REST Api에 대 한 HTTP 요청을 수행 하지 않는 경우 각 HTTP 요청에 대해 프로그래밍 방식으로 HMACs를 만들어야 합니다. 다음 단계에서는 인증 헤더를 생성 하는 방법을 설명 합니다.

1. 헤더 또는 표준 HTTP 헤더에서 요청에 대 한 UTC (협정 세계시) 타임 스탬프를 지정 `x-ms-date` `Date` 합니다. 서비스는 재생 공격을 포함 하 여 특정 보안 공격 으로부터 보호 하기 위해이의 유효성을 검사 합니다.
1. SHA256 알고리즘을 사용 하 여 HTTP 요청 본문을 해시 한 다음 헤더를 통해 요청과 함께 전달 합니다 `x-ms-content-sha256` .
1. HTTP 동사 (예: `GET` 또는 `PUT` ), http 요청 경로, 및 http 헤더의 값을 `Date` `Host` `x-ms-content-sha256` 다음 형식으로 연결 하 여 서명할 문자열을 생성 합니다.
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. 이전 단계에서 만든 u t f-8로 인코딩된 문자열의 HMAC-256 서명을 생성 합니다. 그런 다음 결과를 b a s e 64로 인코딩합니다. 저장소 계정 키를 Base64로 디코딩하도 해야 합니다. 다음 형식을 사용 합니다 (의사 코드로 표시 됨).
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. 권한 부여 헤더를 다음과 같이 지정 합니다.
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    여기서 `<hmac-sha256-signature>` 는 이전 단계에서 HMAC를 계산 합니다.

## <a name="authenticate-with-a-user-access-token"></a>사용자 액세스 토큰을 사용 하 여 인증

사용자 액세스 토큰을 통해 클라이언트 응용 프로그램에서 Azure Communication Services에 대해 직접 인증할 수 있습니다. 이를 위해서는 응용 프로그램 사용자를 인증 하 고 관리 클라이언트 라이브러리를 사용 하 여 사용자 액세스 토큰을 발급 하는 신뢰할 수 있는 서비스를 설정 해야 합니다. 아키텍처 고려 사항에 대해 자세히 알아보려면 [클라이언트 및 서버 아키텍처](./client-and-server-architecture.md) 개념 설명서를 참조 하세요.

클래스에는 `CommunicationClientCredential` 클라이언트 라이브러리에 사용자 액세스 토큰 자격 증명을 제공 하 고 수명 주기를 관리 하는 논리가 포함 되어 있습니다.

### <a name="initialize-the-client-libraries"></a>클라이언트 라이브러리 초기화

사용자 액세스 토큰 인증이 필요한 Azure Communication Services 클라이언트 라이브러리를 초기화 하려면 먼저 클래스의 인스턴스 `CommunicationClientCredential` 를 만든 다음이를 사용 하 여 API 클라이언트를 초기화 합니다.

다음 코드 조각은 사용자 액세스 토큰을 사용 하 여 채팅 클라이언트 라이브러리를 초기화 하는 방법을 보여 줍니다.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>사용자 액세스 토큰 새로 고침

사용자 액세스 토큰은 사용자에 게 서비스 중단이 발생 하지 않도록 하기 위해 다시 발급 해야 하는 수명이 짧은 자격 증명입니다. `CommunicationUserCredential`생성자는 사용자 액세스 토큰이 만료 되기 전에 업데이트할 수 있도록 하는 새로 고침 콜백 함수를 허용 합니다. 이 콜백을 사용 하 여 신뢰할 수 있는 서비스에서 새 사용자 액세스 토큰을 가져와야 합니다.

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

`refreshProactively`옵션을 사용 하면 토큰 수명 주기를 관리 하는 방법을 결정할 수 있습니다. 기본적으로 토큰이 유효 하지 않으면 콜백은 API 요청을 차단 하 고 새로 고침을 시도 합니다. `refreshProactively`가로 설정 되 면 `true` 콜백이 예약 되 고 토큰이 만료 되기 전에 비동기적으로 실행 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 항목을 참조하세요.
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
