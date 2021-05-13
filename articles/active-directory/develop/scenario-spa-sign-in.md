---
title: 단일 페이지 앱 로그인 및 로그아웃
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드하는 방법 알아보기(로그인)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5892904c3cfc475683d081a58b47e4bec1266ab9
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966812"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>단일 페이지 애플리케이션: 로그인 및 로그아웃

단일 페이지 애플리케이션의 코드에 로그인을 추가하는 방법을 알아봅니다.

애플리케이션의 API에 액세스하기 위한 토큰을 얻으려면 인증된 사용자 컨텍스트가 필요합니다. 다음 두 가지 방법으로 MSAL.js의 애플리케이션에 사용자를 로그인할 수 있습니다.

* [팝업 창](#sign-in-with-a-pop-up-window), `loginPopup` 메서드 사용
* [리디렉션](#sign-in-with-redirect), `loginRedirect` 메서드 사용

로그인 시 사용자의 동의가 필요한 API 범위를 선택적으로 전달할 수도 있습니다.

> [!NOTE]
> 애플리케이션에 이미 인증된 사용자 컨텍스트 또는 ID 토큰에 대한 액세스 권한이 있으면 로그인 단계를 건너뛰고 토큰을 직접 획득할 수 있습니다. 자세한 내용은 [MSAL.js 로그인을 사용하지 않는 SSO](msal-js-sso.md#sso-without-msaljs-login)를 참조하세요.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중에서 선택

애플리케이션에서 팝업과 리디렉션 방법을 둘 다 사용할 수는 없습니다. 팝업이나 리디렉션 환경 중 선택은 애플리케이션 흐름에 따라 다릅니다.

* 인증 중에 사용자가 기본 애플리케이션 페이지에서 이동하는 것을 원하지 않는 경우 팝업 방법을 권장합니다. 인증 리디렉션은 팝업 창에서 발생하기 때문에 기본 애플리케이션의 상태가 유지됩니다.

* 사용자에게 팝업 창을 사용하지 않게 설정하는 브라우저 제약 조건이나 정책이 있으면 리디렉션 메서드를 사용할 수 있습니다. [Internet Explorer의 팝업 창에 알려진 이슈](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)가 있으므로 Internet Explorer 브라우저에서 리디렉션 메서드를 사용합니다.

## <a name="sign-in-with-a-pop-up-window"></a>팝업 창을 사용하여 로그인


# <a name="javascript-msaljs-2x"></a>[JavaScript(MSAL.js 2.x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript(MSAL.js 1.x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular 래퍼를 사용하면 `MsalGuard`를 경로 정의에 추가하여 애플리케이션의 특정 경로를 보호할 수 있습니다. 이 가드는 해당 경로에 액세스할 때 로그인하는 메서드를 호출합니다.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

팝업 창 환경을 사용하려면 `popUp` 구성 옵션을 사용으로 설정합니다. 다음과 같이 동의가 필요한 범위도 전달할 수 있습니다.

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>리디렉션을 사용하여 로그인

# <a name="javascript-msaljs-2x"></a>[JavaScript(MSAL.js 2.x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise().then(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript(MSAL.js 1.x)](#tab/javascript1)

리디렉션 메서드는 기본 앱에서 이동해 나가기 때문에 프라미스를 반환하지 않습니다. 반환된 토큰을 처리하고 액세스하려면 리디렉션 메서드를 호출하기 전에 성공 및 오류 콜백을 등록합니다.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

여기에서 코드는 팝업 창을 사용한 로그인에 관한 섹션에서 앞서 설명한 것과 같습니다. 기본 흐름은 리디렉션입니다.

---

## <a name="sign-out"></a>로그아웃

MSAL 라이브러리는 브라우저 스토리지에서 캐시를 지우고 Azure AD(Azure Active Directory)에 로그아웃 요청을 보내는 `logout` 메서드를 제공합니다. 로그아웃 후 라이브러리는 기본적으로 애플리케이션 시작 페이지로 다시 리디렉션됩니다.

`postLogoutRedirectUri`를 설정하여 로그아웃 후 리디렉션해야 하는 URI를 구성할 수 있습니다. 이 URI는 애플리케이션 등록에서 로그아웃 URI로도 등록되어야 합니다.

# <a name="javascript-msaljs-2x"></a>[JavaScript(MSAL.js 2.x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript(MSAL.js 1.x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>다음 단계

본 시나리오의 다음 문서인 [앱용 토큰 획득](scenario-spa-acquire-token.md)으로 이동합니다.