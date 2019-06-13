---
title: Tutorial - 사용자 흐름 만들기 - Azure Active Directory B2C | Microsoft Docs
description: Azure Portal를 사용하여 Azure Active Directory B2C에서 애플리케이션의 사용자 흐름을 만드는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fd8aefa924b927d9a55d0e91f35f40060097c53b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512146"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 사용자 흐름 만들기

애플리케이션에 사용자가 등록하거나, 로그인하거나, 프로필을 관리할 수 있는 [사용자 흐름](active-directory-b2c-reference-policies.md)이 있을 수 있습니다. zure AD(Azure Active Directory) B2C 테넌트에 다른 형식의 여러 사용자 흐름을 만들고 필요에 따라 애플리케이션에서 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 프로필 편집 사용자 흐름 만들기
> * 암호 재설정 사용자 흐름 만들기

이 자습서에서는 Azure Portal을 사용하여 권장되는 일부 사용자 흐름을 만드는 방법을 보여줍니다. 애플리케이션에서 ROPC(리소스 소유자 암호 자격 증명) 흐름을 설정하는 방법에 대한 정보를 원할 경우 [Azure AD B2C에서 리소스 소유자 암호 자격 증명 흐름 구성](configure-ropc.md)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

만들려는 사용자 흐름에 속하는 [애플리케이션을 등록](tutorial-register-applications.md)합니다. 

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>가입 및 로그인 사용자 흐름 만들기

등록 및 로그인 사용자 흐름은 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 애플리케이션 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.

    ![구독 디렉터리로 전환](./media/tutorial-create-user-flows/switch-directories.png)

3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 왼쪽 메뉴에서 **사용자 흐름**을 선택한 후 **새 사용자 흐름**을 선택합니다.

    ![새 사용자 흐름 선택](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

5. 권장 탭에서 **등록 및 로그인** 사용자 흐름을 선택합니다.

    ![등록 및 로그인 사용자 흐름 선택](./media/tutorial-create-user-flows/signup-signin-type.png)

6. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *signupsignin1*과 같습니다.
7. **ID 공급자**에서 **메일 등록**을 선택합니다.

    ![흐름 속성 설정](./media/tutorial-create-user-flows/signup-signin-properties.png)

8. **사용자 특성 및 클레임**에 대해 수집한 후 등록 동안 사용자로부터 전송하려는 클레임 및 특성을 선택합니다. 예를 들어 **자세히 보기**를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호**를 선택합니다. **확인**을 클릭합니다.

    ![특성 및 클레임 선택](./media/tutorial-create-user-flows/signup-signin-attributes.png)

9. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭하고 **지금 등록**을 선택합니다.

    ![사용자 흐름 실행](./media/tutorial-create-user-flows/signup-signin-run-now.png)

4. 유효한 이메일 주소를 입력하고 **확인 코드 보내기**를 클릭한 후 받은 확인 코드를 입력합니다.
5. 새 암호를 입력하고 암호를 확인합니다.
6. 표시하려는 이름을 입력하고, 국가 및 지역을 선택한 후, 우편 번호를 입력하고 **만들기**를 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
7. 이제 사용자 흐름을 다시 실행할 수 있으며 만든 계정으로 로그인할 수 있습니다. 반환된 된 토큰 이름, 국가/지역 및 우편 번호 선택 하는 클레임을 포함 합니다.

## <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

애플리케이션에서 프로필을 편집할 수 있도록 하려면 프로필 편집 사용자 흐름을 사용합니다.

1. 왼쪽 메뉴에서 **사용자 흐름**을 선택한 후 **새 사용자 흐름**을 선택합니다.
2. 권장 탭에서 **프로필 편집** 사용자 흐름을 선택합니다.
3. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *profileediting1*과 같습니다.
4. **ID 공급자**로 **로컬 계정 로그인**을 선택합니다.
5. **사용자 특성**으로 고객이 프로필에서 편집할 수 있도록 하려는 특성을 선택합니다. 예를 들어, **자세히 보기**를 선택한 후 **표시 이름** 및 **직함**을 선택합니다. **확인**을 클릭합니다.
6. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭하고 이전에 만든 계정으로 로그인합니다.
4. 이제 사용자의 표시 이름 및 직함을 변경할 수 있습니다. **계속**을 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

필요한 경우 애플리케이션 사용자가 암호를 재설정하도록 설정할 수 있습니다. 암호 재설정을 사용하도록 설정하려면 암호 재설정 사용자 흐름을 사용합니다.

1. 왼쪽 메뉴에서 **사용자 흐름**을 선택한 후 **새 사용자 흐름**을 선택합니다.
2. 권장 탭에서 **암호 재설정** 사용자 흐름을 선택합니다.
3. 사용자 흐름에 대한 **이름**을 입력합니다. 예를 들어 *passwordreset1*과 같습니다.
4. **ID 공급자**에서 **메일 주소를 사용하여 암호 재설정**을 사용하도록 설정합니다.
5. 애플리케이션 클레임에서 **자세히 표시**를 클릭하고, 애플리케이션으로 다시 전송되는 인증 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID**를 선택합니다.
6. **확인**을 클릭합니다.
7. **만들기**를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1*이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름의 개요 페이지에서 **사용자 흐름 실행**을 선택합니다.
2. **애플리케이션**으로 이전에 등록한 *webapp1*이라는 웹 애플리케이션을 선택합니다. **회신 URL**에는 `https://jwt.ms`가 표시되어야 합니다.
3. **사용자 흐름 실행**을 클릭하고 이전에 만든 계정으로 로그인합니다.
4. 이제 사용자의 암호를 변경할 수 있습니다. **계속**을 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 프로필 편집 사용자 흐름 만들기
> * 암호 재설정 사용자 흐름 만들기

> [!div class="nextstepaction"]
> [Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)