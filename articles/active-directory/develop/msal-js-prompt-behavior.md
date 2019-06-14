---
title: 프롬프트 동작 대화형 요청 (Microsoft Authentication Library for JavaScript) | Azure
description: JavaScript (MSAL.js) 용 Microsoft Authentication Library를 사용 하 여 대화형 호출에서 메시지 표시 동작을 사용자 지정 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544263"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 대화형 요청에서 프롬프트 동작

사용자 설정에 현재 Azure AD 세션 여러 사용자 계정으로 하는 경우 Azure AD 로그인 페이지는 기본적으로 사용자에 게 표시에 로그인 하기 전에 계정을 선택 합니다. 사용자는 경우 Azure AD 사용 하 여 단일 인증 된 세션 에서만 발생 하는 계정 선택을 표시 되지 않습니다.

MSAL.js 라이브러리 (v0.2.4부터) 대화형 요청 중을 프롬프트 매개 변수를 보내지 않습니다 (`loginRedirect`, `loginPopup`를 `acquireTokenRedirect` 및 `acquireTokenPopup`), 및 있으므로 모든 메시지 표시 동작을 적용 하지 않습니다. 사용 하 여 자동 토큰 요청에 대 한 합니다 `acquireTokenSilent` 메서드를 MSAL.js로 프롬프트 매개 변수를 전달 `none`합니다.

응용 프로그램 시나리오에 따라 설정 하 여 대화형 요청 프롬프트 매개 변수 요청 매개 변수를 메서드에 전달에 대 한 메시지 표시 동작을 제어할 수 있습니다. 예를 들어 계정 선택 환경을 호출 하려는 경우:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Azure AD로 인증 하는 경우에 다음 프롬프트 값을 전달할 수 있습니다.

**login:** 이 값에 인증 요청에 자격 증명을 입력 하도록 사용자에 게를 강제 됩니다.

**select_account:** 이 값은 세션에서 모든 계정을 나열 하는 계정 선택 환경을 사용 하 여 사용자를 제공 합니다.

**consent:** 이 값은 사용자가 앱에 권한을 부여할 수 있도록 OAuth 동의 대화 상자를 호출 합니다.

**None:** 이 값은 사용자 어떠한 대화형 프롬프트를 표시 하지 않습니다 확인 합니다. 했을 수와 MSAL.js의 대화형 메서드에이 값을 전달 하지 않는 것이 좋습니다 예기치 않은 동작이 있습니다. 대신는 `acquireTokenSilent` 자동 호출을 수행 하는 방법입니다.

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 합니다 `prompt` 의 매개 변수를 [OAuth 2.0 암시적 허용](v2-oauth2-implicit-grant-flow.md) MSAL.js 라이브러리를 사용 하는 프로토콜입니다.
