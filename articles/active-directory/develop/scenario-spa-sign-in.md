---
title: 단일 페이지 앱 로그인 & 로그 아웃-Microsoft identity platform | Microsoft
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (로그인)
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
ms.openlocfilehash: 2a73af0a0488043d31722b4dc46ca19530cf34ac
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443775"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>단일 페이지 응용 프로그램: 로그인 및 로그 아웃

단일 페이지 응용 프로그램에 대 한 코드에 로그인을 추가 하는 방법에 대해 알아봅니다.

응용 프로그램의 Api에 액세스 하기 위해 토큰을 가져오려면 인증 된 사용자 컨텍스트가 필요 합니다. 다음 두 가지 방법으로 MSAL.js의 응용 프로그램에 사용자를 로그인 할 수 있습니다.

* 메서드를 사용 하 여 [팝업 창](#sign-in-with-a-pop-up-window) `loginPopup`
* 메서드를 사용 하 여 [리디렉션](#sign-in-with-redirect) `loginRedirect`

사용자가 로그인 시 동의 해야 하는 Api의 범위를 선택적으로 전달할 수도 있습니다.

> [!NOTE]
> 응용 프로그램에 인증 된 사용자 컨텍스트 또는 ID 토큰에 대 한 액세스 권한이 이미 있는 경우 로그인 단계를 건너뛰고 토큰을 직접 가져올 수 있습니다. 자세한 내용은 [MSAL.js 로그인을 사용 하지 않는 SSO](msal-js-sso.md#sso-without-msaljs-login)를 참조 하세요.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중에서 선택

응용 프로그램에서 팝업 및 리디렉션 메서드를 모두 사용할 수는 없습니다. 팝업 또는 리디렉션 환경 간의 선택은 응용 프로그램 흐름에 따라 달라 집니다.

* 인증 하는 동안 사용자가 주 응용 프로그램 페이지에서 멀리 이동 하지 않도록 하려면 팝업 방법을 권장 합니다. 인증 리디렉션은 팝업 창에서 발생 하므로 주 응용 프로그램의 상태는 유지 됩니다.

* 사용자에 게 팝업 창이 사용 하지 않도록 설정 된 브라우저 제약 조건 또는 정책이 있으면 redirect 메서드를 사용할 수 있습니다. Internet [explorer의 팝업 창에 알려진 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)있으므로 internet explorer 브라우저에서 리디렉션 방법을 사용 합니다.

## <a name="sign-in-with-a-pop-up-window"></a>팝업 창으로 로그인


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

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

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

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

MSAL 각도 래퍼를 사용 하면 `MsalGuard` 경로 정의에를 추가 하 여 응용 프로그램의 특정 경로를 보호할 수 있습니다. 이 가드는 해당 경로에 액세스 하는 경우 메서드를 호출 하 여 로그인 합니다.

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

팝업 창 환경을 사용 하려면 구성 옵션을 사용 하도록 설정 `popUp` 합니다. 다음과 같이 동의가 필요한 범위를 전달할 수도 있습니다.

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

## <a name="sign-in-with-redirect"></a>리디렉션으로 로그인

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

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

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

리디렉션 메서드는 주 앱에서 벗어나 이동 하기 때문에 약속을 반환 하지 않습니다. 반환 된 토큰을 처리 하 고 액세스 하려면 리디렉션 메서드를 호출 하기 전에 성공 및 오류 콜백을 등록 해야 합니다.

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

여기에서 코드는 팝업 창을 사용한 로그인에 대 한 섹션의 앞부분에서 설명한 것과 같습니다. 기본 흐름은 리디렉션입니다.

---

## <a name="sign-out"></a>로그아웃

MSAL 라이브러리는 `logout` 브라우저 저장소에서 캐시를 지우고 Azure Active Directory (AZURE AD)에 대 한 로그 아웃 요청을 보내는 메서드를 제공 합니다. 로그 아웃 한 후 라이브러리는 기본적으로 응용 프로그램 시작 페이지로 다시 리디렉션됩니다.

을 설정 하 여 로그 아웃 한 후 리디렉션해야 하는 URI를 구성할 수 있습니다 `postLogoutRedirectUri` . 또한이 URI는 응용 프로그램 등록에서 로그 아웃 URI로 등록 되어야 합니다.

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

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

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

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

이 시나리오의 다음 문서로 이동 하 여 [앱에 대 한 토큰을 획득](scenario-spa-acquire-token.md)합니다.