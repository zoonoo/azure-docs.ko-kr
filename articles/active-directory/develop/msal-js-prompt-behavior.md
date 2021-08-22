---
title: 대화형 요청 프롬프트 동작(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft Authentication Library (MSAL.js)를 사용하여 대화형 호출에서 프롬프트 동작의 사용자 지정 방법을 알아보세요.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: f9f81bab52d60e38f07eae8ad8be217fd54082b4
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356566"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 대화형 요청의 프롬프트 동작

사용자가 여러 사용자 계정을 통해 활성화된 Azure AD 세션을 설정한 경우 Azure AD 로그인 페이지에 로그인하기 전 사용자에게 계정을 선택하라는 메시지가 표시됩니다. Azure AD에 인증된 세션이 하나만 있는 경우 사용자에게 계정 선택 환경이 표시되지 않습니다.

MSAL.js 라이브러리 (v 0.2.4에서 시작)는 대화형 요청 (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` 및 `acquireTokenPopup`)중에 프롬프트 매개 변수를 전송하지 않으므로 프롬프트 동작을 적용하지 않습니다. `acquireTokenSilent` 방법을 사용하는 자동 토큰 요청의 경우 MSAL.js는 `none`로 설정된 프롬프트 매개 변수를 전달합니다.

애플리케이션 시나리오에 따라 메서드에 전달된 요청 매개 변수에서 프롬프트 매개 변수를 설정하여 대화형 요청에 대한 프롬프트 동작을 제어할 수 있습니다. 예를 들어 계정 선택 환경을 호출하려면 다음을 수행합니다.

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Azure AD를 사용하여 인증하는 경우 다음 프롬프트 값을 전달할 수 있습니다.

**로그인:** 이 값은 사용자가 인증 요청에 대한 자격 증명을 입력하도록 합니다.

**select_account:** 이 값은 세션의 모든 계정을 나열하는 계정 선택 환경을 사용자에게 제공합니다.

**동의:** 이 값은 사용자가 앱에 권한을 부여할 수 있는 OAuth 동의 대화 상자를 호출합니다.

**없음:** 이 값을 사용하는 경우 사용자에게 대화형 프롬프트가 표시되지 않습니다. 예기치 않은 동작이 발생할 수 있으므로 MSAL.js의 대화형 메서드에 이 값을 전달하지 않는 것이 권장됩니다. 대신 `acquireTokenSilent` 메서드를 사용하여 자동 호출을 수행하세요.

## <a name="next-steps"></a>다음 단계

MSAL.js 라이브러리에서 사용하는 [OAuth 2.0 암시적 허용](v2-oauth2-implicit-grant-flow.md) 프로토콜의 `prompt`매개 변수에 대한 자세한 내용을 참조하세요.
