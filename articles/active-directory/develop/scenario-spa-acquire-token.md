---
title: 단일 페이지 응용 프로그램 (API를 호출 하는 토큰 획득)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (API를 호출 하는 토큰 획득)을 빌드하는 방법을 알아봅니다
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138809"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>단일 페이지 응용 프로그램-API를 호출 하는 토큰 획득

MSAL.js 사용 하 여 Api를 사용 하 여 자동 토큰 요청을 먼저 시도 하는 것에 대 한 토큰을 얻기 위한 패턴을 `acquireTokenSilent` 메서드. 이 메서드를 호출 하는 경우 라이브러리는 먼저 유효한 토큰 존재 하 고 반환 하는 경우를 확인 하려면 브라우저 저장소에 캐시를 확인 합니다. 캐시에 유효한 토큰이 있으면 보냅니다 자동 토큰 요청을 Azure Active Directory (Azure AD)에 숨겨진된 iframe에서. 또한이 메서드는 토큰을 갱신 하는 라이브러리 수 있습니다. Single sign-on 세션 및 Azure AD에서 토큰 수명 값에 대 한 자세한 내용은 참조 하세요. [토큰 수명](active-directory-configurable-token-lifetimes.md)합니다.

Azure AD에 자동 토큰 요청은 만료 된 Azure AD 세션 또는 암호 변경과 같은 몇 가지 이유로 실패할 수 있습니다. 이 경우 토큰을 획득 하 (사용자 라는 메시지가 표시 됩니다)는 대화형 방법 중 하나을 호출할 수 있습니다.

* [팝업 창 사용 하 여 토큰을 획득](#acquire-token-with-a-pop-up-window) 를 사용 하 여 `acquireTokenPopup`
* [리디렉션 사용한 토큰을 획득](#acquire-token-with-redirect) 를 사용 하 여 `acquireTokenRedirect`

**팝업 또는 리디렉션 경험 중에서 선택**

 응용 프로그램에 팝업 및 리디렉션 방법의 조합을 사용할 수 없습니다. 팝업 또는 리디렉션 환경 간의 선택 응용 프로그램 흐름에 따라 달라 집니다.

* 사용자를 인증 하는 동안 주 응용 프로그램 페이지를 벗어나려 않으려면는 것이 좋습니다 팝업 메서드를 사용 합니다. 팝업 창에 인증 리디렉션 수행, 기본 응용 프로그램의 상태 그대로 유지 됩니다.

* 리디렉션 메서드를 사용 해야 하는 경우도 있습니다. 응용 프로그램의 사용자가 브라우저 제약 조건 또는 정책을 팝업 창이 비활성화는 리디렉션 메서드를 사용할 수 있습니다. 개 있으므로 특정 Internet Explorer 브라우저를 사용 하 여 리디렉션 메서드를 사용 하는 것이 권장 수도 [Internet Explorer를 사용 하 여 문제를 알려진](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) 팝업 창을 처리 하는 경우.

액세스 토큰을 액세스 토큰 요청을 빌드할 때 포함 하려는 API 범위를 설정할 수 있습니다. 모든 요청된 범위는 액세스 토큰에 부여 될 수 있습니다 하 고 사용자의 동의에 따라 달라 집니다 note 합니다.

## <a name="acquire-token-with-a-pop-up-window"></a>팝업 창에는 토큰 획득

### <a name="javascript"></a>JavaScript

팝업 환경에 대 한 메서드를 사용 하 여 위의 패턴:

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

HTTP 인터셉터를 추가 하는 편리 하 게 MSAL Angular 래퍼 `MsalInterceptor` 는 자동으로 자동으로 액세스 토큰을 획득 하 고 Api에 HTTP 요청에 연결 합니다.

Api에 대 한 범위를 지정할 수 있습니다는 `protectedResourceMap` 구성 옵션을 자동으로 토큰을 획득할 때 MsalInterceptor를 요청 합니다.

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

성공 및 실패 한 자동 토큰 획득, MSAL Angular 제공 콜백 구독할 수 있습니다. 구독을 취소 해야 하는 일을 해야 이기도 합니다.

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

또는 핵심 MSAL.js 라이브러리에 설명 된 대로 acquire 토큰 메서드를 사용 하 여 토큰을 명시적으로 얻을 수 있습니다.

## <a name="acquire-token-with-redirect"></a>리디렉션 사용한 토큰 획득

### <a name="javascript"></a>JavaScript

패턴을 대화형으로 토큰을 획득 하는 리디렉션 메서드를 사용 하 여 표시 된 것 이지만 위에서 설명 됩니다. 위에서 언급 한 대로 리디렉션 콜백을 등록할 해야 note 합니다.

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

### <a name="angular"></a>Angular

이 위에서 설명한 것과 동일 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Web API 호출](scenario-spa-call-api.md)
