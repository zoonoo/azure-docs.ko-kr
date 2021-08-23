---
title: 단일 페이지 앱 구성 | Azure
titleSuffix: Microsoft identity platform
description: 단일 페이지 애플리케이션을 빌드하는 방법 알아보기(앱의 코드 구성)
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: f049f49893b2ce1a895b9d175cbc308ee2868043
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357574"
---
# <a name="single-page-application-code-configuration"></a>단일 페이지 애플리케이션: 코드 구성

SPA(단일 페이지 애플리케이션)용 코드를 구성하는 방법을 알아봅니다.

## <a name="microsoft-libraries-supporting-single-page-apps"></a>단일 페이지 앱을 지원하는 Microsoft 라이브러리

다음 Microsoft 라이브러리는 단일 페이지 앱을 지원합니다.

[!INCLUDE [active-directory-develop-libraries-spa](../../../includes/active-directory-develop-libraries-spa.md)]

## <a name="application-code-configuration"></a>애플리케이션 코드 구성

MSAL 라이브러리에서 애플리케이션 등록 정보는 라이브러리 초기화 중에 구성으로 전달됩니다.

# <a name="javascript-msaljs-v2"></a>[JavaScript(MSAL.js v2)](#tab/javascript2)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new PublicClientApplication(config);
```

구성 가능한 옵션에 대한 자세한 내용은 [MSAL.js를 사용하여 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

# <a name="javascript-msaljs-v1"></a>[JavaScript(MSAL.js v1)](#tab/javascript1)

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

구성 가능한 옵션에 대한 자세한 내용은 [MSAL.js를 사용하여 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

# <a name="angular-msaljs-v2"></a>[Angular(MSAL.js v2)](#tab/angular2)

```javascript
// In app.module.ts
import { MsalModule } from '@azure/msal-angular';
import { PublicClientApplication } from '@azure/msal-browser';

@NgModule({
    imports: [
        MsalModule.forRoot( new PublicClientApplication({
            auth: {
                clientId: 'Enter_the_Application_Id_Here',
            }
        }), null, null)
    ]
})
export class AppModule { }
```

# <a name="angular-msaljs-v1"></a>[Angular(MSAL.js v1)](#tab/angular1)

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

# <a name="react"></a>[React](#tab/react)

```javascript
import { PublicClientApplication } from "@azure/msal-browser";
import { MsalProvider } from "@azure/msal-react";

// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create PublicClientApplication instance
const publicClientApplication = new PublicClientApplication(config);

// Wrap your app component tree in the MsalProvider component
ReactDOM.render(
    <React.StrictMode>
        <MsalProvider instance={publicClientApplication}>
            <App />
        </ MsalProvider>
    </React.StrictMode>,
    document.getElementById('root')
);
```

---

## <a name="next-steps"></a>다음 단계

이 시나리오의 다음 문서인 [로그인 및 로그아웃](scenario-spa-sign-in.md)으로 이동합니다.
