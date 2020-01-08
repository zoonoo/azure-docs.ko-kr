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
ms.openlocfilehash: 4e01dbb0036761215a9a05c464b20ead340a2e3d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423738"
---
### <a name="authenticationresult-properties-in-msalnet"></a>MSAL.NET의 AuthenticationResult 속성

토큰을 획득 하는 메서드는 `AuthenticationResult` (또는 비동기 메서드의 경우 `Task<AuthenticationResult>`을 반환 합니다.

MSAL.NET에서 `AuthenticationResult`를 노출 합니다.

- 리소스에 액세스 하는 Web API에 대 한 `AccessToken`입니다. 이 매개 변수는 문자열입니다. 일반적으로 base64 인코딩 JWT 이지만 클라이언트는 액세스 토큰 내에서 볼 수 없습니다. 형식은 안정적 상태 유지가 보장되지 않으며 리소스에 대해 암호화할 수 있습니다. 클라이언트의 액세스 토큰 콘텐츠에 따라 코드를 작성하는 사용자는 오류 및 클라이언트 논리 나누기의 가장 큰 원인 중 하나입니다. 참고 항목 [액세스 토큰](../articles/active-directory/develop/access-tokens.md)
- 사용자의 `IdToken`입니다 (이 매개 변수는 인코딩된 JWT). [ID 토큰](../articles/active-directory/develop/id-tokens.md) 참조
- `ExpiresOn`은 토큰이 만료 되는 날짜/시간을 알려 줍니다.
- `TenantId`은 사용자가 있는 테 넌 트를 포함 합니다. 게스트 사용자 (Azure AD B2B 시나리오)의 경우 테 넌 트 ID는 고유 테 넌 트가 아니라 게스트 테 넌 트입니다.
토큰은 사용자에 게 전달 될 때이 사용자에 대 한 정보를 포함 `AuthenticationResult` 합니다. 응용 프로그램에 대해 사용자가 없는 토큰을 요청 하는 기밀 클라이언트 흐름의 경우이 사용자 정보는 null입니다.
- 토큰이 발급 된 `Scopes`입니다.
- 사용자의 고유 Id입니다.

### <a name="iaccount"></a>IAccount

MSAL.NET는 `IAccount` 인터페이스를 통해 계정의 개념을 정의 합니다. 이 주요 변경은 올바른 의미 체계, 즉 동일한 사용자가 여러 Azure AD 디렉터리에 여러 계정을 가질 수 있다는 사실을 제공합니다. 또한 MSAL.NET은 홈 계정 정보가 제공 됨에 따라 게스트 시나리오의 경우 더 나은 정보를 제공 합니다.
다음 다이어그램은 `IAccount` 인터페이스의 구조를 보여줍니다.

![이미지](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

`AccountId` 클래스는 특정 테 넌 트의 계정을 식별 합니다. 해당 속성은 다음과 같습니다.

| 속성 | Description |
|----------|-------------|
| `TenantId` | 계정이 있는 테 넌 트의 ID 인 GUID에 대 한 문자열 표현입니다. |
| `ObjectId` | 테 넌 트의 계정을 소유 하는 사용자의 ID 인 GUID에 대 한 문자열 표현입니다. |
| `Identifier` | 계정에 대 한 고유 식별자입니다. `Identifier`은 쉼표로 구분 된 `ObjectId` 및 `TenantId`의 연결 이며 b a s e 64로 인코딩되지 않습니다. |

`IAccount` 인터페이스는 단일 계정에 대 한 정보를 나타냅니다. 동일한 사용자가 다른 테 넌 트에 있을 수 있습니다. 즉, 사용자가 여러 계정을 가질 수 있습니다. 해당 멤버는 다음과 같습니다.

| 속성 | Description |
|----------|-------------|
| `Username` | UserPrincipalName (UPN) 형식의 표시할 때 값을 포함 하는 문자열입니다 (예: john.doe@contoso.com). 이 문자열은 null 일 수 있지만 HomeAccountId 및 HomeAccountId는 null이 아닙니다. 이 속성은 이전 버전의 MSAL.NET에서 `IUser`의 `DisplayableId` 속성을 대체 합니다. |
| `Environment` | 이 계정에 대 한 id 공급자를 포함 하는 문자열입니다 (예: `login.microsoftonline.com`). 이 속성은 `IUser`의 `IdentityProvider` 속성을 대체 합니다. 단,이 경우에는 클라우드 환경 뿐만 아니라 테 넌 트에 대 한 정보도 포함 되어 있습니다. `IdentityProvider` 단, 여기에는 값이 호스트만 있습니다. |
| `HomeAccountId` | 사용자에 대 한 홈 계정의 AccountId입니다. 이 속성은 Azure AD 테 넌 트 간에 사용자를 고유 하 게 식별 합니다. |

### <a name="using-the-token-to-call-a-protected-api"></a>토큰을 사용 하 여 보호 된 API 호출

`AuthenticationResult` MSAL에서 반환 된 경우 (`result`) 보호 된 웹 API에 액세스 하기 전에 HTTP 권한 부여 헤더에 추가 해야 합니다.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```