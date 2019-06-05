---
title: 인증 요청 (JavaScript 용 Microsoft Authentication Library)에서 사용자 지정 상태 전달 | Azure
description: JavaScript (MSAL.js) 용 Microsoft Authentication Library를 사용 하 여 인증 요청에 사용자 지정 상태 매개 변수 값을 전달 하는 방법에 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f29d84838ddb11ac359d7a04dbce8e39dd05ac01
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420500"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>MSAL.js를 사용 하 여 인증 요청에서 사용자 지정 상태 전달
합니다 *상태* 매개 변수를 OAuth 2.0에서 정의 된 인증 요청에 포함 되 고 교차 사이트 요청 위조 공격을 방지 하기 위해 토큰 응답에 반환 됩니다. 기본적으로 JavaScript (MSAL.js) 용 Microsoft Authentication Library에서는 임의로 생성 된 고유한 *상태* 인증 요청에서 매개 변수 값입니다.

또한 상태 매개 변수 이동 하기 전에 앱의 상태 정보를 인코딩하는 데 사용할 수 있습니다. 이 매개 변수 입력으로에 있었던 페이지 또는 보기와 같은 앱에서 사용자의 상태를 전달할 수 있습니다. MSAL.js 라이브러리를 사용 하 여 사용자 지정 상태에서 상태 매개 변수로 전달할 수는 `Request` 개체:

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

예를 들면 다음과 같습니다.

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: “page_url”
}

myMSALObj.loginPopup(loginRequest);
```

전달 된 상태는 요청을 보내는 동안 MSAL.js 설정한 고유 GUID로 추가 됩니다. 응답이 반환 됩니다 MSAL.js 상태 일치 항목을 검사 하 고 다음 전달 된 상태에서 사용자 지정을 반환 합니다 `Response` 개체 `accountState`합니다.

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

읽어 자세히 알아보려면 [단일 페이지 응용 프로그램 (SPA) 빌드](scenario-spa-overview.md) MSAL.js를 사용 하 여 합니다.