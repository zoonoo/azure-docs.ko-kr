---
title: 단일 페이지 응용 프로그램 (로그인)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (로그인)을 빌드하는 방법을 알아봅니다
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
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138797"
---
# <a name="single-page-application---sign-in"></a>단일 페이지 응용 프로그램-로그인

단일 페이지 응용 프로그램에 대 한 코드에 로그인을 추가 하는 방법에 알아봅니다.

응용 프로그램에서 Api에 액세스 하려고 토큰을 받으려면 먼저 인증 된 사용자 컨텍스트를 해야 합니다. 사용자가 두 가지 방법으로 MSAL.js에서 응용 프로그램에 서명할 수 있습니다.

* [팝업 창을 사용 하 여 로그인](#sign-in-with-a-pop-up-window) 를 사용 하 여 `loginPopup` 메서드
* [리디렉션을 사용 하 여 로그인](#sign-in-with-redirect) 를 사용 하 여 `loginRedirect` 메서드

또한 필요에 따라 로그인 시 동의 해야 하는 Api의 범위를 전달할 수 있습니다.

> [!NOTE]
> 응용 프로그램에 이미 인증 된 사용자 컨텍스트 또는 id에 액세스할 수 있는 경우 토큰을 로그인 단계를 건너뛰고 직접 토큰을 획득할 수 있습니다. 자세한 내용은 참조 하세요. [msal.js 로그인 없이 sso](msal-js-sso.md#sso-without-msaljs-login)합니다.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 경험 중에서 선택

응용 프로그램에 팝업 및 리디렉션 방법의 조합을 사용할 수 없습니다. 팝업 또는 리디렉션 환경 간의 선택 응용 프로그램 흐름에 따라 달라 집니다.

* 사용자를 인증 하는 동안 주 응용 프로그램 페이지를 벗어나려 않으려면는 것이 좋습니다 팝업 메서드를 사용 합니다. 팝업 창에 인증 리디렉션 수행, 기본 응용 프로그램의 상태 그대로 유지 됩니다.

* 리디렉션 메서드를 사용 해야 하는 경우도 있습니다. 응용 프로그램의 사용자가 브라우저 제약 조건 또는 정책을 팝업 창이 비활성화는 리디렉션 메서드를 사용할 수 있습니다. 특정 하므로 Internet Explorer 브라우저를 사용 하 여 리디렉션 메서드를 사용 [Internet Explorer를 사용 하 여 문제를 알려진](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) 팝업 창을 처리 하는 경우.

## <a name="sign-in-with-a-pop-up-window"></a>팝업 창을 사용 하 여 로그인

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

MSAL Angular 래퍼를 사용 하면 추가 하면 응용 프로그램에서 특정 경로 보호할 수 있습니다는 `MsalGuard` 경로 정의 합니다. 이 보호는 해당 경로 액세스할 때 로그인 메서드를 호출 합니다.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

팝업 창 경험을 사용 하도록 설정 된 `popUp` 구성 옵션입니다. 또한 다음과 같은 동의 필요로 하는 범위를 전달할 수 있습니다.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>리디렉션을 사용 하 여 로그인

### <a name="javascript"></a>JavaScript

리디렉션 메서드는 기본 앱에서 탐색 인해 프라미스를 반환 하지 않습니다. 처리 하 고 반환 된 토큰을 액세스, 성공 및 오류 콜백 리디렉션 메서드를 호출 하기 전에 등록 해야 합니다.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

여기에서 코드는 로그인 팝업 창 섹션에서 설명한 것과 동일 합니다. 기본 흐름은 리디렉션.

> [!NOTE]
> ID 토큰 승인된 범위를 포함 하지 않습니다 하 고만 인증된 된 사용자를 나타냅니다. 승인된 범위는 다음 단계에서 획득 하는 액세스 토큰에서 반환 됩니다.

## <a name="sign-out"></a>로그아웃

MSAL 라이브러리에서 제공 된 `logout` 브라우저 저장소에 캐시가 지워집니다 하 고 Azure AD로 로그 아웃 요청을 전송 하는 메서드. 로그 아웃 후, 리디렉션됩니다 다시 응용 프로그램 시작 페이지를 기본적으로.

URI를 리디렉션해야 하는지 로그인 한 후 로그 아웃 설정 하 여 구성할 수 있습니다는 `postLogoutRedirectUri`합니다. 이 URI는 또한 응용 프로그램 등록에서 로그 아웃 URI로 등록 되어야 합니다.

### <a name="javascript"></a>JavaScript

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

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [앱에 대 한 토큰 가져오기](scenario-spa-acquire-token.md)
