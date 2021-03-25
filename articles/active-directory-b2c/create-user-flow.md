---
title: 사용자 흐름 만들기 - Azure Active Directory B2C
description: Azure Active Directory B2C에서 애플리케이션에 대한 가입, 로그인 및 사용자 프로필 편집을 사용하도록 설정하는 사용자 흐름을 Azure Portal에서 만드는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bd436b972dfb1549232831b1f07c3726ff459dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556493"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 흐름 만들기

Azure AD B2C(Azure Active Directory B2C) 테넌트에서 다양한 유형의 [사용자 흐름](user-flow-overview.md)을 만들고, 필요에 따라 애플리케이션에서 이를 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다.

> [!IMPORTANT]
> 사용자 흐름 버전을 참조하는 방법이 변경되었습니다. 이전에는 V1(프로덕션 준비) 버전과 V1.1 및 V2(미리 보기) 버전을 제공했습니다. 이제 사용자 흐름은 **추천**(차세대 미리 보기) 및 **표준**(일반 공급) 버전으로 통합되었습니다. 모든 V1.1 및 V2 레거시 미리 보기 사용자 흐름은 **2021년 8월 1일** 에 사용 중단될 예정입니다. 자세한 내용은 [Azure AD B2C의 사용자 흐름 버전](user-flow-versions.md)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

- 새 사용자 흐름을 테스트하는 데 사용하려는 **애플리케이션을 등록** 합니다. 예제를 보려면 [자습서: Azure AD B2C에서 웹 애플리케이션 등록](tutorial-register-applications.md)을 참조하세요.
- 공급자(예: Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft 또는 Twitter)를 사용하여 사용자 로그인을 사용하도록 설정하려면 **외부 ID 공급자를 추가** 합니다. [Azure AD B2C의 애플리케이션에 ID 공급자 추가](add-identity-provider.md)를 참조하세요.
- 테넌트의 로컬 계정에 지원하려는 ID 유형(이메일, 사용자 이름, 전화 번호)을 지정하려면 **로컬 계정 ID 공급자를 구성** 합니다. 그런 다음, 개별 사용자 흐름을 만들 때 지원되는 이러한 ID 유형 중에서 선택할 수 있습니다. 사용자가 사용자 흐름을 완료하면 Azure AD B2C 디렉터리에 로컬 계정이 만들어지고 **로컬 계정** ID 공급자가 사용자의 정보를 인증합니다. 다음 단계를 사용하여 테넌트의 로컬 계정 ID 공급자를 구성합니다.

   1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 
   2. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
   3. Azure Portal 위쪽의 검색 창에서 **Azure AD B2C** 를 검색하여 선택합니다.
   4. **관리** 아래에서 **ID 공급자** 를 선택합니다.
   5. ID 공급자 목록에서 **로컬 계정** 을 선택합니다.
   6. **로컬 IDP 구성** 페이지에서 지원하려는 모든 ID 유형을 선택합니다. 여기서 옵션을 선택하면 나중에 만드는 사용자 흐름에 사용할 수 있습니다.
      - **전화**(미리 보기): 사용자가 가입 시 확인되고 사용자 ID가 되는 전화 번호를 입력할 수 있습니다.
      - **이메일**(기본값): 사용자가 가입 시 확인되고 사용자 ID가 되는 이메일 주소를 입력할 수 있습니다.
      - **사용자 이름**: 사용자가 고유한 사용자 ID를 만들 수 있습니다. 이메일 주소는 사용자로부터 수집되어 확인됩니다.
    7. **저장** 을 선택합니다.

## <a name="create-a-user-flow"></a>사용자 흐름 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

    ![B2C 테넌트, 디렉터리 및 구독 창, Azure Portal](./media/create-user-flow/directory-subscription-pane.png)

3. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
4. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.

    ![새 사용자 흐름 단추가 강조 표시된 포털의 사용자 흐름 페이지](./media/create-user-flow/signup-signin-user-flow.png)

5. **사용자 흐름 만들기** 페이지에서 만들려는 사용자 흐름 유형을 선택합니다(개요는 [Azure AD B2C의 사용자 흐름](user-flow-overview.md) 참조).

    ![가입 및 로그인 흐름이 강조 표시된 사용자 흐름 페이지 선택](./media/create-user-flow/select-user-flow-type.png)

6. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)

    ![속성이 강조 표시된 Azure Portal의 사용자 흐름 만들기 페이지](./media/create-user-flow/select-version.png)

7. 사용자 흐름에 대한 **이름** 을 입력합니다(예: *signupsignin1*, *profileediting1*, *passwordreset1*).
8. **ID 공급자** 아래에서 만들고 있는 사용자 흐름 유형에 따라 다음 옵션을 선택합니다.

   - **로컬 계정**. 사용자가 Azure AD B2C 테넌트에서 로컬 계정을 만들 수 있도록 하려면 사용할 식별자 유형(예: 이메일, 사용자 ID 또는 전화)을 선택합니다. [로컬 계정 ID 공급자](#before-you-begin) 설정에 구성된 ID 유형만 나열됩니다.

   - **소셜 ID 공급자**. [추가한 소셜 ID 공급자](add-identity-provider.md)(예: Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft 또는 Twitter)를 사용하여 사용자 로그인을 허용하려면 목록에서 공급자를 선택합니다.

9. **사용자 특성 및 클레임** 에 대해 수집한 후 등록 동안 사용자로부터 전송하려는 클레임 및 특성을 선택합니다. **자세히 보기** 를 선택합니다. 특성 및 클레임을 선택한 다음, **확인** 을 선택합니다.

    ![세 개의 클레임이 선택된 특성 및 클레임 선택 페이지](./media/create-user-flow/signup-signin-attributes.png)

10. **만들기** 를 선택하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. **정책** > **사용자 흐름** 을 차례로 선택한 다음, 만든 사용자 흐름을 선택합니다. 사용자 흐름 개요 페이지에서 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 에 대해 1단계에서 등록한 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
2. 테스트하는 사용자 흐름 유형에 따라 유효한 이메일 주소를 사용하여 가입하고 가입 흐름을 따르거나, 이전에 만든 계정을 사용하여 로그인합니다.

    ![사용자 흐름 실행 단추가 강조 표시된 포털의 사용자 흐름 실행 페이지](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. 사용자 흐름 프롬프트를 따릅니다. 사용자 흐름을 완료하면 토큰이 `https://jwt.ms`에 반환되고 사용자에게 표시됩니다.

> [!NOTE]
> "사용자 흐름 실행" 환경은 현재 인증 코드 흐름을 사용하는 SPA 회신 URL 유형과 호환되지 않습니다. 이러한 종류의 앱에서 "사용자 흐름 실행" 환경을 사용하려면 "웹" 유형의 회신 URL을 등록하고 [여기](tutorial-register-spa.md)에 설명된 대로 암시적 흐름을 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 사용자 흐름에 조건부 액세스 추가](conditional-access-user-flow.md)
- [Azure AD B2C 사용자 흐름에서 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)
