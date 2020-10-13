---
title: 세션 동작 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 세션 동작을 구성 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961037"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 세션 동작 구성

Azure Active Directory B2C (Azure AD B2C)의 [sso (Single sign-on) 세션](session-overview.md) 관리를 통해 관리자는 사용자가 이미 인증 된 후 사용자와의 상호 작용을 제어할 수 있습니다. 예를 들어 관리자는 id 공급자의 선택 여부 또는 계정 세부 정보를 다시 입력 해야 하는지 여부를 제어할 수 있습니다. 이 문서에서는 Azure AD B2C에 대한 SSO 설정을 구성하는 방법을 설명합니다.

## <a name="session-behavior-properties"></a>세션 동작 속성

다음 속성을 사용하여 웹 애플리케이션 세션을 관리할 수 있습니다.

- **웹앱 세션 수명(분)** - 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
    - 기본값: 1440분.
    - 최소(포함) = 15분.
    - 최대(포함) = 1440분.
- **웹 앱 세션 제한 시간** - [세션 만료 유형](session-overview.md#session-expiry-type), *롤링*또는 *절대*입니다. 
- **Single sign-on 구성** -Azure AD B2C 테 넌 트의 여러 앱 및 사용자 흐름에 걸친 SSO (Single Sign-On) 동작의 [세션 범위](session-overview.md#session-scope) 입니다.


## <a name="configure-the-properties"></a>속성 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 흐름**을 선택합니다.
5. 이전에 만든 사용자 흐름을 엽니다.
6. **속성**을 선택합니다.
7. **웹 앱 세션 수명 (분)**, **웹 앱 세션 시간 제한**, **Single sign-on 구성**을 구성 하 고 필요 **에 따라 로그 아웃 요청에 ID 토큰을 요구** 합니다.

    ![Azure Portal의 세션 동작 속성 설정](./media/session-behavior/session-behavior.png)

8. **Save**을 클릭합니다.

## <a name="configure-sign-out-behavior"></a>로그 아웃 동작 구성

### <a name="secure-your-logout-redirect"></a>로그 아웃 리디렉션 보안

로그 아웃 한 후에는 `post_logout_redirect_uri` 응용 프로그램에 대해 지정 된 회신 url에 관계 없이 사용자가 매개 변수에 지정 된 URI로 리디렉션됩니다. 그러나 유효한를 `id_token_hint` 전달 하 고 **로그 아웃 요청에 ID 토큰 필요** 를 설정 하는 경우 Azure AD B2C는 `post_logout_redirect_uri` 리디렉션을 수행 하기 전에의 값이 응용 프로그램의 구성 된 리디렉션 uri 중 하 나와 일치 하는지 확인 합니다. 응용 프로그램에 대해 일치 하는 회신 URL이 구성 되지 않은 경우 오류 메시지가 표시 되 고 사용자가 리디렉션되지 않습니다. 로그 아웃 요청에 ID 토큰을 요구 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **사용자 흐름**을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성**을 선택합니다.
1. **로그 아웃 요청에 ID 토큰 필요**를 사용 하도록 설정 합니다.
1. **Azure AD B2C**으로 돌아갑니다.
1. **앱 등록**를 선택 하 고 응용 프로그램을 선택 합니다.
1. **인증**을 선택합니다.
1. **로그 아웃 URL** 텍스트 상자에 사후 로그 아웃 리디렉션 URI를 입력 하 고 **저장**을 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-overview.md)에 대해 자세히 알아보세요.
