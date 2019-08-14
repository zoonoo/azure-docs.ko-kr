---
title: 리디렉션 URL을 b2clogin.com으로 설정 - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C의 리디렉션 URL에 b2clogin.com을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927276"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Azure Active Directory B2C의 리디렉션 URL을 b2clogin.com으로 설정

Azure AD(Azure Active Directory) B2C 애플리케이션의 등록 및 로그인을 위한 ID 공급자를 설정하는 경우 리디렉션 URL을 지정해야 합니다. 이전에는 login.microsoftonline.com을 사용했지만 지금은 b2clogin.com을 사용해야 합니다.

> [!NOTE]
> B2clogin.com에서 JavaScript 클라이언트 쪽 코드 (현재 미리 보기 상태)를 사용할 수 있습니다. Login.microsoftonline.com를 사용 하는 경우 사용자 지정 페이지에서 JavaScript 코드가 제거 됩니다. 사용자 지정 페이지에서 HTML 양식 요소를 제거 하는 등의 login.microsoftonline.com에도 추가 보안 제한이 적용 됩니다. 

b2clogin.com을 사용하면 다음과 같은 추가적인 이점이 제공됩니다.

- 쿠키 헤더에서 Microsoft 서비스가 사용하는 공간이 줄어듭니다.
- URL에 더 이상 Microsoft에 대한 참조가 포함되지 않습니다. `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp` )을 입력합니다.

> [!NOTE]
> 다음과 같이 테 넌 트 이름 및 테 넌 트 GUID를 모두 사용할 수 있습니다.
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(계속 참조 `onmicrosoft.com`)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(이 경우에는 Microsoft에 대 한 참조가 없습니다.)
>
> 그러나 Azure Active Directory B2C 테 넌 트에 대해서는 _사용자 지정 도메인_ 을 사용할 수 없습니다 `https://your-tenant-name.b2clogin.com/your-custom-domain-name` . 예를 들어는 작동 _하지 않습니다_ .

b2clogin.com을 사용하는 경우 변경해야 할 수 있는 다음 설정을 고려하세요.

- ID 공급자 애플리케이션의 리디렉션 URL에 b2clogin.com이 사용되도록 설정합니다. 
- 사용자 흐름 참조 및 토큰 엔드포인트에 대해 b2clogin.com을 사용하도록 Azure AD B2C 애플리케이션을 설정합니다. 
- MSAL을 사용하는 경우 **ValidateAuthority** 속성을 `false`로 설정해야 합니다.
- [사용자 인터페이스 사용자 지정](active-directory-b2c-ui-customization-custom-dynamic.md)의 CORS 설정에서 정의한 **허용된 원본**을 변경해야 합니다.  

## <a name="change-redirect-urls"></a>리디렉션 URL 변경

b2clogin.com을 사용하려면 ID 공급자 애플리케이션의 설정에서 신뢰할 수 있는 URL 목록을 찾은 후 Azure AD B2C로 다시 리디렉션하도록 변경합니다.  현재는 일부 login.microsoftonline.com 사이트로 다시 리디렉션하도록 설정한 상태일 것입니다. 

`your-tenant-name.b2clogin.com`이 인증되도록 리디렉션 URL을 변경해야 합니다. `your-tenant-name`을 Azure AD B2C 테넌트의 이름으로 바꾸고 `/te`가 URL에 있으면 제거합니다. ID 공급자마다 이 URL이 약간씩 다르므로 해당 페이지에서 정확한 URL을 확인하세요.

다음 문서에서 ID 공급자의 설정 정보를 찾을 수 있습니다.

- [Microsoft 계정](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Custom OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>애플리케이션 업데이트

Azure AD B2C 애플리케이션은 사용자 흐름 참조 및 토큰 엔드포인트 등의 여러 위치에서 `login.microsoftonline.com`을 참조할 수 있습니다.  권한 부여 엔드포인트, 토큰 엔드포인트 및 발급자가 `your-tenant-name.b2clogin.com`을 사용하도록 업데이트되었는지 확인합니다.  

## <a name="set-the-validateauthority-property"></a>ValidateAuthority 속성 설정

MSAL을 사용하는 경우 **ValidateAuthority** 속성을 `false`로 설정합니다. **ValidateAuthority**가 `false`로 설정된 경우 리디렉션은 b2clogin.com으로 허용됩니다. 

다음 예제에서는 이 속성을 설정하는 방법을 보여 줍니다.

[.NET용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

[JavaScript용 MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
