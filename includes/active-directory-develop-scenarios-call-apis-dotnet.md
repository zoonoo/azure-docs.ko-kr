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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76308925"
---
### <a name="authenticationresult-properties-in-msalnet"></a>인증MSAL.NET결과 속성

토큰을 획득하는 메서드가 반환됩니다. `AuthenticationResult` 비동기 메서드의 `Task<AuthenticationResult>` 경우 반환합니다.

MSAL.NET `AuthenticationResult` 다음을 노출합니다.

- `AccessToken`웹 API가 리소스에 액세스할 수 있도록 합니다. 이 매개 변수는 문자열, 일반적으로 Base-64 인코딩 된 JWT입니다. 클라이언트는 액세스 토큰 내부를 들여다보지 않아야 합니다. 형식이 안정적으로 유지될 수 있다고 보장할 수는 없으며 리소스에 대해 암호화할 수 있습니다. 클라이언트에서 액세스 토큰 콘텐츠에 종속된 코드를 작성하는 것은 오류 및 클라이언트 논리 중단의 가장 큰 원인 중 하나입니다. 자세한 내용은 [액세스 토큰 을](../articles/active-directory/develop/access-tokens.md)참조하십시오.
- `IdToken`사용자에 대한 것입니다. 이 매개 변수는 인코딩된 JWT입니다. 자세한 내용은 [ID 토큰 을](../articles/active-directory/develop/id-tokens.md)참조하십시오.
- `ExpiresOn`은 토큰이 만료되는 날짜와 시간을 알려줍니다.
- `TenantId`에는 사용자가 발견된 테넌트가 포함됩니다. Azure Active Directory(Azure AD) B2B 시나리오의 게스트 사용자의 경우 테넌트 ID는 고유한 테넌트가 아닌 게스트 테넌트입니다.
토큰이 사용자에 대해 배달되면 `AuthenticationResult` 이 사용자에 대한 정보도 포함됩니다. 응용 프로그램에 대한 사용자 없이 토큰을 요청하는 기밀 클라이언트 흐름의 경우 이 사용자 정보는 null입니다.
- `Scopes` 토큰이 발행된 것입니다.
- 사용자에 대한 고유한 ID.

### <a name="iaccount"></a>아이 계정

MSAL.NET 인터페이스를 통해 계정의 `IAccount` 개념을 정의합니다. 이 주요 변경 사항은 올바른 의미 체계를 제공합니다. 동일한 사용자는 다른 Azure AD 디렉터리에서 여러 계정을 가질 수 있습니다. 또한 홈 계정 정보가 제공되므로 게스트 시나리오의 경우 더 나은 정보를 제공하는 MSAL.NET.
다음 다이어그램은 `IAccount` 인터페이스의 구조를 보여 줍니다.

![IAccount 인터페이스 구조](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

클래스는 `AccountId` 다음 표에 표시된 속성을 가진 특정 테넌트의 계정을 식별합니다.

| 속성 | 설명 |
|----------|-------------|
| `TenantId` | 계정이 있는 테넌트의 ID인 GUID에 대한 문자열 표현입니다. |
| `ObjectId` | 테넌트의 계정을 소유한 사용자의 ID인 GUID에 대한 문자열 표현입니다. |
| `Identifier` | 계정의 고유 식별자입니다. `Identifier`은 쉼표로 `ObjectId` 연결되고 `TenantId` 구분되는 것입니다. 기본 64가 인코딩되지 않습니다. |

인터페이스는 `IAccount` 단일 계정에 대한 정보를 나타냅니다. 동일한 사용자가 다른 테넌션에 있을 수 있으며, 이는 사용자가 여러 계정을 가질 수 있음을 의미합니다. 해당 멤버는 다음 표에 나와 있습니다.

| 속성 | 설명 |
|----------|-------------|
| `Username` | 예를 들어 UPN(UserPrincipalName) 형식으로 표시 가능한 값을 포함하는 john.doe@contoso.com문자열입니다. 이 문자열은 null이 아닐 수 있는 HomeAccountId 및 HomeAccountId.Identifier와 달리 null일 수 있습니다. 이 속성은 `DisplayableId` 이전 `IUser` 버전의 MSAL.NET 속성을 대체합니다. |
| `Environment` | 이 계정에 대한 ID 공급자가 포함된 문자열(예: `login.microsoftonline.com`) . 이 속성은 `IdentityProvider` 클라우드 `IUser`환경 외에 `IdentityProvider` 테넌트에 대한 정보도 있는 경우를 제외하고 의 속성을 대체합니다. 여기서 값은 호스트일 뿐입니다. |
| `HomeAccountId` | 사용자의 홈 계정의 계정 ID입니다. 이 속성은 Azure AD 테넌자 전체에서 사용자를 고유하게 식별합니다. |

### <a name="use-the-token-to-call-a-protected-api"></a>토큰을 사용하여 보호된 API를 호출합니다.

에서 `AuthenticationResult` MSAL에 `result`의해 반환 된 후 보호 된 웹 API에 액세스 하기 위해 호출 하기 전에 HTTP 권한 부여 헤더에 추가 합니다.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```