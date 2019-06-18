---
title: 자습서-Azure Active Directory B2C 사용자 흐름-만들기
description: 로그인을 사용 하도록 설정, 로그인, Azure portal 및 Azure Active Directory B2C에서 응용 프로그램에 대 한 사용자 프로필 편집에서 사용자 흐름을 만드는 방법에 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 111196388d0e17ecde8a2055959f2f573e43ade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056388"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 사용자 흐름 만들기

응용 프로그램에서 할 수 있습니다 [사용자 흐름](active-directory-b2c-reference-policies.md) 사용자 등록, 로그인 또는 프로필을 관리할 수 있도록 합니다. zure AD(Azure Active Directory) B2C 테넌트에 다른 형식의 여러 사용자 흐름을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 프로필 편집 사용자 흐름 만들기
> * 암호 재설정 사용자 흐름 만들기

이 자습서에서는 Azure Portal을 사용하여 권장되는 일부 사용자 흐름을 만드는 방법을 보여줍니다. 응용 프로그램에 (ROPC) 흐름을 리소스 소유자 암호 자격 증명을 설정 하는 방법에 대 한 정보를 찾으려는 경우 참조 [구성에서 Azure AD B2C 리소스 소유자 암호 자격 증명 흐름](configure-ropc.md)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

만들려는 사용자 흐름에 속하는 [애플리케이션을 등록](tutorial-register-applications.md)합니다.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>가입 및 로그인 사용자 흐름 만들기

등록 및 로그인 사용자 흐름은 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 애플리케이션 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.

    ![구독 디렉터리로 전환](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 왼쪽된 메뉴에서 **정책을**를 선택 **사용자 흐름 (정책)** 를 선택한 후 **새 사용자 흐름**합니다.

    ![새 사용자 흐름 선택](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. 에 **권장** 탭을 선택 합니다 **로그인 등록 및 로그인** 사용자 흐름.

    ![등록 및 로그인 사용자 흐름 선택](./media/tutorial-create-user-flows/signup-signin-type.png)

1. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *signupsignin1*과 같습니다.
1. **ID 공급자**에서 **메일 등록**을 선택합니다.

    ![흐름 속성 설정](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. **사용자 특성 및 클레임**에 대해 수집한 후 등록 동안 사용자로부터 전송하려는 클레임 및 특성을 선택합니다. 예를 들어, 선택 **자세히 보기**를 선택한 다음 특성 및 클레임에 대 한 **국가/지역**, **표시 이름**, 및 **Postal Code**. **확인**을 클릭합니다.

    ![특성 및 클레임 선택](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 해당 개요 페이지를 연 다음 선택 하기 위해 만든 사용자 흐름 선택 **사용자 흐름을 실행**합니다.
1. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행**을 클릭하고 **지금 등록**을 선택합니다.

    ![사용자 흐름 실행](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 유효한 전자 메일 주소를 입력, 클릭 **확인 코드 보내기**, receive 셰이프가 선택 하는 확인 코드를 입력 **코드 확인**합니다.
1. 새 암호를 입력하고 암호를 확인합니다.
1. 사용자의 국가 및 지역 선택, 표시 이름을 하려는 입력, 우편 번호를 입력 한 다음 클릭 **만들기**합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
1. 이제 사용자 흐름을 다시 실행할 수 있으며 만든 계정으로 로그인할 수 있습니다. 반환된 된 토큰에는 선택한 국가/지역, 이름 및 우편 번호 클레임 포함 됩니다.

## <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

애플리케이션에서 프로필을 편집할 수 있도록 하려면 프로필 편집 사용자 흐름을 사용합니다.

1. Azure AD B2C 테 넌 트 개요 페이지의 왼쪽된 메뉴에서 선택 **(정책) 사용자 흐름**를 선택한 후 **새 사용자 흐름**합니다.
1. 권장 탭에서 **프로필 편집** 사용자 흐름을 선택합니다.
1. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *profileediting1*과 같습니다.
1. **ID 공급자**로 **로컬 계정 로그인**을 선택합니다.
1. **사용자 특성**으로 고객이 프로필에서 편집할 수 있도록 하려는 특성을 선택합니다. 예를 들어, 선택 **자세히 보기**, 특성 및 클레임을 모두 선택 합니다 **표시 이름** 하 고 **직위**. **확인**을 클릭합니다.
1. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 해당 개요 페이지를 연 다음 선택 하기 위해 만든 사용자 흐름 선택 **사용자 흐름을 실행**합니다.
1. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행**을 클릭하고 이전에 만든 계정으로 로그인합니다.
1. 이제 사용자의 표시 이름 및 직함을 변경할 수 있습니다. **계속**을 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

사용자가 자신의 암호를 재설정 하도록 응용 프로그램을 사용 하려면 암호 재설정 사용자 흐름을 사용할 수 있습니다.

1. 왼쪽된 메뉴에서 선택 **(정책) 사용자 흐름**를 선택한 후 **새 사용자 흐름**합니다.
1. 권장 탭에서 **암호 재설정** 사용자 흐름을 선택합니다.
1. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *passwordreset1*과 같습니다.
1. **ID 공급자**에서 **메일 주소를 사용하여 암호 재설정**을 사용하도록 설정합니다.
1. 애플리케이션 클레임에서 **자세히 표시**를 클릭하고, 애플리케이션으로 다시 전송되는 인증 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID**를 선택합니다.
1. **확인**을 클릭합니다.
1. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 해당 개요 페이지를 연 다음 선택 하기 위해 만든 사용자 흐름 선택 **사용자 흐름을 실행**합니다.
1. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
1. 클릭 **사용자 흐름을 실행**에서 이전에 만든 선택한 계정의 메일 주소 확인 **계속**합니다.
1. 이제 사용자의 암호를 변경할 수 있습니다. 선택 하 고 암호를 변경할 **계속**합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 프로필 편집 사용자 흐름 만들기
> * 암호 재설정 사용자 흐름 만들기

다음으로, 사용자 로그인에 Azure AD와 같은 공급자를 사용 하 여 사용할 수 있도록 응용 프로그램에 id 공급자를 추가 하는 방법을 알아봅니다 Amazon, Facebook, GitHub, LinkedIn, Microsoft 또는 Twitter 합니다.

> [!div class="nextstepaction"]
> [응용 프로그램에 id 공급자 추가 >](tutorial-add-identity-providers.md)