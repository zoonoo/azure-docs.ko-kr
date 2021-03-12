---
title: 단일 페이지 앱 구성 | Microsoft
titleSuffix: Microsoft identity platform
description: 단일 페이지 응용 프로그램을 빌드하는 방법 알아보기 (앱 코드 구성)
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
ms.openlocfilehash: fe73832ec5eaee62a2dc2d397c12f82334e2efd8
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103010708"
---
# <a name="single-page-application-code-configuration"></a>단일 페이지 응용 프로그램: 코드 구성

SPA (단일 페이지 응용 프로그램)에 대 한 코드를 구성 하는 방법에 대해 알아봅니다.

## <a name="microsoft-libraries-supporting-single-page-apps"></a>단일 페이지 앱을 지 원하는 Microsoft 라이브러리 

다음 Microsoft 라이브러리는 단일 페이지 앱을 지원 합니다.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>응용 프로그램 코드 구성

MSAL 라이브러리에서 응용 프로그램 등록 정보는 라이브러리 초기화 중에 구성으로 전달 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

구성 가능한 옵션에 대 한 자세한 내용은 [MSAL.js를 사용 하 여 응용 프로그램 초기화 ](msal-js-initializing-client-applications.md)를 참조 하세요.

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

이 시나리오에서 다음 문서로 이동 하 여 로그인 [및 로그 아웃](scenario-spa-sign-in.md)합니다.
