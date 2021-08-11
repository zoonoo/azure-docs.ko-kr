---
title: 인증 요청에서 사용자 지정 상태 전달(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 인증 요청에서 사용자 지정 state 매개 변수 값을 전달하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063674"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js를 사용하여 인증 요청에서 사용자 지정 상태 전달

OAuth 2.0에서 정의된 *state* 매개 변수는 인증 요청에 포함되며 교차 사이트 요청 위조 공격을 방지하기 위해 토큰 응답에서도 반환됩니다. 기본적으로 JavaScript용 Microsoft 인증 라이브러리(MSAL.js)는 인증 요청에서 임의로 생성된 고유 *state* 매개 변수 값을 전달합니다.

state 매개 변수를 사용하여 리디렉션 전에 앱 상태 정보를 인코딩할 수도 있습니다. 이 매개 변수에 대한 입력으로 앱의 사용자 상태(예: 사용자가 있었던 페이지 또는 보기)를 전달할 수 있습니다. MSAL.js 라이브러리를 사용하여 사용자 지정 상태를 `Request` 개체의 state 매개 변수로 전달할 수 있습니다.

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
> 캐시된 토큰을 건너뛰고 서버로 이동하려면 로그인/토큰 요청을 만드는 데 사용되는 AuthenticationParameters 개체에 부울 `forceRefresh`를 전달하세요.
> 애플리케이션 성능에 영향을 미치므로 `forceRefresh`는 기본적으로 사용하지 않아야 합니다.
> 캐시를 사용하면 사용자에게 더 나은 환경이 제공됩니다.
> 캐시 건너뛰기는 현재 캐시된 데이터에 최신 정보가 없음을 알고 있는 시나리오에서만 사용해야 합니다.
> 업데이트된 역할이 있는 새 토큰을 가져와야 하는 사용자에 역할을 추가하는 관리 도구와 같습니다.

예를 들면 다음과 같습니다.

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

전달된 상태는 요청을 보낼 때 MSAL.js에서 설정된 고유 GUID에 추가됩니다. 응답이 반환되면 MSAL.js는 상태 일치를 확인한 다음, 사용자 지정 전달된 상태를 `Response` 개체에서 `accountState`로 반환합니다.

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

자세한 내용은 MSAL.js를 사용하여 [SPA(단일 페이지 애플리케이션) 빌드](scenario-spa-overview.md)를 참조하세요.