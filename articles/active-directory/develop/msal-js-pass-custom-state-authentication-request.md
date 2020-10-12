---
title: 인증 요청에서 사용자 지정 상태 전달 (MSAL.js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft 인증 라이브러리 (MSAL.js)를 사용 하 여 인증 요청에서 사용자 지정 상태 매개 변수 값을 전달 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 840c371e63aacf8ef410cbf84cc9f68137dd77df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85477586"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js를 사용 하 여 인증 요청에서 사용자 지정 상태 전달

OAuth 2.0에 의해 정의 된 *상태* 매개 변수는 인증 요청에 포함 되며 교차 사이트 요청 위조 공격을 방지 하기 위해 토큰 응답에도 반환 됩니다. 기본적으로 JavaScript 용 Microsoft Authentication Library (MSAL.js)는 인증 요청에서 임의로 생성 된 고유한 *상태* 매개 변수 값을 전달 합니다.

State 매개 변수를 사용 하 여 리디렉션 전에 앱의 상태 정보를 인코딩할 수도 있습니다. 앱에서 사용자의 상태를이 매개 변수에 대 한 입력으로 전달할 수 있습니다 (예: 설정 된 페이지 또는 보기). MSAL.js 라이브러리를 사용 하면 개체에서 사용자 지정 상태를 상태 매개 변수로 전달할 수 있습니다 `Request` .

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
> 캐시 된 토큰을 건너뛰고 서버로 이동 하려면 `forceRefresh` 로그인/토큰 요청을 수행 하는 데 사용 되는 AuthenticationParameters 개체에 부울을 전달 하세요.
> `forceRefresh` 응용 프로그램의 성능에 미치는 영향 때문에 기본적으로를 사용 하면 안 됩니다.
> 캐시에 의존 하면 사용자에 게 더 나은 환경이 제공 됩니다.
> 캐시를 건너뛰는 것은 현재 캐시 된 데이터에 최신 정보가 없다는 것을 알고 있는 경우에만 사용 해야 합니다.
> 업데이트 된 역할이 있는 새 토큰을 가져와야 하는 사용자에 게 역할을 추가 하는 관리 도구입니다.

예를 들면 다음과 같습니다.

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

요청을 보낼 때 MSAL.js에 의해 설정 된 고유 GUID에 전달 된 상태가 추가 됩니다. 응답이 반환 되 면 MSAL.js에서 상태 일치를 확인 한 후 개체의 상태에 전달 된 사용자 지정를 `Response` 로 반환 합니다 `accountState` .

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

자세히 알아보려면 MSAL.js를 사용 하 여 [SPA (단일 페이지 응용 프로그램) 빌드](scenario-spa-overview.md) 를 참조 하세요.