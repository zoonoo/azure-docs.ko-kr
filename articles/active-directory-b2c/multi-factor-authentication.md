---
title: Azure Active Directory B2C의 Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C에서 보호하는 소비자 지향 애플리케이션에서 Multi-Factor Authentication을 사용하는 방법입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b7b7f1c5fb0a7991707a26b4a7f54fb3ffaf7bab
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033523"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 다단계 인증 사용 | Microsoft Docs

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C)는 [AZURE AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) 와 직접 통합 되므로 응용 프로그램에서 등록 및 로그인 환경에 두 번째 보안 계층을 추가할 수 있습니다. 코드 작성 없이도 다단계 인증을 사용하도록 설정할 수 있습니다. 이미 가입 및 로그인 사용자 흐름을 만든 경우에도 다단계 인증을 사용할 수 있습니다.

이 기능을 통해 응용 프로그램은 다음과 같은 시나리오를 처리할 수 있습니다.

- 하나의 애플리케이션에 액세스하려는 경우 다단계 인증이 필요하지 않지만 다른 애플리케이션에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정을 사용하여 자동차 보험 애플리케이션에 로그인할 수 있지만 동일한 디렉터리에 등록된 주택 보험 애플리케이션에 액세스하기 전에 전화번호를 확인해야 합니다.
- 일반적으로 애플리케이션에 액세스하는 데 다단계 인증이 필요하지 않지만 그 안에 중요한 부분에 액세스하려면 다단계 인증이 필요합니다. 예를 들어 고객이 소셜 또는 로컬 계정으로 은행 애플리케이션에 로그인하고 계정 잔액을 확인할 수 있지만 유선 전송을 시도하기 전에 전화번호를 확인해야 합니다.

## <a name="set-multi-factor-authentication"></a>다단계 인증 설정

::: zone pivot="b2c-user-flow"

1. [Azure 포털](https://portal.azure.com)
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 사용 하 여 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름** 을 선택합니다.
1. MFA를 사용 하도록 설정할 사용자 흐름을 선택 합니다. 예를 들어 *B2C_1_signinsignup* 합니다.
1. **속성** 을 선택합니다.
1. **다단계 인증** 섹션에서 원하는 **mfa 방법을** 선택한 다음 **mfa 적용** 에서 **Always on** 또는 **조건부 (권장)** 를 선택 합니다.
   > [!NOTE]
   >
   > - **조건부 (권장)** 를 선택 하는 경우 [사용자 흐름에 대 한 조건부 액세스를 추가](conditional-access-user-flow.md)하 고 정책을 적용할 앱을 지정 해야 합니다.
   > - MFA (multi-factor authentication)는 등록 사용자 흐름에 대해 기본적으로 사용 되지 않습니다. 전화 등록을 사용 하 여 사용자 흐름에서 MFA를 사용 하도록 설정할 수 있지만 전화 번호가 기본 식별자로 사용 되기 때문에 두 번째 인증 요소에 사용할 수 있는 유일한 옵션은 전자 메일 일회용 암호입니다.

1. **저장** 을 선택합니다. 이제이 사용자 흐름에 대해 MFA를 사용할 수 있습니다.

**사용자 흐름 실행** 을 사용하여 환경을 확인할 수 있습니다. 다음 시나리오를 확인합니다.

다단계 인증 단계가 실행되기 전에 테넌트에 고객 계정이 만들어집니다. 이 단계에서 고객에게 자신의 전화 번호를 제공하고 확인하라는 메시지가 표시됩니다. 확인에 성공한 경우 전화번호는 나중에 사용하도록 고객 계정에 연결됩니다. 고객이 취소 또는 삭제하더라도 다음에 로그인하는 동안 다시 전화 번호를 확인하도록 요청할 수 있습니다(다단계 인증을 사용하도록 설정한 경우).

::: zone-end

::: zone pivot="b2c-custom-policy"

Multi-Factor Authentication 사용 하도록 설정 하려면 GitHub에서 사용자 지정 정책 시작 팩을 가져온 다음 Azure AD B2C 테 넌 트 이름으로 **SocialAndLocalAccountsWithMFA** 스타터 팩의 XML 파일을 업데이트 합니다. **SocialAndLocalAccountsWithMFA** 는 소셜, 로컬 및 multi-factor authentication 옵션을 사용 하도록 설정 합니다. 자세한 내용은 [Active Directory B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)을 참조 하세요. 

::: zone-end
