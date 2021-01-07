---
title: Application Insights JavaScript SDK의 각도 플러그 인
description: Application Insights JavaScript SDK에 대해 각도 플러그 인을 설치 하 고 사용 하는 방법입니다.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843894"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK의 각도 플러그 인

Application Insights JavaScript SDK의 각도 플러그 인은 다음을 사용 합니다.

- 라우터 변경 내용 추적
- 각도 구성 요소 사용 통계

> [!WARNING]
> 각도 플러그 인은 ECMAScript 3 (ES3)과 호환 되지 않습니다.

## <a name="getting-started"></a>시작

Npm 패키지를 설치 합니다.

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>기본 사용법

앱의 항목 구성 요소에서 Application Insights의 인스턴스를 설정 합니다.

```js
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

메서드를 사용 하 여 `trackMetric` 각도 구성 요소 사용을 추적 하려면 `AngularPluginService` 파일의 공급자 목록에를 공급자로 추가 `app.module.ts` 합니다.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

구성 요소의 수명을 추적 하려면 `trackMetric` `ngOnDestroy` 해당 구성 요소의 메서드에서를 호출 합니다. 구성 요소가 제거 되 면 `trackMetric` 사용자가 페이지와 구성 요소 이름을 높게 유지 시간을 보내는 이벤트를 트리거합니다.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JAVASCRIPT sdk 설명서](javascript.md) 를 참조 하세요.
- [GitHub의 각도 플러그 인](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)