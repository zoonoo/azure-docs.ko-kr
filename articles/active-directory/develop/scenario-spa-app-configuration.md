---
title: 단일 페이지 앱 구성 - Microsoft ID 플랫폼 | Azure
description: 단일 페이지 응용 프로그램(앱의 코드 구성)을 빌드하는 방법 알아보기
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160086"
---
# <a name="single-page-application-code-configuration"></a>단일 페이지 응용 프로그램: 코드 구성

단일 페이지 응용 프로그램(SPA)에 대한 코드를 구성하는 방법에 대해 알아봅니다.

## <a name="msal-libraries-that-support-implicit-flow"></a>암시적 흐름을 지원하는 MSAL 라이브러리

Microsoft ID 플랫폼은 업계에서 권장하는 보안 방법을 사용하여 암시적 흐름을 지원하기 위해 다음과 같은 MSAL(Microsoft 인증 라이브러리) 라이브러리를 제공합니다.  

| MSAL 라이브러리 | 설명 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 앵글러, Vue.js 및 React.js와 같은 자바 스크립트 또는 SPA 프레임 워크를 통해 구축 된 모든 클라이언트 측 웹 앱에서 사용하기 위한 일반 자바 스크립트 라이브러리. |
| ![MSAL 각도](media/sample-v2-code/logo_angular.png) <br/> [MSAL 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular 프레임워크를 통해 빌드된 단일 페이지 앱에서 사용을 단순화하기 위해 핵심 MSAL.js 라이브러리의 래퍼입니다. 이 라이브러리는 미리 보기 중이며 특정 각도 버전 및 브라우저에 [문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) 있습니다. |

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에서 응용 프로그램 등록 정보는 라이브러리 초기화 중에 구성으로 전달됩니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

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
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그인 및 로그아웃](scenario-spa-sign-in.md)
