---
title: 단일 페이지 응용 프로그램 (앱의 코드 구성)-Microsoft id 플랫폼
description: 단일 페이지 응용 프로그램 (앱의 코드 구성)을 빌드하는 방법을 알아봅니다
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
ms.openlocfilehash: 6236f0fa2400113225e04ffd4884cf743d1e250a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138281"
---
# <a name="single-page-application---code-configuration"></a>단일 페이지 응용 프로그램 코드 구성

단일 페이지 응용 프로그램 (SPA)에 대 한 코드를 구성 하는 방법에 알아봅니다.

## <a name="msal-libraries-supporting-implicit-flow"></a>암시적 흐름을 지 원하는 MSAL 라이브러리

Microsoft id 플랫폼 MSAL.js 라이브러리 업계를 사용 하 여 암시적 흐름을 지원 하기 위해 권장 되는 보안 정보를 제공 합니다.  

암시적 흐름을 지 원하는 라이브러리는 다음과 같습니다.

| MSAL 라이브러리 | 설명 |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Angular, Vue.js, React.js 등 JavaScript 또는 SPA 프레임 워크를 사용 하 여 작성 되는 모든 클라이언트 쪽 웹 앱에서 사용 하기 위해 일반 JavaScript 라이브러리입니다. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Angular 프레임 워크를 사용 하 여 만든 단일 페이지 앱의 사용을 단순화 하 고 핵심 MSAL.js 라이브러리의 래퍼. 이 라이브러리는 미리 보기로 제공 되었으며 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) 특정 Angular 버전 및 브라우저를 사용 하 여 합니다. |

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에 라이브러리를 초기화 하는 동안 구성으로 응용 프로그램 등록 정보를 전달 됩니다.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
사용할 수 있는 구성 가능한 옵션에 대 한 자세한 내용은 참조 하세요. [MSAL.js 사용 하 여 응용 프로그램 초기화](msal-js-initializing-client-applications.md)합니다.

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
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
