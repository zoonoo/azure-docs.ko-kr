---
title: 포함 파일
description: 포함 파일
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "76308925"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET의 AuthenticationResult 속성

토큰을 획득 하는 메서드는를 반환 `AuthenticationResult` 합니다. 비동기 메서드의 경우을 `Task<AuthenticationResult>` 반환 합니다.

MSAL.NET에서 `AuthenticationResult` 다음을 노출 합니다.

- `AccessToken` 웹 API가 리소스에 액세스 하는 데 사용 됩니다. 이 매개 변수는 일반적으로 Base-64로 인코딩된 JWT의 문자열입니다. 클라이언트는 액세스 토큰 내에서 절대 찾지 말아야 합니다. 형식이 안정적으로 유지 되 고 리소스에 대해 암호화 될 수 있습니다. 클라이언트의 액세스 토큰 내용에 따라 달라 지는 코드를 작성 하는 것은 오류 및 클라이언트 논리 중단의 가장 큰 원인 중 하나입니다. 자세한 내용은 [액세스 토큰](../articles/active-directory/develop/access-tokens.md)을 참조 하세요.
- `IdToken` 사용자의 경우입니다. 이 매개 변수는 인코딩된 JWT입니다. 자세한 내용은 [ID 토큰](../articles/active-directory/develop/id-tokens.md)을 참조 하세요.
- `ExpiresOn` 토큰이 만료 되는 날짜와 시간을 알려 줍니다.
- `TenantId` 사용자가 있는 테 넌 트를 포함 합니다. Azure AD (Azure Active Directory) B2B 시나리오에서 게스트 사용자의 경우 테 넌 트 ID는 고유 테 넌 트가 아닌 게스트 테 넌 트입니다.
토큰은 사용자에 대해 전달 되는 경우에 `AuthenticationResult` 도이 사용자에 대 한 정보를 포함 합니다. 응용 프로그램에 대 한 사용자 없이 토큰이 요청 된 기밀 클라이언트 흐름의 경우이 사용자 정보는 null입니다.
- `Scopes`토큰이 발급 된입니다.
- 사용자에 대한 고유한 ID.

### <a name="iaccount"></a>IAccount

MSAL.NET는 인터페이스를 통해 계정의 개념을 정의 합니다 `IAccount` . 이러한 주요 변경 내용으로 올바른 의미 체계를 제공 합니다. 동일한 사용자는 서로 다른 Azure AD 디렉터리에 여러 계정을 가질 수 있습니다. 또한 MSAL.NET은 홈 계정 정보를 제공 하기 때문에 게스트 시나리오의 경우 더 나은 정보를 제공 합니다.
다음 다이어그램에서는 인터페이스의 구조를 보여 줍니다 `IAccount` .

![IAccount 인터페이스 구조](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId`클래스는 다음 표에 나와 있는 속성을 사용 하 여 특정 테 넌 트의 계정을 식별 합니다.

| 속성 | 설명 |
|----------|-------------|
| `TenantId` | 계정이 있는 테 넌 트의 ID 인 GUID에 대 한 문자열 표현입니다. |
| `ObjectId` | 테 넌 트의 계정을 소유 하는 사용자의 ID 인 GUID에 대 한 문자열 표현입니다. |
| `Identifier` | 계정에 대 한 고유 식별자입니다. `Identifier` 는 쉼표로 구분 된 및의 연결입니다 `ObjectId` `TenantId` . 기본 64 인코딩되지 않습니다. |

`IAccount`인터페이스는 단일 계정에 대 한 정보를 나타냅니다. 동일한 사용자가 다른 테 넌 트에 있을 수 있습니다. 즉, 사용자는 여러 계정을 가질 수 있습니다. 해당 멤버는 다음 표에 나와 있습니다.

| 속성 | 설명 |
|----------|-------------|
| `Username` | UserPrincipalName (UPN) 형식의 표시할 값 (예:)을 포함 하는 문자열입니다 john.doe@contoso.com . HomeAccountId 및 HomeAccountId와 달리이 문자열은 null 일 수 없습니다. 이 속성은 `DisplayableId` `IUser` 이전 버전의 MSAL.NET에 있는의 속성을 대체 합니다. |
| `Environment` | 이 계정에 대 한 id 공급자를 포함 하는 문자열입니다 (예:) `login.microsoftonline.com` . 이 속성은 `IdentityProvider` `IUser` `IdentityProvider` 클라우드 환경 외에도 테 넌 트에 대 한 정보도 포함 하는을 제외 하 고의 속성을 대체 합니다. 여기에서 값은 호스트만 해당 합니다. |
| `HomeAccountId` | 사용자에 대 한 홈 계정의 계정 ID입니다. 이 속성은 Azure AD 테 넌 트 간에 사용자를 고유 하 게 식별 합니다. |

### <a name="use-the-token-to-call-a-protected-api"></a>토큰을 사용 하 여 보호 된 API 호출

`AuthenticationResult`가 MSAL에서 반환 된 후에는 `result` 보호 된 web API에 액세스 하기 위해 호출 하기 전에 HTTP 권한 부여 헤더에 추가 합니다.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```