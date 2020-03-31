---
title: 페이지 다시 로드 방지(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 토큰을 자동으로 획득하고 갱신할 때 페이지 다시 로드를 방지하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084962"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>MSAL.js를 사용하여 토큰을 자동으로 획득하고 갱신할 때 페이지 재로드 방지
자바 스크립트에 대한 마이크로 소프트 인증 라이브러리 `iframe` (MSAL.js)는 숨겨진 요소를 사용하여 백그라운드에서 자동으로 토큰을 획득하고 갱신합니다. Azure AD는 토큰 요청에 지정된 등록된 redirect_uri 토큰을 반환합니다(기본적으로 앱의 루트 페이지). 응답은 302이기 때문에 에 로드되는 것에 `redirect_uri` 해당하는 HTML이 `iframe`생성됩니다. 일반적으로 `redirect_uri` 앱의 루트 페이지이며 이로 인해 다시 로드됩니다.

다른 경우에는 앱의 루트 페이지로 이동하여 인증이 필요한 경우 `iframe` 중첩된 `X-Frame-Options: deny` 요소 또는 오류가 발생할 수 있습니다.

MSAL.js는 Azure AD에서 발급한 302를 해제할 수 없으며 반환된 `redirect_uri` 토큰을 처리하는 `iframe`데 필요하므로 에서 로드되는 것을 방지할 수 없습니다.

전체 앱이 다시 로드되지 않도록 하거나 이로 인해 발생하는 기타 오류를 방지하려면 다음 해결 방법을 따르십시오.

## <a name="specify-different-html-for-the-iframe"></a>iframe에 대해 다른 HTML 지정

인증이 `redirect_uri` 필요하지 않은 간단한 페이지로 구성에 있는 속성을 설정합니다. Azure 포털에 `redirect_uri` 등록된 것과 일치하는지 확인해야 합니다. MSAL은 사용자가 로그인 프로세스를 시작하고 로그인이 완료된 후 정확한 위치로 다시 리디렉션될 때 시작 페이지를 저장하기 때문에 사용자의 로그인 경험에는 영향을 주지 않습니다.

## <a name="initialization-in-your-main-app-file"></a>기본 앱 파일의 초기화

앱의 초기화, 라우팅 및 기타 사항을 정의하는 중앙 Javascript 파일이 하나 있도록 앱이 구조화된 경우 앱이 `iframe` 로드되고 있는지 여부에 따라 앱 모듈을 조건부로 로드할 수 있습니다. 예를 들어:

앵글러JS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

각도: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

Msal 구성 요소:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>다음 단계
MSAL.js를 사용하여 [단일 페이지 응용 프로그램(SPA) 빌드에](scenario-spa-overview.md) 대해 자세히 알아봅니다.