---
title: 세션 동작 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 세션 동작을 구성 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed10a9258590f8f9037a574bd0322a82dd309a5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385249"
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
4. **사용자 흐름(정책)** 을 선택합니다.
5. 이전에 만든 사용자 흐름을 엽니다.
6. **속성**을 선택합니다.
7. **웹 앱 세션 수명 (분)**, **웹 앱 세션 시간 제한**, **Single sign-on 구성**을 구성 하 고 필요 **에 따라 로그 아웃 요청에 ID 토큰을 요구** 합니다.

    ![Azure Portal의 세션 동작 속성 설정](./media/session-behavior/session-behavior.png)

8. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 세션](session-overview.md)에 대해 자세히 알아보세요.