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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76308925"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET의 AuthenticationResult 속성

토큰을 획득하는 메서드는 `AuthenticationResult`를 반환합니다. 비동기 메서드의 경우 `Task<AuthenticationResult>`가 반환됩니다.

MSAL.NET에서 `AuthenticationResult`는 다음을 노출합니다.

- 웹 API가 리소스에 액세스하는 데 사용되는 `AccessToken`. 이 매개 변수는 일반적으로 Base-64로 인코딩된 JWT인 문자열입니다. 클라이언트는 액세스 토큰 내부를 보지 않아야 합니다. 형식은 안정적 상태 유지가 보장되지 않으며 리소스에 대해 암호화할 수 있습니다. 클라이언트의 액세스 토큰 콘텐츠에 따른 코드 작성은 사용자는 오류 및 클라이언트 논리 나누기의 가장 큰 원인 중 하나입니다. 자세한 내용은 [액세스 토큰](../articles/active-directory/develop/access-tokens.md)을 참조하세요.
- 사용자에 대한 `IdToken`. 이 매개 변수는 인코딩된 JWT입니다. 자세한 내용은 [ID 토큰](../articles/active-directory/develop/id-tokens.md)을 참조하세요.
- `ExpiresOn`은 토큰이 만료되는 날짜와 시간을 알려줍니다.
- `TenantId`는 사용자가 발견된 테넌트를 포함합니다. Azure AD(Azure Active Directory) B2B 시나리오의 게스트 사용자의 경우 테넌트 ID는 고유 테넌트가 아니라 게스트 테넌트입니다.
사용자에 대해 토큰이 전달될 때 `AuthenticationResult`에는 이 사용자에 대한 정보도 포함됩니다. 애플리케이션에 대한 토큰이 사용자의 개입이 없이 요청되는 기밀 클라이언트 흐름의 경우 이 사용자 정보는 null입니다.
- 토큰이 발급된 `Scopes`.
- 사용자에 대한 고유한 ID.

### <a name="iaccount"></a>IAccount

MSAL.NET은 `IAccount` 인터페이스를 통해 계정의 개념을 정의합니다. 이러한 호환성이 손상되는 변경은 올바른 의미 체계를 제공합니다. 동일한 사용자가 서로 다른 Azure AD 디렉터리에 여러 계정을 가질 수 있습니다. 또한 MSAL.NET은 홈 계정 정보가 제공되므로 게스트 시나리오의 경우 더 나은 정보를 제공합니다.
다음 다이어그램에서는 `IAccount` 인터페이스의 구조를 보여줍니다.

![IAccount 인터페이스 구조](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 클래스는 다음 표에 나와 있는 속성을 사용하여 특정 테넌트의 계정을 식별합니다.

| 속성 | Description |
|----------|-------------|
| `TenantId` | 계정이 있는 테넌트의 ID인 GUID에 대한 문자열 표현입니다. |
| `ObjectId` | 테넌트에 계정을 소유하고 있는 사용자의 ID인 GUID에 대한 문자열 표현입니다. |
| `Identifier` | 계정의 고유 식별자입니다. `Identifier`는 `ObjectId`와 `TenantId`를 쉼표로 구분하여 연결한 것입니다. Base 64로 인코딩되지 않습니다. |

`IAccount` 인터페이스는 단일 계정에 대한 정보를 나타냅니다. 동일한 사용자가 다른 테넌트에 있을 수 있습니다. 즉, 사용자가 여러 계정을 가질 수 있습니다. 다음 표에 해당 멤버가 나와 있습니다.

| 속성 | Description |
|----------|-------------|
| `Username` | UPN(UserPrincipalName) 형식의 표시 가능한 값을 포함하는 문자열(예: john.doe@contoso.com). 이 문자열은 null이 아닌 HomeAccountId 및 HomeAccountId.Identifier와 달리 null일 수 있습니다. 이 속성은 이전 버전의 MSAL.NET에서 `IUser`의 `DisplayableId` 속성을 대체합니다. |
| `Environment` | 이 계정의 ID 공급자를 포함하는 문자열(예: `login.microsoftonline.com`). 이 속성은 `IUser`의 `IdentityProvider` 속성을 대체합니다. 단, `IdentityProvider`는 클라우드 환경 외에 테넌트에 대한 정보도 가지고 있습니다. 여기서 값은 호스트일 뿐입니다. |
| `HomeAccountId` | 사용자에 대한 홈 계정의 계정 ID. 이 속성은 Azure AD 테넌트에서 사용자를 고유하게 식별합니다. |

### <a name="use-the-token-to-call-a-protected-api"></a>토큰을 사용하여 보호된 API 호출

MSAL이 `result`에 `AuthenticationResult`를 반환한 후 HTTP 인증 헤더에 이를 추가한 다음, 보호되는 웹 API에 액세스하기 위한 호출을 합니다.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```