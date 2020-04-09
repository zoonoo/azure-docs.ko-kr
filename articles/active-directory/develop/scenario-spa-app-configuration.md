---
title: 단일 페이지 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램(앱의 코드 구성)을 빌드하는 방법 알아보기
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f159105046231ba5fb4e458cdd70d930a411a920
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882338"
---
# <a name="single-page-application-code-configuration"></a>단일 페이지 응용 프로그램: 코드 구성

단일 페이지 응용 프로그램(SPA)에 대한 코드를 구성하는 방법에 대해 알아봅니다.

## <a name="msal-libraries-that-support-implicit-flow"></a>암시적 흐름을 지원하는 MSAL 라이브러리

Microsoft ID 플랫폼은 업계에서 권장하는 보안 방법을 사용하여 암시적 흐름을 지원하기 위해 다음과 같은 MSAL(Microsoft 인증 라이브러리) 라이브러리를 제공합니다.

| MSAL 라이브러리 | Description |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 앵글러, Vue.js 및 React.js와 같은 자바 스크립트 또는 SPA 프레임 워크를 통해 구축 된 모든 클라이언트 측 웹 앱에서 사용하기 위한 일반 자바 스크립트 라이브러리. |
| ![MSAL 각도](media/sample-v2-code/logo_angular.png) <br/> [MSAL 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular 프레임워크를 통해 빌드된 단일 페이지 앱에서 사용을 단순화하기 위해 핵심 MSAL.js 라이브러리의 래퍼입니다. |

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에서 응용 프로그램 등록 정보는 라이브러리 초기화 중에 구성으로 전달됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

구성 가능한 옵션에 대한 자세한 내용은 [MSAL.js를 사용 하 여 응용 프로그램을 초기화](msal-js-initializing-client-applications.md)를 참조 하십시오.

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](scenario-spa-sign-in.md)
