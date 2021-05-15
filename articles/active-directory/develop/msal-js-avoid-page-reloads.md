---
title: 페이지 다시 로드 방지(MSAL.js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript용 Microsoft Authentication Library(MSAL.js)를 사용하여 토큰을 자동으로 획득하고 갱신할 때 페이지가 다시 로드되지 않도록 방지하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0c96d161e55261af1bbe04eae6ead1d245158d02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064830"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>MSAL.js를 사용하여 토큰을 자동으로 획득 및 갱신할 때 페이지 다시 로드 방지
JavaScript용 Microsoft Authentication Library(MSAL.js)는 숨겨진 `iframe` 요소를 사용하여 백그라운드에서 토큰을 자동으로 가져오고 갱신합니다. Azure AD는 토큰을 토큰 요청에 지정된 등록 redirect_uri로 다시 반환합니다(기본적으로 앱의 루트 페이지임). 응답이 302이므로 `iframe`에서 로드되는 `redirect_uri`에 해당하는 HTML을 생성합니다. 일반적으로 앱의 `redirect_uri`가 루트 페이지이므로 다시 로드됩니다.

다른 경우에는 앱의 루트 페이지로 이동하여 인증이 필요한 경우 중첩된 `iframe` 요소 또는 `X-Frame-Options: deny` 오류가 발생할 수 있습니다.

MSAL.js는 Azure AD에서 발급된 302를 해제할 수 없고 반환된 토큰을 처리하는 데 필요하므로 `iframe`에서 `redirect_uri`가 로드되는 것을 방지할 수 없습니다.

전체 앱이 다시 로드되거나 이로 인해 발생하는 다른 오류를 방지하려면 다음 해결 방법을 따르십시오.

## <a name="specify-different-html-for-the-iframe"></a>iframe에 대해 다른 HTML 지정

구성에 대한 `redirect_uri` 속성을 인증할 필요가 없는 단순 페이지로 설정합니다. Azure Portal에 등록된 `redirect_uri`와 일치하는지 확인해야 합니다. 사용자가 로그인 프로세스를 시작할 때 MSAL이 시작 페이지를 저장하고 로그인이 완료된 후 다시 정확한 위치로 리디렉션하므로 사용자의 로그인 환경에 영향을 미치지 않습니다.

## <a name="initialization-in-your-main-app-file"></a>주 앱 파일의 초기화

앱의 초기화, 라우팅 및 기타 항목을 정의하는 하나의 중앙 Javascript 파일이 있도록 앱이 구조화된 경우 앱이 `iframe`에서 로드되는지 여부에 따라 앱 모듈을 조건부로 로드할 수 있습니다. 예를 들면 다음과 같습니다.

AngularJS: app.js의 경우

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

Angular: app.module.ts의 경우

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
MSAL.js를 사용하여 [SPA(단일 페이지 애플리케이션)를 빌드하는](scenario-spa-overview.md) 방법에 대해 자세히 알아보세요.