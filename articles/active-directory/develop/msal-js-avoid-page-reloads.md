---
title: 페이지 다시 로드 방지 (MSAL .js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft Authentication Library (MSAL)를 사용 하 여 토큰을 자동으로 획득 하 고 갱신할 때 페이지 다시 로드를 방지 하는 방법에 대해 알아봅니다.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e68798861d5799a4314bd9cd9b2eeeadb926a90f
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696149"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>MSAL를 사용 하 여 토큰을 자동으로 획득 하 고 갱신할 때 페이지를 다시 로드 하지 않습니다.
JavaScript 용 Microsoft Authentication Library (MSAL)는 숨겨진 `iframe` 요소를 사용 하 여 백그라운드에서 토큰을 자동으로 가져오고 갱신 합니다. Azure AD는 토큰을 토큰 요청에 지정 된 등록 된 redirect_uri으로 다시 반환 합니다 (기본적으로 앱의 루트 페이지). 응답이 302 이므로 `iframe`에서 로드 `redirect_uri`에 해당 하는 HTML이 생성 됩니다. 일반적으로 앱의 `redirect_uri`는 루트 페이지 이므로 다시 로드 됩니다.

다른 경우에는 응용 프로그램의 루트 페이지로 이동 하는 데 인증이 필요한 경우 중첩 된 `iframe` 요소나 `X-Frame-Options: deny` 오류가 발생할 수 있습니다.

MSAL는 Azure AD에서 발급 된 302를 해제할 수 없고 반환 된 토큰을 처리 하는 데 필요 하므로 `iframe`에서 `redirect_uri` 로드 되지 않도록 방지할 수 없습니다.

전체 앱 다시 로드를 다시 로드 하지 않거나이로 인해 발생 하는 다른 오류가 발생 하지 않도록 하려면 다음 해결 방법을 따르세요.

## <a name="specify-different-html-for-the-iframe"></a>Iframe에 대해 다른 HTML 지정

구성의 `redirect_uri` 속성을 인증이 필요 하지 않은 간단한 페이지로 설정 합니다. Azure Portal에 등록 된 `redirect_uri`와 일치 하는지 확인 해야 합니다. 이는 사용자의 로그인 환경에 영향을 주지 않습니다. MSAL은 사용자가 로그인 프로세스를 시작할 때 시작 페이지를 저장 하 고 로그인이 완료 된 후 정확한 위치로 다시 리디렉션됩니다.

## <a name="initialization-in-your-main-app-file"></a>주 앱 파일의 초기화

앱의 초기화, 라우팅 및 기타 항목을 정의 하는 중앙 Javascript 파일이 하나 있도록 앱이 구조화 된 경우 앱이 `iframe` 로드 되는지 여부에 따라 앱 모듈을 조건부로 로드할 수 있습니다. 예:

AngularJS: node.js

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

각도: app.config

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
MSAL를 사용 하 여 [SPA (단일 페이지 응용 프로그램)를 빌드하는](scenario-spa-overview.md) 방법에 대해 자세히 알아보세요.