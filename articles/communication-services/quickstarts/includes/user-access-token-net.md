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
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779271"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- 운영 체제에 대한 최신 버전의 [.NET Core 클라이언트 라이브러리](https://dotnet.microsoft.com/download/dotnet-core)
- 활성 Communication Services 리소스 및 연결 문자열 [Communication Services 리소스를 만듭니다](../create-communication-resource.md).

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `UserAccessTokensQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs**라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o UserAccessTokensQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication Services 관리 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 텍스트 편집기에서 **Program.cs** 파일 열기
1. `Azure.Communication.Administration` 네임스페이스를 포함하는 `using` 지시문 추가
1. 비동기 코드를 지원하도록 `Main` 메서드 선언 업데이트

시작하려면 다음 코드를 사용합니다.

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>클라이언트 인증

연결 문자열로 `CommunicationIdentityClient`를 초기화합니다. 아래 코드는 `COMMUNICATION_SERVICES_CONNECTION_STRING`이라는 환경 변수에서 리소스에 대한 연결 문자열을 검색합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

`Main` 메서드에 다음 코드를 추가합니다.

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>사용자 만들기

Azure Communication Services는 경량 ID 디렉터리를 유지 관리합니다. `createUser` 메서드를 사용하여 고유한 `Id`가 있는 디렉터리에 새 항목을 만듭니다. 애플리케이션의 사용자와 Communication Services에서 생성된 ID 간의 매핑을 유지 관리해야 합니다(예: 애플리케이션 서버의 데이터베이스에 저장).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>사용자 액세스 토큰 발급

`issueToken` 메서드를 사용하여 Communication Services 사용자에 대한 액세스 토큰을 발급합니다. 선택적 `user` 매개 변수를 제공하지 않으면 새 사용자가 만들어지고 토큰과 함께 반환됩니다.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

사용자 액세스 토큰은 사용자에게 서비스 중단이 발생하지 않도록 하기 위해 다시 발급해야 하는 단기 자격 증명입니다. `expiresOn` 응답 속성은 토큰의 수명을 나타냅니다.

## <a name="revoke-user-access-tokens"></a>사용자 액세스 토큰 취소

예를 들어, 사용자가 서비스 인증에 사용하는 암호를 변경하는 경우와 같이 사용자 액세스 토큰을 명시적으로 취소해야 하는 경우도 있습니다. 이 기능은 Azure Communication Services 관리 클라이언트 라이브러리를 통해 사용할 수 있습니다.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>사용자 삭제

ID를 삭제하면 모든 활성 토큰이 취소되고 ID에 대한 후속 토큰을 발급할 수 없게 됩니다. 또한 사용자와 연결된 모든 지속형 콘텐츠를 제거합니다.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
