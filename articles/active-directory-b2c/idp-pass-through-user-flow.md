---
title: 사용자 흐름을 통해 액세스 토큰을 응용 프로그램에 전달-Azure Active Directory B2C
description: Azure Active Directory B2C의 사용자 흐름에서 OAuth 2.0 id 공급자에 대 한 액세스 토큰을 클레임으로 전달 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a1a9ad0c615e50db746548a1ed5b557fe6fdea5a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064294"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 애플리케이션으로 액세스 토큰의 사용자 흐름 통과

Azure Active Directory B2C (Azure AD B2C) [사용자 흐름](active-directory-b2c-reference-policies.md) 은 응용 프로그램 사용자에 게 id 공급자를 등록 하거나 로그인 할 수 있는 기회를 제공 합니다. 이 과정이 시작되면 Azure AD B2C는 ID 공급자로부터 [액세스 토큰](active-directory-b2c-reference-tokens.md)을 받습니다. Azure AD B2C는 이 토큰을 사용하여 해당 사용자에 대한 정보를 검색합니다. 사용자는 Azure AD B2C에서 등록한 애플리케이션으로 토큰이 통과되도록 사용자 흐름에서 클레임을 활성화합니다.

Azure AD B2C는 현재 [Facebook](active-directory-b2c-setup-fb-app.md) 및 [Google](active-directory-b2c-setup-goog-app.md)이 포함된 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ID 공급자의 액세스 토큰 통과만 지원합니다. 다른 모든 ID 공급자에 대한 클레임은 빈 상태로 반환됩니다.

## <a name="prerequisites"></a>전제 조건

* 애플리케이션에서 [v2 사용자 흐름](user-flow-versions.md)을 사용하고 있어야 합니다.
* 사용자 흐름은 OAuth 2.0 ID 공급자를 사용하여 구성됩니다.

## <a name="enable-the-claim"></a>클레임 사용

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테 넌 트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 흐름 (정책)** 을 선택 하 고 사용자 흐름을 선택 합니다. 예를 들면 **B2C_1_signupsignin1**입니다.
5. **애플리케이션 클레임**을 선택합니다.
6. **Id 공급자 액세스 토큰** 클레임을 사용 하도록 설정 합니다.

    ![Id 공급자 액세스 토큰 클레임 사용](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. **저장**을 클릭하여 사용자 흐름을 저장합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토하기 위해 Azure AD B2C 토큰이 `https://jwt.ms`로 반환되도록 하는 것이 유용할 수 있습니다.

1. 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**의 경우 사용자가 이전에 등록한 애플리케이션을 선택합니다. 아래 예제에서 토큰을 보려면 **회신 URL**에서 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭한 다음, 계정 자격 증명으로 로그인합니다. **idp_access_token** 클레임에 ID 공급자의 액세스 토큰이 표시되어야 합니다.

    다음 예제와 비슷한 내용이 표시됩니다.

    ![Idp_access_token 블록이 강조 표시 된 jwt.ms의 디코딩된 토큰](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>다음 단계

[Azure AD B2C 토큰 개요](active-directory-b2c-reference-tokens.md)에서 자세히 알아보세요.
