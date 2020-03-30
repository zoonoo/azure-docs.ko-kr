---
title: 응용 프로그램 및 API를 마이그레이션하여 b2clogin.com
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 리디렉션 URL에 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189993"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C의 리디렉션 URL을 b2clogin.com으로 설정

Azure Active Directory B2C(Azure AD B2C) 응용 프로그램에서 등록 및 로그인을 위한 ID 공급자를 설정할 때 리디렉션 URL을 지정해야 합니다. 응용 프로그램 및 API에서 *login.microsoftonline.com* 더 이상 참조해서는 안 됩니다. 대신 모든 새 응용 프로그램에 *b2clogin.com* 사용하고 기존 응용 프로그램을 *login.microsoftonline.com* *b2clogin.com.*

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>login.microsoftonline.com 사용 중단

2019년 12월 04일, Azure AD B2C에서 **2020년 12월 04일에**login.microsoftonline.com 지원의 예정된 은퇴를 발표했습니다.

[Azure Active Directory B2C가 login.microsoftonline.com 더 이상 사용되지 않습니다.](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

login.microsoftonline.com 사용 중단은 2020년 12월 04일에 모든 Azure AD B2C 테넌트에 적용되어 기존 테넌트에게 1년 동안 b2clogin.com 마이그레이션할 수 있도록 합니다. 2019년 12월 04일 이후에 생성된 새 테넌은 login.microsoftonline.com 요청을 수락하지 않습니다. 모든 기능은 b2clogin.com 끝점에서 동일하게 유지됩니다.

login.microsoftonline.com 사용 중단은 Azure Active Directory 테넌토리에 영향을 미치지 않습니다. Azure Active Directory B2C 테넌수만 이 변경의 영향을 받습니다.

## <a name="benefits-of-b2clogincom"></a>b2clogin.com 장점

리디렉션 URL로 *b2clogin.com* 사용하는 경우:

* 쿠키 헤더에서 Microsoft 서비스가 사용하는 공간이 줄어듭니다.
* 리디렉션 URL은 더 이상 Microsoft에 대한 참조를 포함할 필요가 없습니다.
* JavaScript 클라이언트 쪽 코드는 사용자 지정된 페이지에서 지원됩니다(현재 [미리 보기).](user-flow-javascript-overview.md) 보안 제한으로 인해 *login.microsoftonline.com*사용하는 경우 JavaScript 코드 및 HTML 양식 요소가 사용자 지정 페이지에서 제거됩니다.

## <a name="overview-of-required-changes"></a>필요한 변경 사항 개요

응용 프로그램을 *b2clogin.com*위해 마이그레이션하기 위해 몇 가지 수정해야 할 수 있습니다.

* id 공급자의 응용 프로그램에서 리디렉션 URL을 변경하여 *b2clogin.com.*
* Azure AD B2C 응용 프로그램을 업데이트하여 사용자 흐름 및 토큰 끝점 참조에서 *b2clogin.com* 사용합니다.
* [사용자 인터페이스 사용자 지정을](custom-policy-ui-customization.md)위해 CORS 설정에서 정의한 **허용된 원본을** 업데이트합니다.

## <a name="change-identity-provider-redirect-urls"></a>ID 공급자 리디렉션 URL 변경

응용 프로그램을 만든 각 ID 공급자의 웹 사이트에서 login.microsoftonline.com 대신 리디렉션하도록 모든 `your-tenant-name.b2clogin.com` 신뢰할 수 있는 URL을 변경합니다. *login.microsoftonline.com*

url을 리디렉션하기 b2clogin.com 두 가지 형식이 있습니다. 첫 번째는 테넌트 도메인 이름 대신 테넌트 ID(GUID)를 사용하여 URL의 아무 곳에도 "Microsoft"가 나타나지 않는 이점을 제공합니다.

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

두 번째 옵션은 테넌트 도메인 `your-tenant-name.onmicrosoft.com`이름을 의 형태로 사용합니다. 예를 들어:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

두 형식 모두:

* `{your-tenant-name}`은 Azure AD B2C 테넌트의 이름으로 바꿉니다.
* URL에 있는 경우 제거합니다. `/te`

## <a name="update-your-applications-and-apis"></a>응용 프로그램 및 API 업데이트

Azure AD B2C 지원 응용 프로그램 및 API의 `login.microsoftonline.com` 코드는 여러 위치에서 참조할 수 있습니다. 예를 들어 코드에 사용자 흐름 및 토큰 끝점에 대한 참조가 있을 수 있습니다. 대신 참조로 `your-tenant-name.b2clogin.com`다음을 업데이트합니다.

* 권한 부여 엔드포인트
* 토큰 엔드포인트
* 토큰 발급자

예를 들어 Contoso의 등록/로그인 정책에 대한 권한 끝점은 다음과 같은 것입니다.

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

OWIN 기반 웹 응용 프로그램을 b2clogin.com 마이그레이션하는 것에 대한 자세한 내용은 [OWIN 기반 웹 API를 마이그레이션하여 b2clogin.com.](multiple-token-endpoints.md)

Azure AD B2C로 보호되는 Azure API 관리 API를 마이그레이션하려면 [Azure AD B2C를 사용하여 Azure API 관리 API 보안의](secure-api-management.md)b2clogin.com 섹션으로 [마이그레이션을](secure-api-management.md#migrate-to-b2clogincom) 참조하십시오.

## <a name="microsoft-authentication-library-msal"></a>MSAL(Microsoft 인증 라이브러리)

### <a name="validateauthority-property"></a>유효성 검사 기관 속성

[MSAL.NET][msal-dotnet] v2 이전을 사용하는 경우 Redirects를 `false` *b2clogin.com*수 있도록 클라이언트 인스턴스화에서 **ValidateAuthority** 속성을 설정합니다. 이 설정은 MSAL.NET v3 이상에는 필요하지 않습니다.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

[자바 스크립트에 MSAL을][msal-js]사용하는 경우 :

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>다음 단계

OWIN 기반 웹 응용 프로그램을 b2clogin.com 마이그레이션하는 것에 대한 자세한 내용은 [OWIN 기반 웹 API를 마이그레이션하여 b2clogin.com.](multiple-token-endpoints.md)

Azure AD B2C로 보호되는 Azure API 관리 API를 마이그레이션하려면 [Azure AD B2C를 사용하여 Azure API 관리 API 보안의](secure-api-management.md)b2clogin.com 섹션으로 [마이그레이션을](secure-api-management.md#migrate-to-b2clogincom) 참조하십시오.

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
