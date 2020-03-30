---
title: 인증 요청에서 사용자 지정 상태 전달(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 인증 요청에서 사용자 지정 상태 매개 변수 값을 전달하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084943"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js를 사용하여 인증 요청에서 사용자 지정 상태 전달

OAuth 2.0에 정의된 *상태* 매개 변수는 인증 요청에 포함되며 사이트 간 요청 위조 공격을 방지하기 위해 토큰 응답에도 반환됩니다. 기본적으로 자바스크립트용 마이크로소프트 인증 라이브러리(MSAL.js)는 인증 요청에서 임의로 생성된 고유 *상태* 매개 변수 값을 전달합니다.

상태 매개 변수는 리디렉션하기 전에 앱 상태의 정보를 인코딩하는 데 사용할 수도 있습니다. 이 매개 변수에 대한 입력으로 앱에서 사용자의 상태를 전달할 수 있습니다. MSAL.js 라이브러리를 사용하면 개체의 상태 매개 변수로 사용자 지정 상태를 전달할 수 있습니다. `Request`

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> 캐시된 토큰을 건너뛰고 서버로 이동하려면 부울을 `forceRefresh` 로그인/토큰 요청을 하는 데 사용되는 AuthenticationParameters 개체로 전달하십시오.
> `forceRefresh`응용 프로그램에 미치는 성능 영향 때문에 기본적으로 사용해서는 안 됩니다.
> 캐시에 의존하면 사용자에게 더 나은 환경을 제공할 수 있습니다.
> 캐시를 건너뛰는 것은 현재 캐시된 데이터에 최신 정보가 없는 경우에만 사용해야 합니다.
> 업데이트된 역할이 있는 새 토큰을 받아야 하는 사용자에게 역할을 추가하는 관리 도구와 같은 것입니다.

예를 들어:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

전달된 상태는 요청을 보낼 때 MSAL.js가 설정한 고유 GUID에 추가됩니다. 응답이 반환되면 MSAL.js는 상태 일치를 확인한 다음 `Response` 개체에서 전달된 `accountState`사용자 지정을 로 반환합니다.

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

자세한 내용은 MSAL.js를 사용하여 [단일 페이지 응용 프로그램(SPA) 빌드에](scenario-spa-overview.md) 대해 읽어보십시오.