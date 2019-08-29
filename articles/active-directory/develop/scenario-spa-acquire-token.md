---
title: 단일 페이지 응용 프로그램 (API를 호출 하는 토큰 획득)-Microsoft identity platform
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (API를 호출 하는 토큰 획득)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135704"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>단일 페이지 응용 프로그램-API를 호출 하는 토큰을 가져옵니다.

Msal를 사용 하 여 api에 대 한 토큰을 획득 하는 패턴은 먼저 `acquireTokenSilent` 메서드를 사용 하 여 자동 토큰 요청을 시도 하는 것입니다. 이 메서드가 호출 되 면 라이브러리는 먼저 브라우저 저장소의 캐시를 확인 하 여 유효한 토큰이 있는지 확인 하 고이를 반환 합니다. 캐시에 유효한 토큰이 없으면 숨겨진 iframe에서 Azure Active Directory (Azure AD)에 자동 토큰 요청을 보냅니다. 또한이 방법을 사용 하면 라이브러리에서 토큰을 갱신할 수 있습니다. Azure AD의 Single Sign-On 세션 및 토큰 수명 값에 대 한 자세한 내용은 [토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조 하세요.

Azure AD에 대 한 자동 토큰 요청은 만료 된 Azure AD 세션 또는 암호 변경과 같은 몇 가지 이유로 실패할 수 있습니다. 이 경우 사용자에 게 메시지를 표시 하는 대화형 메서드 중 하나를 호출 하 여 토큰을 획득할 수 있습니다.

* 을 사용 하 여 [팝업 창에서 토큰 획득](#acquire-token-with-a-pop-up-window)`acquireTokenPopup`
* 리디렉션을 사용 하 여 [토큰 획득](#acquire-token-with-redirect)`acquireTokenRedirect`

**팝업 또는 리디렉션 환경 중에서 선택**

 응용 프로그램에서 팝업 및 리디렉션 방법의 조합을 사용할 수 없습니다. 팝업 또는 리디렉션 환경 간의 선택은 응용 프로그램 흐름에 따라 달라 집니다.

* 인증 하는 동안 사용자가 주 응용 프로그램 페이지에서 벗어나 이동 하지 않으려면 팝업 방법을 사용 하는 것이 좋습니다. 인증 리디렉션은 팝업 창에서 발생 하므로 주 응용 프로그램의 상태는 유지 됩니다.

* 특정 한 경우에는 리디렉션 메서드를 사용 해야 할 수 있습니다. 응용 프로그램 사용자에 게 팝업 창이 사용 하지 않도록 설정 된 브라우저 제약 조건 또는 정책이 있는 경우 리디렉션 메서드를 사용할 수 있습니다. 또한 팝업 창을 처리할 때 [Internet explorer에 알려진 특정 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) 있으므로 internet explorer 브라우저에서 리디렉션 메서드를 사용 하는 것이 좋습니다.

액세스 토큰 요청을 작성할 때 액세스 토큰에 포함 하려는 API 범위를 설정할 수 있습니다. 요청 된 모든 범위는 액세스 토큰에서 부여할 수 없으며 사용자의 동의에 따라 결정 됩니다.

## <a name="acquire-token-with-a-pop-up-window"></a>팝업 창에서 토큰 획득

### <a name="javascript"></a>JavaScript

위 패턴은 팝업 환경을 위한 메서드를 사용 합니다.

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

### <a name="angular"></a>Angular

MSAL 각도 래퍼는 자동으로 액세스 토큰을 자동으로 획득 하 여 Api에 대 한 HTTP 요청에 연결 하는 HTTP 인터셉터를 추가 하는 편리한 기능을 제공 합니다.

`protectedResourceMap` 구성 옵션에서 api에 대 한 범위를 지정할 수 있으며,이 옵션은 토큰을 자동으로 가져올 때 msalinterceptor에서 요청 합니다.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

자동 토큰 획득의 성공 및 실패에 대 한 MSAL 각도는 구독할 수 있는 콜백을 제공 합니다. 구독을 취소 하는 것도 중요 합니다.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

또는 핵심 MSAL .js 라이브러리에 설명 된 대로 토큰 획득 메서드를 사용 하 여 명시적으로 토큰을 가져올 수도 있습니다.

## <a name="acquire-token-with-redirect"></a>리디렉션을 사용 하 여 토큰 획득

### <a name="javascript"></a>JavaScript

패턴은 위에서 설명한 대로 사용 되지만 토큰을 대화형으로 얻기 위한 리디렉션 메서드로 표시 됩니다. 위에서 언급 한 대로 리디렉션 콜백을 등록 해야 합니다.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>선택적 클레임에 대 한 요청
응용 프로그램에 대 한 토큰에 포함할 추가 클레임을 지정 하기 위해 앱에서 선택적 클레임을 요청할 수 있습니다. Id_token에서 선택적 클레임을 요청 하기 위해 문자열 형식 클레임 개체를 AuthenticationParameters. ts 클래스의 claimsRequest 필드로 보낼 수 있습니다.

다음과 같은 용도로 선택적 클레임을 사용할 수 있습니다.

- 응용 프로그램에 대 한 토큰에 추가 클레임을 포함 합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.


### <a name="javascript"></a>JavaScript
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
선택적 클레임에 대 한 자세한 내용을 보려면 [선택적 클레임](active-directory-optional-claims.md) 을 확인 하세요.


### <a name="angular"></a>Angular

이는 위에서 설명한 것과 같습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Web API 호출](scenario-spa-call-api.md)
