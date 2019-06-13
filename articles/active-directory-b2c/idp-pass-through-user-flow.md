---
title: 응용 프로그램에 Azure Active Directory B2C 사용자 흐름을 통해 액세스 토큰을 전달 합니다. | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 흐름의 클레임으로 OAuth2.0 ID 공급자에 대한 액세스 토큰을 통과하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: db4b799aa31a4132609b0dd158b65070fb2474b7
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510960"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 애플리케이션으로 액세스 토큰의 사용자 흐름 통과

> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공됩니다.

Azure AD(Azure Active Directory) B2C 의 [사용자 흐름](active-directory-b2c-reference-policies.md)은 애플리케이션 사용자에게 ID 공급자를 등록하거나 로그인할 수 있는 기회를 제공합니다. 이 과정이 시작되면 Azure AD B2C는 ID 공급자로부터 [액세스 토큰](active-directory-b2c-reference-tokens.md)을 받습니다. Azure AD B2C는 이 토큰을 사용하여 해당 사용자에 대한 정보를 검색합니다. 사용자는 Azure AD B2C에서 등록한 애플리케이션으로 토큰이 통과되도록 사용자 흐름에서 클레임을 활성화합니다.

Azure AD B2C는 현재 [Facebook](active-directory-b2c-setup-fb-app.md) 및 [Google](active-directory-b2c-setup-goog-app.md)이 포함된 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) ID 공급자의 액세스 토큰 통과만 지원합니다. 다른 모든 ID 공급자에 대한 클레임은 빈 상태로 반환됩니다.

## <a name="prerequisites"></a>필수 조건

- 애플리케이션에서 [v2 사용자 흐름](user-flow-versions.md)을 사용하고 있어야 합니다.
- 사용자 흐름은 OAuth 2.0 ID 공급자를 사용하여 구성됩니다.

## <a name="enable-the-claim"></a>클레임 사용

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테 넌 트를 포함 하는 디렉터리 사용 했는지 확인 합니다. 선택 된 **디렉터리 및 구독 필터** 최상위 메뉴에서 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 선택 **사용자 흐름 (정책)** , 한 다음 사용자 흐름을 선택 합니다. 예를 들어 **B2C_1_signupsignin1**합니다.
5. **애플리케이션 클레임**을 선택합니다.
6. 사용 하도록 설정 합니다 **Identity 공급자 액세스 토큰** 클레임입니다.

    ![Identity Provider 액세스 토큰 클레임을 사용 하도록 설정](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. **저장**을 클릭하여 사용자 흐름을 저장합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토하기 위해 Azure AD B2C 토큰이 `https://jwt.ms`로 반환되도록 하는 것이 유용할 수 있습니다.

1. 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**의 경우 사용자가 이전에 등록한 애플리케이션을 선택합니다. 아래 예제에서 토큰을 보려면 **회신 URL**에서 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭한 다음, 계정 자격 증명으로 로그인합니다. **idp_access_token** 클레임에 ID 공급자의 액세스 토큰이 표시되어야 합니다.

    다음 예제와 비슷한 내용이 표시됩니다.

    ![디코딩된 토큰](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>다음 단계

자세한 내용을 보려면 합니다 [Azure AD B2C 토큰 개요](active-directory-b2c-reference-tokens.md)합니다.




