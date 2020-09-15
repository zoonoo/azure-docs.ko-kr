---
title: 응용 프로그램 및 Api를 b2clogin.com로 마이그레이션
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 리디렉션 URL에 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 53d41b5024b29a8c6c394d65a3ce36f8bb878fc2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90524983"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C의 리디렉션 URL을 b2clogin.com으로 설정

Azure Active Directory B2C (Azure AD B2C) 응용 프로그램에서 등록 및 로그인을 위해 id 공급자를 설정 하는 경우 리디렉션 URL을 지정 해야 합니다. Azure AD B2C를 사용 하 여 사용자를 인증 하기 위해 응용 프로그램 및 Api에서 *login.microsoftonline.com* 을 더 이상 참조 하지 않아야 합니다. 대신 모든 새 응용 프로그램에 대해 *b2clogin.com* 를 사용 하 고 기존 응용 프로그램을 *login.microsoftonline.com* 에서 *b2clogin.com*로 마이그레이션합니다.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Login.microsoftonline.com의 사용 중단

04 년 12 월 2019에는 **04 년 12 월 2020**에 Azure AD B2C에서 login.microsoftonline.com 지원의 예약 된 사용 중지가 발표 되었습니다.

[사용 중단 login.microsoftonline.com Azure Active Directory B2C](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Login.microsoftonline.com의 사용 중단은 04 년 12 월 2020에 모든 Azure AD B2C 테 넌 트에 적용 되 고 기존 테 넌 트 1 년간 b2clogin.com로 마이그레이션하는 것을 제공 합니다. 2019 년 12 월 4 일 이후에 만들어진 새 테 넌 트는 login.microsoftonline.com의 요청을 수락 하지 않습니다. 모든 기능은 b2clogin.com 끝점에서 동일 하 게 유지 됩니다.

Login.microsoftonline.com의 사용 중단은 테 넌 트 Azure Active Directory 영향을 주지 않습니다. Azure Active Directory B2C 테 넌 트가이 변경의 영향을 받습니다.

## <a name="what-endpoints-does-this-apply-to"></a>이에 적용 되는 끝점
B2clogin.com로의 전환은 사용자를 인증 하기 위해 Azure AD B2C 정책 (사용자 흐름 또는 사용자 지정 정책)을 사용 하는 인증 끝점에만 적용 됩니다. 이러한 끝점에는 `<policy-name>` 사용 해야 하 Azure AD B2C 정책을 지정 하는 매개 변수가 있습니다. [Azure AD B2C 정책에 대해 자세히 알아보세요](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

이러한 끝점은 다음과 같습니다.
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

또는 `<policy-name>` 쿼리 매개 변수로를 전달할 수도 있습니다.
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> ' Policy ' 매개 변수를 사용 하는 끝점은 업데이트 되어야 하 고 [id 공급자 리디렉션 url](#change-identity-provider-redirect-urls)도 업데이트 해야 합니다.

일부 Azure AD B2C 고객은 OAuth 2.0 클라이언트 자격 증명 부여 흐름과 같은 Azure AD enterprise 테 넌 트의 공유 기능을 사용 합니다. 이러한 기능은 *정책 매개 변수를 포함 하지*않는 Azure AD의 login.microsoftonline.com 끝점을 사용 하 여 액세스 됩니다. __이러한 끝점에는 영향을 주지 않습니다__.

## <a name="benefits-of-b2clogincom"></a>B2clogin.com의 이점

리디렉션 URL로 *b2clogin.com* 를 사용 하는 경우:

* 쿠키 헤더에서 Microsoft 서비스가 사용하는 공간이 줄어듭니다.
* 리디렉션 Url은 더 이상 Microsoft에 대 한 참조를 포함할 필요가 없습니다.
* JavaScript 클라이언트 쪽 코드가 사용자 지정 된 페이지에서 지원 됩니다 (현재 [미리 보기로](user-flow-javascript-overview.md)제공 됨). 보안 제한으로 인해 *login.microsoftonline.com*를 사용 하는 경우 JavaScript 코드 및 HTML 양식 요소가 사용자 지정 페이지에서 제거 됩니다.

## <a name="overview-of-required-changes"></a>필수 변경 사항 개요

응용 프로그램을 *b2clogin.com*로 마이그레이션하기 위해 수행 해야 할 몇 가지 수정 사항이 있습니다.

* *B2clogin.com*를 참조 하도록 id 공급자의 응용 프로그램에서 리디렉션 URL을 변경 합니다.
* 사용자 흐름 및 토큰 끝점 참조에서 *b2clogin.com* 를 사용 하도록 Azure AD B2C 응용 프로그램을 업데이트 합니다. 여기에는 MSAL (Microsoft 인증 라이브러리)과 같은 인증 라이브러리의 사용 업데이트가 포함 될 수 있습니다.
* CORS 설정에서 [사용자 인터페이스 사용자 지정](custom-policy-ui-customization.md)에 대해 정의한 모든 **허용 된 원본을** 업데이트 합니다.

이전 끝점은 다음과 같을 수 있습니다.
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

해당 하는 업데이트 된 끝점은 다음과 같습니다.
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Id 공급자 리디렉션 Url 변경

응용 프로그램을 만든 각 id 공급자의 웹 사이트에서 login.microsoftonline.com 대신 리디렉션할 모든 신뢰할 수 있는 Url을 변경 `your-tenant-name.b2clogin.com` 합니다. *login.microsoftonline.com*

B2clogin.com 리디렉션 Url에 사용할 수 있는 두 가지 형식이 있습니다. 첫 번째는 테 넌 트 도메인 이름 대신에 테 넌 트 ID (GUID)를 사용 하 여 URL의 어디에 나 "Microsoft"가 표시 되지 않도록 하는 이점을 제공 합니다.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

두 번째 옵션은 형식의 테 넌 트 도메인 이름을 사용 합니다 `your-tenant-name.onmicrosoft.com` . 예를 들어:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

두 형식 모두:

* `{your-tenant-name}`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
* `/te`URL에 있으면를 제거 합니다.

## <a name="update-your-applications-and-apis"></a>응용 프로그램 및 Api 업데이트

Azure AD B2C 지원 응용 프로그램 및 Api의 코드는 `login.microsoftonline.com` 여러 위치에서 참조할 수 있습니다. 예를 들어 코드에 사용자 흐름과 토큰 끝점에 대 한 참조가 있을 수 있습니다. 다음을 대신 참조로 업데이트 합니다 `your-tenant-name.b2clogin.com` .

* 권한 부여 엔드포인트
* 토큰 엔드포인트
* 토큰 발급자

예를 들어 Contoso의 등록/로그인 정책에 대 한 기관 끝점은 이제 다음과 같습니다.

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

OWIN 기반 웹 응용 프로그램을 b2clogin.com로 마이그레이션하는 방법에 대 한 자세한 내용은 [OWIN 기반 웹 API를 b2clogin.com로 마이그레이션](multiple-token-endpoints.md)을 참조 하세요.

Azure AD B2C에서 보호 하는 Azure API Management Api를 마이그레이션하려면 Azure AD B2C를 사용 하 여 [azure API MANAGEMENT API 보안](secure-api-management.md)설정의 [b2clogin.com로 마이그레이션](secure-api-management.md#migrate-to-b2clogincom) 섹션을 참조 하세요.

## <a name="microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET ValidateAuthority 속성

[MSAL.NET][msal-dotnet] v2 이전 버전을 사용 하는 경우 b2clogin.com로 리디렉션을 허용 하도록 **validateauthority** 속성을 클라이언트 인스턴스화에 설정으로 설정 합니다 `false` . *b2clogin.com* `false`MSAL.NET v3 이상에는이 값을로 설정 하지 않아도 됩니다.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>JavaScript validateAuthority 속성의 MSAL

JavaScript v 1.2.2 이전 버전 [에 Msal][msal-js] 을 사용 하는 경우 **validateauthority** 속성을로 설정 `false` 합니다.

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

`validateAuthority: true`MSAL.js 1.3.0 + (기본값)에서를 설정 하는 경우 다음을 사용 하 여 유효한 토큰 발급자도 지정 해야 합니다 `knownAuthorities` .

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>다음 단계

OWIN 기반 웹 응용 프로그램을 b2clogin.com로 마이그레이션하는 방법에 대 한 자세한 내용은 [OWIN 기반 웹 API를 b2clogin.com로 마이그레이션](multiple-token-endpoints.md)을 참조 하세요.

Azure AD B2C에서 보호 하는 Azure API Management Api를 마이그레이션하려면 Azure AD B2C를 사용 하 여 [azure API MANAGEMENT API 보안](secure-api-management.md)설정의 [b2clogin.com로 마이그레이션](secure-api-management.md#migrate-to-b2clogincom) 섹션을 참조 하세요.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
