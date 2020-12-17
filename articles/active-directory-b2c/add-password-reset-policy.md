---
title: 암호 재설정 흐름 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 암호 다시 설정 흐름을 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618831"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 재설정 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>암호 rest 흐름

암호 재설정 정책을 사용 하면 사용자가 잊어버린 암호를 다시 설정할 수 있습니다. 암호 다시 설정 흐름은 다음 단계를 포함 합니다. 
1. 등록 및 로그인 페이지에서 사용자가 "암호를 잊으셨나요?"를 클릭 합니다. 링크를 사용하여 비용 없이 계정을 만들 수 있습니다. Azure AD B2C AADB2C90118 오류 코드를 앱에 반환 합니다. 앱은 암호 재설정 정책을 호출 하 여이 오류 코드를 처리 합니다. 
1. 사용자는 시간을 한 번의 암호를 사용 하 여 전자 메일을 제공 하 고 확인 합니다.
1. 새 암호를 입력 하십시오.

![암호 다시 설정 흐름](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>필수 구성 요소

아직 수행 하지 않은 경우 [Azure Active Directory B2C에 웹 응용 프로그램을 등록](tutorial-register-applications.md)합니다.

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>암호 재설정 사용자 흐름 만들기

애플리케이션 사용자가 자신의 암호를 다시 설정할 수 있도록 하려면 암호 재설정 사용자 흐름을 사용합니다.

1. Azure AD B2C 테넌트 개요 메뉴에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 탭에서 **암호 다시 설정** 사용자 흐름을 선택합니다. 
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다.
1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *passwordreset1* 과 같습니다.
1. **ID 공급자** 에서 **메일 주소를 사용하여 암호 재설정** 을 사용하도록 설정합니다.
2. 애플리케이션 클레임에서 **자세히 표시** 를 클릭하고, 애플리케이션으로 다시 전송되는 인증 토큰에 반환하려는 클레임을 선택합니다. 예를 들어 **사용자의 개체 ID** 를 선택합니다.
3. **확인** 을 클릭합니다.
4. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1* 이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭하고, 이전에 만든 계정의 이메일 주소를 확인한 다음, **계속** 을 선택합니다.
1. 이제 사용자의 암호를 변경할 수 있습니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>암호 재설정 정책 만들기

사용자 지정 정책은 사용자 경험를 정의 하기 위해 Azure AD B2C 테 넌 트에 업로드 하는 XML 파일 집합입니다. 등록 및 로그인, 암호 재설정 및 프로필 편집 정책을 포함 하 여 몇 가지 미리 작성 된 정책으로 시작 팩을 제공 합니다. 자세한 내용은 [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)을 참조 하세요.

::: zone-end

## <a name="next-steps"></a>다음 단계

[프로필 편집 흐름](add-profile-editing-policy.md)을 설정 합니다.