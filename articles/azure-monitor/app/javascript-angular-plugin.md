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
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737021"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK의 각도 플러그 인

Application Insights JavaScript SDK의 각도 플러그 인은 다음을 사용 합니다.

- 라우터 변경 내용 추적

> [!WARNING]
> 각도 플러그 인은 ECMAScript 3 (ES3)과 호환 되지 않습니다.

## <a name="getting-started"></a>시작

Npm 패키지를 설치 합니다.

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>기본적인 사용 방법

앱의 항목 구성 요소에서 Application Insights의 인스턴스를 설정 합니다.

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JAVASCRIPT sdk 설명서](javascript.md) 를 참조 하세요.
- [GitHub의 각도 플러그 인](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
