---
title: 인증 요청에서 사용자 지정 상태 전달 (MSAL .js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft 인증 라이브러리 (MSAL)를 사용 하 여 인증 요청에서 사용자 지정 상태 매개 변수 값을 전달 하는 방법에 대해 알아봅니다.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4cb0f3d054f9afd0c606f80fd6fc5d553eff806
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916318"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL를 사용 하 여 인증 요청에서 사용자 지정 상태를 전달 합니다.
OAuth 2.0에 의해 정의 된 *상태* 매개 변수는 인증 요청에 포함 되며 교차 사이트 요청 위조 공격을 방지 하기 위해 토큰 응답에도 반환 됩니다. 기본적으로 JavaScript 용 Microsoft 인증 라이브러리 (MSAL .js)는 인증 요청에서 임의로 생성 된 고유한 *상태* 매개 변수 값을 전달 합니다.

State 매개 변수를 사용 하 여 리디렉션 전에 앱의 상태 정보를 인코딩할 수도 있습니다. 앱에서 사용자의 상태를이 매개 변수에 대 한 입력으로 전달할 수 있습니다 (예: 설정 된 페이지 또는 보기). MSAL .js 라이브러리를 사용 하면 `Request` 개체에서 사용자 지정 상태를 상태 매개 변수로 전달할 수 있습니다.

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
};
```

다음은 그 예입니다.

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

요청을 보낼 때 MSAL에 의해 설정 된 고유 GUID에 전달 된 상태가 추가 됩니다. 응답이 반환 되 면 MSAL는 상태 일치를 확인 한 다음 `Response` 개체의 상태에 전달 된 사용자 지정을 `accountState`로 반환 합니다.

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

자세히 알아보려면 MSAL를 사용 하 여 [단일 페이지 응용 프로그램 (SPA) 빌드](scenario-spa-overview.md) 를 참조 하세요.