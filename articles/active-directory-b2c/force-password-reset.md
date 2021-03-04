---
title: Azure AD B2C에서 암호 재설정 흐름 강제 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 강제 암호 재설정 흐름을 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0477153b466702bec0fa2d5221fee1e054d06314
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033780"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 재설정 흐름을 설정 합니다.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

관리자는 사용자가 암호를 잊어버린 경우 [사용자의 암호를 다시 설정할](manage-users-portal.md#reset-a-users-password) 수 있습니다. 또는 암호를 다시 설정 하도록 강제할 수 있습니다. 이 문서에서는 이러한 시나리오에서 암호 재설정을 강제로 수행 하는 방법을 알아봅니다.

## <a name="overview"></a>개요

관리자가 Azure Portal를 통해 사용자의 암호를 다시 설정 하면 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이로 설정 됩니다 `true` .

[로그인 및 등록](add-sign-up-and-sign-in-policy.md) 과정에서이 특성의 값을 확인 합니다. 사용자가 로그인을 완료 한 후 특성을로 설정 하면 `true` 사용자가 암호를 다시 설정 해야 합니다. 그런 다음 특성의 값이 뒤로로 설정 됩니다 `false` .

![암호 다시 설정 흐름 강제](./media/force-password-reset/force-password-reset-flow.png)

암호 다시 설정 흐름은 [전자 메일 주소](identity-provider-local.md#email-sign-in) 를 사용 하는 Azure AD B2C의 로컬 계정 또는 로그인에 대 한 암호를 포함 하는 [사용자 이름](identity-provider-local.md#username-sign-in) 에 적용 됩니다.

### <a name="force-a-password-reset-after-90-days"></a>90 일 후 암호 다시 설정 적용

관리자는 [MS Graph](microsoft-graph-operations.md)를 사용 하 여 사용자의 암호 만료를 90 일로 설정할 수 있습니다. 90 일 후에 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이 자동으로로 설정 됩니다 `true` . 사용자의 암호 만료 정책을 설정 하는 방법에 대 한 자세한 내용은 [암호 정책 특성](user-profile-attributes.md#password-policy-attribute)을 참조 하세요.

암호 만료 정책이 설정 되 면이 문서에 설명 된 대로 강제 암호 재설정 흐름을 구성 해야 합니다.  

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>정책 구성

::: zone pivot="b2c-user-flow"

등록 또는 로그인 사용자 흐름에서 **강제 암호 재설정** 설정을 사용 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정 하려는 등록 및 로그인 또는 로그인 사용자 흐름 ( **권장** 형식)을 선택 합니다.
1. **설정** 아래의 왼쪽 메뉴에서 **속성** 을 선택 합니다.
1. **암호 복잡도** 에서 **강제 암호 재설정** 을 선택 합니다.
1. **저장** 을 선택합니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 사용자 관리자 또는 암호 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 사용 가능한 역할에 대 한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/roles/permissions-reference.md#all-roles)을 참조 하세요.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자** 를 선택합니다. 암호 재설정을 테스트 하는 데 사용할 사용자를 검색 하 고 선택 하 고 **암호 재설정** 을 선택 합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 테스트 하려는 등록 또는 로그인 사용자 흐름 ( **권장**)을 선택 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 암호를 재설정 한 사용자 계정으로 로그인 합니다.
1. 이제 사용자에 대 한 암호를 변경 해야 합니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

1. [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/force-password-reset)에서 암호 재설정 강제 실행의 예를 가져옵니다.
1. 각 파일에서 문자열을 `yourtenant` Azure AD B2C 테 넌 트의 이름으로 바꿉니다. 예를 들어 B2C 테 넌 트의 이름이 *contosob2c* 인 경우의 모든 인스턴스는가 됩니다 `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com` .
1. 다음 순서로 정책 파일을 업로드 합니다. 확장 정책 `TrustFrameworkExtensionsCustomForcePasswordReset.xml` , 신뢰 당사자 정책 `SignUpOrSigninCustomForcePasswordReset.xml` .

### <a name="test-the-policy"></a>정책 테스트

1. 사용자 관리자 또는 암호 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다. 사용 가능한 역할에 대 한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/roles/permissions-reference.md#all-roles)을 참조 하세요.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자** 를 선택합니다. 암호 재설정을 테스트 하는 데 사용할 사용자를 검색 하 고 선택 하 고 **암호 재설정** 을 선택 합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. 정책을 선택 `B2C_1A_signup_signin_Custom_ForcePasswordReset` 하 여 엽니다. 
1. **응용 프로그램** 의 경우 [이전에 등록](troubleshoot-custom-policies.md#troubleshoot-the-runtime)한 웹 응용 프로그램을 선택 합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택 합니다.
1. 암호를 재설정 한 사용자 계정으로 로그인 합니다.
1. 이제 사용자에 대 한 암호를 변경 해야 합니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[셀프 서비스 암호 재설정](add-password-reset-policy.md)을 설정 합니다.
