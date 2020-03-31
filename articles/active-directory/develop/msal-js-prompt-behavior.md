---
title: 대화형 요청 프롬프트 동작(MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: 자바스크립트용 Microsoft 인증 라이브러리(MSAL.js)를 사용하여 대화형 호출에서 프롬프트 동작을 사용자 지정하는 방법을 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695979"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>MSAL.js 대화형 요청의 프롬프트 동작

사용자가 여러 사용자 계정으로 활성 Azure AD 세션을 설정하면 Azure AD 로그인 페이지는 기본적으로 로그인을 진행하기 전에 계정을 선택하라는 메시지를 표시합니다. Azure AD를 가진 인증된 세션이 하나만 있는 경우 사용자는 계정 선택 환경을 볼 수 없습니다.

MSAL.js 라이브러리(v0.2.4에서 시작)는 대화형`loginRedirect`요청 `loginPopup` `acquireTokenRedirect` (및) `acquireTokenPopup`중에 프롬프트 매개 변수를 보내지 않으므로 프롬프트 동작을 적용하지 않습니다. 메서드를 사용하는 자동 `acquireTokenSilent` 토큰 요청의 경우 MSAL.js는 프롬프트 매개 변수를 로 설정합니다. `none`

응용 프로그램 시나리오에 따라 메서드에 전달된 요청 매개 변수에서 프롬프트 매개 변수를 설정하여 대화형 요청에 대한 프롬프트 동작을 제어할 수 있습니다. 예를 들어 계정 선택 환경을 호출하려면 다음을 수행합니다.

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Azure AD로 인증할 때 다음 프롬프트 값을 전달할 수 있습니다.

**로그인:** 이 값을 사용하면 사용자가 인증 요청에 자격 증명을 입력해야 합니다.

**select_account:** 이 값은 세션의 모든 계정을 나열하는 계정 선택 환경을 사용자에게 제공합니다.

**동의:** 이 값은 사용자가 앱에 권한을 부여할 수 있는 OAuth 동의 대화를 호출합니다.

**없음:** 이 값은 사용자에게 대화형 프롬프트가 표시되지 않도록 합니다. 예기치 않은 동작이 있을 수 있으므로 이 값을 MSAL.js의 대화형 메서드에 전달하지 않는 것이 좋습니다. 대신 이 `acquireTokenSilent` 메서드를 사용하여 자동 호출을 달성합니다.

## <a name="next-steps"></a>다음 단계

MSAL.js 라이브러리에서 `prompt` 사용하는 [OAuth 2.0 암시적 부여](v2-oauth2-implicit-grant-flow.md) 프로토콜의 매개 변수에 대해 자세히 읽어보십시오.
