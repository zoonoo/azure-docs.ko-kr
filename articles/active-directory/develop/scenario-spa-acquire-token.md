---
title: 웹 API를 호출하도록 토큰 획득(단일 페이지 앱) | Azure
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드(API를 호출하도록 토큰 획득)하는 방법 알아보기
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/2/2021
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 2e4369c729fc5497f615f64c1f7235d19c293f98
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2021
ms.locfileid: "108227743"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>단일 페이지 애플리케이션: API를 호출하도록 토큰 획득

[MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)를 사용하여 API의 토큰을 획득하는 패턴은 먼저 `acquireTokenSilent` 메서드를 사용하여 자동 토큰 요청을 시도하는 것입니다. 이 메서드가 호출되면 라이브러리는 먼저 브라우저 스토리지의 캐시를 확인하여 유효한 토큰이 있는지 확인하고 반환합니다. 캐시에 유효한 토큰이 없으면 새로 고침 토큰을 사용하여 토큰을 가져오려고 시도합니다. 새로 고침 토큰의 24시간 수명이 만료된 경우 MSAL.js는 숨겨진 iframe을 열어 새 권한 부여 코드를 자동으로 요청하고 이를 유효한 새 새로 고침 토큰으로 교환합니다. Azure AD의 Single Sign-On 세션과 토큰 수명 값에 대한 자세한 내용은 [토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하세요.

Azure AD에 대한 자동 토큰 요청은 암호 변경 또는 업데이트된 조건부 액세스 정책과 같은 이유로 실패할 수 있습니다.  대개 이러한 실패는 새로 고침 토큰의 24시간 수명이 만료되고 [브라우저에서 타사 쿠키를 차단](reference-third-party-cookies-spas.md)하기 때문입니다. 이로 인해 숨겨진 iframe을 사용하여 사용자를 계속 인증할 수 없습니다.  이러한 경우에는 토큰을 얻기 위해 대화형 메서드(사용자에게 메시지가 표시될 수 있음) 중 하나를 호출해야 합니다.

* [팝업 창](#acquire-a-token-with-a-pop-up-window)(`acquireTokenPopup` 사용)
* [리디렉션](#acquire-a-token-with-a-redirect)(`acquireTokenRedirect` 사용)

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>팝업 또는 리디렉션 환경에서 선택

팝업이나 리디렉션 환경 중 선택은 애플리케이션 흐름에 따라 다릅니다.

* 인증 중에 사용자가 기본 애플리케이션 페이지에서 이동하는 것을 원하지 않는 경우 팝업 방법을 권장합니다. 인증 리디렉션은 팝업 창에서 발생하기 때문에 기본 애플리케이션의 상태가 유지됩니다.

* 사용자에게 팝업 창을 사용하지 않게 설정하는 브라우저 제약 조건이나 정책이 있으면 리디렉션 메서드를 사용할 수 있습니다. [Internet Explorer의 팝업 창에 알려진 이슈](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md#popups)가 있으므로 Internet Explorer 브라우저에서 리디렉션 메서드를 사용합니다.

액세스 토큰 요청을 빌드할 때 액세스 토큰에 포함하려는 API 범위를 설정할 수 있습니다. 모든 요청 범위가 액세스 토큰에 부여되지 않을 수 있습니다. 사용자 동의에 따라 다릅니다.

## <a name="acquire-a-token-with-a-pop-up-window"></a>팝업 창을 사용하여 토큰 획득

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

다음 코드에서는 앞에서 설명한 패턴과 팝업 환경의 메서드를 결합합니다.

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error instanceof InteractionRequiredAuthError) {
        publicClientApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
            let accessToken = accessTokenResponse.accessToken;
            // Call your API with token
            callApi(accessToken);
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

다음 코드에서는 앞에서 설명한 패턴과 팝업 환경의 메서드를 결합합니다.

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

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

MSAL Angular 래퍼는 자동으로 액세스 토큰을 획득하고 API에 대한 HTTP 요청에 연결하는 HTTP 인터셉터를 제공합니다.

`protectedResourceMap` 구성 옵션에서 API 범위를 지정할 수 있습니다. `MsalInterceptor`는 자동으로 토큰을 획득할 경우 이러한 범위를 요청합니다.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule } from '@azure/msal-angular';

@NgModule({
    declarations: [
        // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
        auth: {
            clientId: 'Enter_the_Application_Id_Here',
        },
        cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
        }
        }), {
            interactionType: InteractionType.Popup,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Popup,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
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

자동 토큰 획득을 성공 및 실패할 경우 MSAL Angular는 구독할 수 있는 이벤트를 제공합니다. 또한 구독을 취소해야 합니다.

```javascript
import { MsalBroadcastService } from '@azure/msal-angular';
import { EventMessage, EventType } from '@azure/msal-browser';

// In app.component.ts
export class AppComponent implements OnInit {
    private readonly _destroying$ = new Subject<void>();

    constructor(private broadcastService: MsalBroadcastService) { }

    ngOnInit() {
        this.broadcastService.msalSubject$
        .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.ACQUIRE_TOKEN_SUCCESS),
            takeUntil(this._destroying$)
        )
        .subscribe((result: EventMessage) => {
            // Do something with event payload here
        });
    }

    ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
    }
}
```

또는 핵심 MSAL.js 라이브러리의 설명대로 acquire-token 메서드를 사용하여 토큰을 명시적으로 획득할 수 있습니다.

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)
MSAL Angular 래퍼는 자동으로 액세스 토큰을 획득하고 API에 대한 HTTP 요청에 연결하는 HTTP 인터셉터를 제공합니다.
`protectedResourceMap` 구성 옵션에서 API 범위를 지정할 수 있습니다. `MsalInterceptor`는 자동으로 토큰을 획득할 경우 이러한 범위를 요청합니다.

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

자동 토큰 획득을 성공 및 실패할 경우 MSAL Angular는 구독할 수 있는 콜백을 제공합니다. 또한 구독을 취소해야 합니다.

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

또는 핵심 MSAL.js 라이브러리의 설명대로 acquire-token 메서드를 사용하여 토큰을 명시적으로 획득할 수 있습니다.

# <a name="react"></a>[React](#tab/react)

다음 코드에서는 앞에서 설명한 패턴과 팝업 환경의 메서드를 결합합니다.

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        if (!apiData && inProgress === InteractionStatus.None) {
            const accessTokenRequest = {
                scopes: ["user.read"],
                account: accounts[0]
            }
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
                        // Acquire token interactive success
                        let accessToken = accessTokenResponse.accessToken;
                        // Call your API with token
                        callApi(accessToken).then((response) => { setApiData(response) });
                    }).catch(function(error) {
                        // Acquire token interactive failure
                        console.log(error);
                    });
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

또는 React 구성 요소 외부에서 토큰을 획득해야 하는 경우 `acquireTokenSilent`를 호출할 수 있습니다. 하지만 실패한 경우 상호 작용으로 대체해서는 안 됩니다. 모든 상호 작용은 구성 요소 트리의 `MsalProvider` 구성 요소 아래에서 발생해야 합니다.

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="acquire-a-token-with-a-redirect"></a>리디렉션을 사용하여 토큰 획득

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

다음 패턴은 앞에서 설명되었지만 대화형으로 토큰을 획득하도록 리디렉션 메서드로 표시됩니다. 페이지 로드에서 `handleRedirectPromise`를 호출하고 기다려야 합니다.

```javascript
const redirectResponse = await publicClientApplication.handleRedirectPromise();
if (redirectResponse !== null) {
    // Acquire token silent success
    let accessToken = redirectResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
} else {
    // MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
    const account = publicClientApplication.getAllAccounts()[0];
    
    const accessTokenRequest = {
        scopes: ["user.read"],
        account: account
    }
    
    publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
        // Acquire token silent success
        // Call API with token
        let accessToken = accessTokenResponse.accessToken;
        // Call your API with token
        callApi(accessToken);
    }).catch(function (error) {
        //Acquire token silent failure, and send an interactive request
        console.log(error);
        if (error instanceof InteractionRequiredAuthError) {
            publicClientApplication.acquireTokenRedirect(accessTokenRequest);
        }
    });
}
```

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

다음 패턴은 앞에서 설명되었지만 대화형으로 토큰을 획득하도록 리디렉션 메서드로 표시됩니다. 앞에서의 설명대로 리디렉션 콜백을 등록해야 합니다.

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

선택적 클레임을 다음 목적으로 사용할 수 있습니다.

- 애플리케이션의 토큰에 추가 클레임을 포함합니다.
- Azure AD에서 토큰에 반환하는 특정 클레임의 동작을 변경합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

`IdToken`에서 선택적 클레임을 요청하려면 stringified 클레임 개체를 `AuthenticationParameters.ts` 클래스의 `claimsRequest` 필드로 전송하면 됩니다.

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

자세한 내용은 [선택적 클레임](active-directory-optional-claims.md)을 참조하세요.

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

이 코드는 앞에서의 설명과 동일하지만 리디렉션을 처리하도록 `MsalRedirectComponent`를 부트스트랩하는 것이 좋다는 점이 다릅니다. 리디렉션을 사용하도록 `MsalInterceptor` 구성을 변경할 수도 있습니다.

```javascript
// In app.module.ts
import { PublicClientApplication, InteractionType } from '@azure/msal-browser';
import { MsalInterceptor, MsalModule, MsalRedirectComponent } from '@azure/msal-angular';

@NgModule({
    declarations: [
      // ...
    ],
    imports: [
        // ...
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            },
            cache: {
                cacheLocation: 'localStorage',
                storeAuthStateInCookie: isIE,
            }
        }), {
            interactionType: InteractionType.Redirect,
            authRequest: {
                scopes: ['user.read']
            }
        }, {
            interactionType: InteractionType.Redirect,
            protectedResourceMap: new Map([ 
                ['https://graph.microsoft.com/v1.0/me', ['user.read']]
            ])
        })
    ],
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ],
    bootstrap: [AppComponent, MsalRedirectComponent]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)
이 코드는 앞에서의 설명과 동일합니다.

# <a name="react"></a>[React](#tab/react)

`acquireTokenSilent`가 실패하면 `acquireTokenRedirect`로 대체합니다. 이 메서드는 풀프레임 리디렉션을 초기화하고 응답은 애플리케이션에 반환될 때 처리됩니다. 이 구성 요소가 리디렉션에서 반환된 후 렌더링되면 `acquireTokenSilent`는 이제 캐시에서 토큰을 가져오므로 성공해야 합니다.

```javascript
import { InteractionRequiredAuthError, InteractionStatus } from "@azure/msal-browser";
import { AuthenticatedTemplate, useMsal } from "@azure/msal-react";

function ProtectedComponent() {
    const { instance, inProgress, accounts } = useMsal();
    const [apiData, setApiData] = useState(null);


    useEffect(() => {
        const accessTokenRequest = {
            scopes: ["user.read"],
            account: accounts[0]
        }
        if (!apiData && inProgress === InteractionStatus.None) {
            instance.acquireTokenSilent(accessTokenRequest).then((accessTokenResponse) => {
                // Acquire token silent success
                let accessToken = accessTokenResponse.accessToken;
                // Call your API with token
                callApi(accessToken).then((response) => { setApiData(response) });
            }).catch((error) => {
                if (error instanceof InteractionRequiredAuthError) {
                    instance.acquireTokenRedirect(accessTokenRequest);
                }
                console.log(error);
            })
        }
    }, [instance, accounts, inProgress, apiData]);

    return <p>Return your protected content here: {apiData}</p>
}

function App() {
    return (
        <AuthenticatedTemplate>
            <ProtectedComponent />
        </ AuthenticatedTemplate>
    )
}
```

또는 React 구성 요소 외부에서 토큰을 획득해야 하는 경우 `acquireTokenSilent`를 호출할 수 있습니다. 하지만 실패한 경우 상호 작용으로 대체해서는 안 됩니다. 모든 상호 작용은 구성 요소 트리의 `MsalProvider` 구성 요소 아래에서 발생해야 합니다.

```javascript
// MSAL.js v2 exposes several account APIs, logic to determine which account to use is the responsibility of the developer
const account = publicClientApplication.getAllAccounts()[0];

const accessTokenRequest = {
    scopes: ["user.read"],
    account: account
}

// Use the same publicClientApplication instance provided to MsalProvider
publicClientApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    let accessToken = accessTokenResponse.accessToken;
    // Call your API with token
    callApi(accessToken);
}).catch(function (error) {
    //Acquire token silent failure
    console.log(error);
});
```

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서 [웹 API 호출](scenario-spa-call-api.md)로 이동합니다.
