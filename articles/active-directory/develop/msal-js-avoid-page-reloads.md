---
title: 페이지 다시 로드 (JavaScript 용 Microsoft Authentication Library) 방지 | Azure
description: 가져올 때 페이지를 다시 로드를 방지 하는 방법 및 자동으로 Microsoft 인증 라이브러리를 사용 하 여 JavaScript (MSAL.js)에 대 한 갱신 토큰에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420470"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>가져올 때 및 MSAL.js를 사용 하 여 자동으로 갱신 토큰 페이지를 다시 로드 방지
Microsoft Authentication Library for JavaScript (MSAL.js) 사용 하 여 숨겨진 `iframe` 획득 백그라운드에서 자동으로 토큰을 갱신 하는 요소입니다. Azure AD 토큰 요청에 지정 된 등록 된 redirect_uri 돌아가기 토큰을 반환 합니다 (기본적으로이 앱의 루트 페이지). 302 응답 이므로 그 결과 해당 하는 HTML 합니다 `redirect_uri` 에 로드 되는 `iframe`합니다. 일반적으로 앱의 `redirect_uri` 루트 페이지 이며이 인해 다시 로드 합니다.

다른 경우에 인증이 필요한 앱의 루트에 대 한 페이지로 이동 하는 경우 될 수 있습니다에 중첩 `iframe` 요소 또는 `X-Frame-Options: deny` 오류입니다.

MSAL.js Azure AD에서 발급 한 302를 해제할 수 없습니다 하 고 반환된 된 토큰을 처리 하는 데 필요한를 막을 수 없습니다는 `redirect_uri` 에 로드 하기는 `iframe`합니다.

전체 앱 다시 다시 로드 하거나이 인해 발생 한 다른 오류를 방지 하려면 이러한 해결 방법을 따르세요.

## <a name="specify-different-html-for-the-iframe"></a>Iframe에 대 한 다른 HTML 지정

설정 된 `redirect_uri` 인증 필요 하지 않은 간단한 페이지로 구성의 속성입니다. 와 일치 하는지 확인 해야 합니다 `redirect_uri` Azure 포털에 등록 합니다. MSAL 사용자 로그인 프로세스를 시작 하 고 로그인이 완료 된 후 정확한 위치를 다시 리디렉션합니다 때 시작 페이지를 저장 하는 대로 사용자의 로그인 환경이 되지 영향이 수 있습니다.

## <a name="initialization-in-your-main-app-file"></a>기본 앱 파일에서 초기화

앱의 앱의 초기화, 라우팅 및 기타 자료를 정의 하는 하나의 중앙 Javascript 파일이 되도록 구성 하는 경우 앱 모듈 앱에서 로드 되는 여부에 따라 조건부로 로드할 수 있습니다는 `iframe` 여부입니다. 예를 들면 다음과 같습니다.

AngularJS에서: app.js

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

Angular에서: app.module.ts

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

MsalComponent:

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
에 대해 자세히 알아보세요 [단일 페이지 응용 프로그램 (SPA) 빌드](scenario-spa-overview.md) MSAL.js를 사용 하 여 합니다.