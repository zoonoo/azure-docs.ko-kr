---
title: 웹 API(단일 페이지 앱)를 호출하는 토큰 획득 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기(API를 호출하는 토큰 획득)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419655"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>단일 페이지 응용 프로그램: API를 호출하는 토큰을 획득합니다.

MSAL.js를 사용하여 API에 대한 토큰을 획득하는 패턴은 먼저 `acquireTokenSilent` 메서드를 사용하여 자동 토큰 요청을 시도하는 것입니다. 이 메서드가 호출되면 라이브러리는 먼저 브라우저 저장소의 캐시를 검사하여 유효한 토큰이 있는지 확인하고 반환합니다. 캐시에 유효한 토큰이 없는 경우 숨겨진 iframe에서 Azure Active Directory(Azure AD)로 자동 토큰 요청을 보냅니다. 이 방법을 사용하면 라이브러리에서 토큰을 갱신할 수도 있습니다. Azure AD의 단일 사인온 세션 및 토큰 수명 값에 대한 자세한 내용은 [토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하십시오.

Azure AD에 대한 자동 토큰 요청은 만료된 Azure AD 세션 또는 암호 변경과 같은 이유로 실패할 수 있습니다. 이 경우 사용자에게 토큰을 획득하라는 대화형 메서드 중 하나를 호출할 수 있습니다.

* [팝업 창을](#acquire-a-token-with-a-pop-up-window)사용하여`acquireTokenPopup`
* [로 리디렉션](#acquire-a-token-with-a-redirect)`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경 중에서 선택

 응용 프로그램에서 팝업 및 리디렉션 메서드를 모두 사용할 수 없습니다. 팝업 또는 리디렉션 환경 중에서 선택할 수 있는 방법은 응용 프로그램 흐름에 따라 다릅니다.

* 인증 하는 동안 사용자가 기본 응용 프로그램 페이지에서 멀리 이동 하지 않으려면 팝업 방법을 권장 합니다. 인증 리디렉션은 팝업 창에서 발생하므로 주 응용 프로그램의 상태가 유지됩니다.

* 사용자에게 팝업 창이 비활성화된 브라우저 제약 조건 또는 정책이 있는 경우 리디렉션 메서드를 사용할 수 있습니다. Internet Explorer에 [팝업 창에 알려진 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)있기 때문에 Internet Explorer 브라우저에서 리디렉션 메서드를 사용합니다.

액세스 토큰이 액세스 토큰 요청을 빌드할 때 포함할 API 범위를 설정할 수 있습니다. 요청된 모든 범위는 액세스 토큰에 부여되지 않을 수 있습니다. 사용자의 동의에 따라 다릅니다.

## <a name="acquire-a-token-with-a-pop-up-window"></a>팝업 창으로 토큰 획득

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 코드는 앞서 설명한 패턴과 팝업 환경을 위한 메서드를 결합합니다.

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL 각도 래퍼는 HTTP 인터셉터를 제공하며, 이 인터셉터는 자동으로 액세스 토큰을 자동으로 획득하여 API에 HTTP 요청에 연결합니다.

구성 옵션에서 API에 대한 범위를 `protectedResourceMap` 지정할 수 있습니다. `MsalInterceptor`토큰을 자동으로 획득할 때 이러한 범위를 요청합니다.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

자동 토큰 획득의 성공과 실패를 위해 MSAL Angular는 구독할 수 있는 콜백을 제공합니다. 구독을 취소하는 것도 중요합니다.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

또는 핵심 MSAL.js 라이브러리에 설명된 대로 획득 토큰 메서드를 사용하여 토큰을 명시적으로 획득할 수 있습니다.

---

## <a name="acquire-a-token-with-a-redirect"></a>리디렉션으로 토큰 획득

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 패턴은 앞서 설명한 대로 이지만 대화식으로 토큰을 획득 하는 리디렉션 메서드와 함께 표시 됩니다. 앞에서 설명한 대로 리디렉션 콜백을 등록해야 합니다.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>선택적 클레임 요청

선택적 클레임은 다음과 같은 목적으로 사용할 수 있습니다.

- 응용 프로그램에 대한 토큰에 추가 클레임을 포함합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

에서 `IdToken`선택적 클레임을 요청하려면 문자열화된 클레임 개체를 `claimsRequest` `AuthenticationParameters.ts` 클래스 필드에 보낼 수 있습니다.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하십시오.

# <a name="angular"></a>[Angular](#tab/angular)

이 코드는 앞서 설명한 것과 동일합니다.

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 API 호출](scenario-spa-call-api.md)
