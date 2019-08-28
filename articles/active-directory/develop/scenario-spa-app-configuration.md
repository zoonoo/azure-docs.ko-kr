---
title: 단일 페이지 응용 프로그램 (앱의 코드 구성)-Microsoft identity platform
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (앱 코드 구성)
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
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891526"
---
# <a name="single-page-application---code-configuration"></a>단일 페이지 응용 프로그램-코드 구성

SPA (단일 페이지 응용 프로그램)에 대 한 코드를 구성 하는 방법에 대해 알아봅니다.

## <a name="msal-libraries-supporting-implicit-flow"></a>암시적 흐름을 지 원하는 MSAL 라이브러리

Microsoft id 플랫폼은 업계 권장 보안 관행을 사용 하 여 암시적 흐름을 지원 하기 위해 MSAL .js 라이브러리를 제공 합니다.  

암시적 흐름을 지 원하는 라이브러리는 다음과 같습니다.

| MSAL 라이브러리 | Description |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | 모든 클라이언트 쪽 웹 앱에서 사용 되는 일반 JavaScript 라이브러리는 각도, Vue, 지 문의 등 JavaScript 또는 SPA 프레임 워크를 사용 하 여 빌드된 모든 클라이언트 쪽 웹 앱에서 사용 됩니다. |
| ![MSAL 각도](media/sample-v2-code/logo_angular.png) <br/> [MSAL 각도](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | 각도 프레임 워크를 사용 하 여 작성 된 단일 페이지 앱에서 사용을 간소화 하기 위한 핵심 MSAL .js 라이브러리의 래퍼입니다. 이 라이브러리는 미리 보기 상태 이며 특정 각도 버전 및 브라우저와 관련 된 [알려진 문제가](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) 있습니다. |

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에서 응용 프로그램 등록 정보는 라이브러리 초기화 중에 구성으로 전달 됩니다.

### <a name="javascript"></a>JavaScript

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
사용 가능한 구성 가능한 옵션에 대 한 자세한 내용은 [MSAL를 사용 하 여 응용 프로그램 초기화](msal-js-initializing-client-applications.md)를 참조 하세요.

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로그인 및 로그 아웃](scenario-spa-sign-in.md)
