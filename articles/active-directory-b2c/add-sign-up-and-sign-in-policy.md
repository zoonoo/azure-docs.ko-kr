---
title: 등록 및 로그인 흐름 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 등록 및 로그인 흐름을 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fbcdef0002a227d5319fc01e625146480e4c99fc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043753"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 등록 및 로그인 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>등록 및 로그인 흐름

등록 및 로그인 정책을 통해 사용자는 다음을 수행할 수 있습니다. 

* 로컬 계정으로 등록
* 로컬 계정으로 로그인
* 소셜 계정을 사용 하 여 등록 또는 로그인
* 암호 재설정

![프로필 편집 흐름](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

아직 수행 하지 않은 경우 [Azure Active Directory B2C에 웹 응용 프로그램을 등록](tutorial-register-applications.md)합니다.

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>가입 및 로그인 사용자 흐름 만들기

등록 및 로그인 사용자 흐름은 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 애플리케이션 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.

    ![새 사용자 흐름 단추가 강조 표시된 포털의 사용자 흐름 페이지](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. **사용자 흐름 만들기** 탭에서 **가입 및 로그인** 사용자 흐름을 선택합니다.

    ![가입 및 로그인 흐름이 강조 표시된 사용자 흐름 페이지 선택](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)

    ![속성이 강조 표시된 Azure Portal의 사용자 흐름 만들기 페이지](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *signupsignin1* 과 같습니다.
1. **Id 공급자** 에 대해 **전자 메일 등록** 을 선택 합니다.
1. **사용자 특성 및 클레임** 에 대해 수집한 후 등록 동안 사용자로부터 전송하려는 클레임 및 특성을 선택합니다. 예를 들어 **자세히 보기** 를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호** 의 특성 및 클레임을 선택합니다. **확인** 을 클릭합니다.

    ![세 개의 클레임이 선택된 특성 및 클레임 선택 페이지](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.
2. 단계에 따라 ["암호를 잊으셨나요?"에 대 한 흐름을 처리](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) 합니다. 등록 또는 로그인 정책 내에 있습니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭하고 **지금 등록** 을 선택합니다.

    ![사용자 흐름 실행 단추가 강조 표시된 포털의 사용자 흐름 실행 페이지](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. 유효한 이메일 주소를 입력하고, **확인 코드 보내기** 를 클릭하고, 받은 확인 코드를 입력한 다음, **코드 확인** 을 선택합니다.
1. 새 암호를 입력하고 암호를 확인합니다.
1. 국가 및 지역을 선택하고, 표시하려는 이름을 입력하고, 우편 번호를 입력한 다음, **만들기** 를 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
1. 이제 사용자 흐름을 다시 실행할 수 있으며 만든 계정으로 로그인할 수 있습니다. 반환된 토큰에는 선택한 이름, 국가/지역, 이름 및 우편 번호에 대한 클레임이 포함되어 있습니다.

> [!NOTE]
> "사용자 흐름 실행" 환경은 현재 인증 코드 흐름을 사용하는 SPA 회신 URL 유형과 호환되지 않습니다. 이러한 종류의 앱에서 "사용자 흐름 실행" 환경을 사용하려면 "웹" 유형의 회신 URL을 등록하고 [여기](tutorial-register-spa.md)에 설명된 대로 암시적 흐름을 사용하도록 설정합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>등록 및 로그인 정책 만들기

사용자 지정 정책은 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 등록 및 로그인, 암호 재설정 및 프로필 편집 정책을 포함 하 여 몇 가지 미리 작성 된 정책으로 시작 팩을 제공 합니다. 자세한 내용은 [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)을 참조 하세요.

::: zone-end

## <a name="next-steps"></a>다음 단계

* [소셜 id 공급자를 사용 하 여 로그인](add-identity-provider.md)을 추가 합니다.
* [암호 다시 설정 흐름](add-password-reset-policy.md)을 설정 합니다.
