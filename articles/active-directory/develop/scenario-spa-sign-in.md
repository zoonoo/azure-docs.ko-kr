---
title: 단일 페이지 앱 로그인 & 사인아웃 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기(로그인)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159967"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>단일 페이지 응용 프로그램: 로그인 및 로그아웃

단일 페이지 응용 프로그램의 코드에 로그인을 추가하는 방법을 알아봅니다.

응용 프로그램에서 API에 액세스하기 위해 토큰을 얻으려면 인증된 사용자 컨텍스트가 필요합니다. 다음 두 가지 방법으로 MSAL.js에서 응용 프로그램에 사용자를 로그인할 수 있습니다.

* [팝업 창](#sign-in-with-a-pop-up-window), 메서드를 `loginPopup` 사용하여
* [로 리디렉션](#sign-in-with-redirect): `loginRedirect` 메서드를 사용하여

로그인 시 사용자가 동의해야 하는 API의 범위를 선택적으로 전달할 수도 있습니다.

> [!NOTE]
> 응용 프로그램에서 이미 인증된 사용자 컨텍스트 또는 ID 토큰에 액세스할 수 있는 경우 로그인 단계를 건너뛰고 토큰을 직접 획득할 수 있습니다. 자세한 내용은 [MSAL.js 로그인이 없는 SSO를](msal-js-sso.md#sso-without-msaljs-login)참조하십시오.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중 선택

응용 프로그램에서 팝업 및 리디렉션 메서드를 모두 사용할 수 없습니다. 팝업 또는 리디렉션 환경 중에서 선택할 수 있는 방법은 응용 프로그램 흐름에 따라 다릅니다.

* 인증 하는 동안 사용자가 기본 응용 프로그램 페이지에서 멀리 이동 하지 않으려면 팝업 방법을 권장 합니다. 인증 리디렉션은 팝업 창에서 발생하므로 주 응용 프로그램의 상태가 유지됩니다.

* 사용자에게 브라우저 제약 조건또는 팝업 창이 비활성화된 정책이 있는 경우 리디렉션 메서드를 사용할 수 있습니다. Internet Explorer에 [팝업 창에 알려진 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)있기 때문에 Internet Explorer 브라우저에서 리디렉션 메서드를 사용합니다.

## <a name="sign-in-with-a-pop-up-window"></a>팝업 창으로 로그인

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 각도 래퍼를 사용하면 경로 정의에 추가하여 `MsalGuard` 응용 프로그램의 특정 경로를 보호할 수 있습니다. 이 가드는 해당 경로에 액세스할 때 로그인하는 메서드를 호출합니다.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

팝업 창 환경의 경우 구성 `popUp` 옵션을 사용하도록 설정합니다. 동의가 필요한 범위를 다음과 같이 전달할 수도 있습니다.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```
---

## <a name="sign-in-with-redirect"></a>리디렉션으로 로그인

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

리디렉션 메서드는 기본 앱에서 멀어지기 때문에 약속을 반환하지 않습니다. 반환된 토큰을 처리하고 액세스하려면 리디렉션 메서드를 호출하기 전에 성공 및 오류 콜백을 등록해야 합니다.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

여기에 있는 코드는 팝업 창으로 로그인에 대한 섹션의 앞에서 설명한 것과 동일합니다. 기본 흐름은 리디렉션됩니다.

> [!NOTE]
> ID 토큰은 동의된 범위를 포함하지 않으며 인증된 사용자만 나타냅니다. 동의된 범위는 다음 단계에서 획득할 액세스 토큰에 반환됩니다.

---

## <a name="sign-out"></a>로그아웃

MSAL 라이브러리는 `logout` 브라우저 저장소의 캐시를 지우고 Azure Active Directory(Azure AD)로 로그아웃 요청을 보내는 메서드를 제공합니다. 로그아웃 후 라이브러리는 기본적으로 응용 프로그램 시작 페이지로 리디렉션됩니다.

로그아웃 후 리디렉션할 URI를 설정하여 `postLogoutRedirectUri`구성할 수 있습니다. 이 URI는 응용 프로그램 등록에서 로그아웃 URI로 등록해야 합니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱용 토큰 획득](scenario-spa-acquire-token.md)
