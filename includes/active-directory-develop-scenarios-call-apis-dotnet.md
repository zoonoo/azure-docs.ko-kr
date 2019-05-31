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
ms.openlocfilehash: 0196d39f5b131bc54e00412beb7fdf10b7352336
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121868"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET의 AuthenticationResult 속성

토큰을 획득 하는 메서드 반환를 `AuthenticationResult` (또는 비동기 메서드에 `Task<AuthenticationResult>`합니다.

MSAL.NET을에서 `AuthenticationResult` 노출 합니다.

- `AccessToken` 웹 api를 리소스에 액세스 합니다. 이 매개 변수는 문자열로, 일반적으로 base64 인코딩된 JWT 있지만 클라이언트 액세스 토큰 안에 표시 되지 됩니다. 형식은 안정적 상태 유지가 보장되지 않으며 리소스에 대해 암호화할 수 있습니다. 클라이언트의 액세스 토큰 콘텐츠에 따라 코드를 작성하는 사용자는 오류 및 클라이언트 논리 나누기의 가장 큰 원인 중 하나입니다. 참고 [액세스 토큰](../articles/active-directory/develop/access-tokens.md)
- `IdToken` 사용자 (이 매개 변수는 인코딩된 JWT는 하는 데 사용). 참조 [ID 토큰](../articles/active-directory/develop/id-tokens.md)
- `ExpiresOn` 토큰이 만료 되는 경우 날짜/시간을 알려 줍니다.
- `TenantId` 사용자를 찾을 수 있는 테 넌 트를 포함 합니다. 게스트 사용자 (Azure AD B2B 시나리오)에 대 한 테 넌 트 ID가 게스트 테 넌 트를 고유한 테 넌 트 하지 않습니다.
토큰은 사용자에 대해 전달 될 때 `AuthenticationResult` 도이 사용자에 대 한 정보를 포함 합니다. 기밀 클라이언트 흐름 (응용 프로그램)에 대 한 사용자를 사용 하 여 토큰 요청 된 위치에 대 한이 사용자 정보는 null입니다.
- `Scopes` 토큰을 발행 하는 것에 대 한 합니다.
- 사용자에 대 한 고유 Id입니다.

### <a name="iaccount"></a>IAccount

계정의 개념을 정의 하는 MSAL.NET (통해를 `IAccount` 인터페이스). 이 주요 변경은 올바른 의미 체계, 즉 동일한 사용자가 여러 Azure AD 디렉터리에 여러 계정을 가질 수 있다는 사실을 제공합니다. 또한 MSAL.NET 홈 계정 정보를 제공 하는 대로 게스트 시나리오의 경우 더 나은 정보를 제공 합니다.
다음 다이어그램은 구조를 보여 줍니다.는 `IAccount` 인터페이스:

![Image](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 특정 테 넌 트의 계정 클래스를 식별 합니다. 해당 속성은 다음과 같습니다.

| 자산 | 설명 |
|----------|-------------|
| `TenantId` | 계정이 있는 테 넌 트의 ID 인 GUID에 대 한 문자열 표현입니다. |
| `ObjectId` | 테 넌 트에서 계정을 소유한 사용자의 id는 GUID에 대 한 문자열 표현입니다. |
| `Identifier` | 계정에 대 한 고유 식별자입니다. `Identifier` 가 연결 된 `ObjectId` 고 `TenantId` 는 base64로 인코딩되지 없습니다를 쉼표로 구분 합니다. |

`IAccount` 인터페이스는 단일 계정에 대 한 정보를 나타냅니다. 동일한 사용자는 다른 테 넌 트에 있을 수, 즉, 사용자 계정을 여러 개 있습니다. 해당 멤버는:

| 자산 | 설명 |
|----------|-------------|
| `Username` | 예를 들어, UserPrincipalName (UPN) 형식으로 표시할 수 있는 값을 포함 하는 문자열 john.doe@contoso.com합니다. 이 문자열 HomeAccountId.Identifier 고 HomeAccountId null 없습니다 반면 null 일 수 있습니다. 이 속성은 대체 합니다 `DisplayableId` 속성의 `IUser` MSAL.NET의 이전 버전에서입니다. |
| `Environment` | 예를 들어,이 계정에 대 한 id 공급자를 포함 하는 문자열 `login.microsoftonline.com`합니다. 이 속성은 대체 합니다 `IdentityProvider` 속성을 `IUser`점을 제외 하 고 `IdentityProvider` 여기서 값은만 호스트 하는 반면 (외에 클라우드 환경) 테 넌 트에 대 한 정보는 또한 합니다. |
| `HomeAccountId` | 사용자에 대 한 홈 계정의 계정 Id입니다. 이 속성 Azure AD 테 넌 트에서 사용자를 고유 하 게 식별합니다. |

### <a name="using-the-token-to-call-a-protected-api"></a>보호 된 API를 호출 하는 토큰을 사용 하 여

한 번 합니다 `AuthenticationResult` MSAL에 의해 반환 된 (에서 `result`), 보호 된 웹 API에 액세스를 호출 하기 전에 HTTP 인증 헤더에 추가 해야 합니다.

```CSharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```