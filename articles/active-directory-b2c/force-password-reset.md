---
title: Azure AD B2C에서 암호 강제 재설정 흐름 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 암호 강제 재설정 흐름을 설정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7fac7df0978b23e535d8761b436b14e2f41e5f91
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209505"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호 강제 재설정 흐름 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

> [!IMPORTANT]
> 암호 강제 재설정은 Azure AD B2C의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview"></a>개요
관리자는 사용자가 암호를 잊어버린 경우 [사용자의 암호를 재설정](manage-users-portal.md#reset-a-users-password)할 수 있습니다. 또는 암호를 강제로 재설정하도록 할 수 있습니다. 이 문서에서는 이러한 시나리오에서 암호를 강제로 재설정하는 방법을 알아봅니다.

관리자가 Azure Portal을 통해 사용자의 암호를 재설정하면 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이 `true`로 설정됩니다. [로그인 및 등록](add-sign-up-and-sign-in-policy.md) 과정에서 이 특성의 값을 확인합니다. 특성이 `true`로 설정된 경우 사용자는 로그인을 완료한 후 암호를 재설정해야 합니다. 그런 다음, 특성의 값이 다시 `false`로 설정됩니다.

![암호 강제 재설정 흐름](./media/force-password-reset/force-password-reset-flow.png)

암호 재설정 흐름은 로그인을 위해 암호와 함께 [이메일 주소](identity-provider-local.md#email-sign-in) 또는 [사용자 이름](identity-provider-local.md#username-sign-in)을 사용하는 Azure AD B2C의 로컬 계정에 적용됩니다.

::: zone pivot="b2c-user-flow"

### <a name="force-a-password-reset-after-90-days"></a>90일 후 암호 강제 재설정

관리자는 [MS Graph](microsoft-graph-operations.md)를 사용하여 사용자의 암호 만료를 90일로 설정할 수 있습니다. 90일 후 [forceChangePasswordNextSignIn](user-profile-attributes.md#password-profile-property) 특성의 값이 `true`로 자동 설정됩니다. 사용자의 암호 만료 정책을 설정하는 방법에 대한 자세한 내용은 [암호 정책 특성](user-profile-attributes.md#password-policy-attribute)을 참조하세요.

암호 만료 정책이 설정되면 이 문서에 설명된 대로 암호 강제 재설정 흐름도 구성해야 합니다.  

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>정책 구성

등록 또는 로그인 사용자 흐름에서 **Forced password reset(암호 강제 재설정)** 설정을 사용하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 사용자 지정하려는 등록 및 로그인 또는 로그인 사용자 흐름(**권장** 형식)을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **속성** 을 선택합니다.
1. **암호 복잡성** 에서 **Forced password reset(암호 강제 재설정)** 을 선택합니다.
1. **저장** 을 선택합니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 사용자 관리자 또는 암호 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다. 사용 가능한 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../active-directory/roles/permissions-reference.md#all-roles)을 참조하세요.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자** 를 선택합니다. 암호 재설정을 테스트하는 데 사용할 사용자를 검색하여 선택하고 **암호 재설정** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. 테스트하려는 등록 또는 로그인 사용자 흐름(**권장** 형식)을 선택합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 암호를 재설정한 사용자 계정으로 로그인합니다.
1. 이제 사용자의 암호를 변경해야 합니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

이 기능은 현재 사용자 흐름에만 사용할 수 있습니다. 설정 단계에서 위의 **사용자 흐름** 을 선택합니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[셀프 서비스 암호 재설정](add-password-reset-policy.md)을 지정합니다.
