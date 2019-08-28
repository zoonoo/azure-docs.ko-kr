---
title: 조건부 액세스-위험 기반 조건부 액세스-Azure Active Directory
description: 정책에 대 한 Id 보호 향상 기능을 사용 하도록 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64d1b3e2f36256164420ae6b2e699f0ef48e2e78
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576551"
---
# <a name="conditional-access-risk-based-conditional-access"></a>조건부 액세스: 위험 기반 조건부 액세스

Azure AD Premium P2 라이선스가 있는 조직은 Azure AD ID 보호 위험 이벤트를 통합 하는 조건부 액세스 정책을 만들 수 있습니다. 사용할 수 있는 기본 정책에는 세 가지가 있습니다. 

* 모든 사용자가 Azure Multi-factor Authentication에 등록 하도록 요구 합니다.
* 위험이 높은 사용자의 경우 암호를 변경 해야 합니다.
* 중간 또는 높은 로그인 위험이 있는 사용자의 경우 multi-factor authentication이 필요 합니다.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>모든 사용자가 Azure Multi-factor Authentication에 등록 하도록 요구

이 정책을 사용 하도록 설정 하면 모든 사용자가 14 일 내에 Azure Multi-factor Authentication에 등록 해야 합니다. 

1. **Azure Portal**에 로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. **MFA 등록**을 클릭합니다.
1. **할당**에서 **사용자**를 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **제외**아래에서 **제외 된 사용자 선택**을 선택 하 고 조직의 응급 액세스 또는 손상 없는 계정을 선택한 다음 **선택**을 선택 합니다. 
   1. **완료** 를 선택합니다.
1. **정책 적용** 을 **켜기**로 설정 합니다.
1. **Save**을 클릭합니다.

## <a name="require-a-password-change-high-risk-users"></a>높은 위험 수준의 사용자 암호 변경 필요

Microsoft는 연구원, 법 집행 기관, Microsoft의 다양한 보안 팀, 신뢰할 수 있는 기타 소스와 협력하여 사용자 이름 및 암호 쌍을 찾습니다. 이러한 쌍 중의 하나가 환경의 계정과 일치하는 경우 다음 정책을 사용하여 위험 기반 암호 변경을 트리거할 수 있습니다.

1. **Azure Portal**에 로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. **사용자 위험 정책**을 클릭 합니다.
1. **할당**에서 **사용자** 를 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **제외**아래에서 **제외 된 사용자 선택**을 선택 하 고 조직의 응급 액세스 또는 손상 없는 계정을 선택한 다음 **선택**을 선택 합니다.
   1. **완료** 를 선택합니다.
1. **조건**에서 **사용자 위험**을 선택한 다음 **높음**을 선택 합니다.
   1. **선택** , **완료**를 차례로 클릭 합니다.
1. **액세스** **제어** > 에서 **액세스 허용**을 선택 하 고 **암호 변경 필요**를 선택 합니다.
   1. **선택**을 클릭합니다.
1. **정책 적용** 을 **켜기**로 설정 합니다.
1. **Save**을 클릭합니다.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>MFA 중간 또는 높은 로그인 위험 사용자 필요

대부분의 사용자는 추적 가능한 일반 동작을 갖고 있으며, 정상 범위를 벗어나면 사용자가 로그인하도록 허용하는 것이 위험할 수 있습니다. 해당 사용자를 차단 하거나 multi-factor authentication을 수행 하도록 요청 하는 것이 사실 사용자의 신원을 증명할 수 있습니다. 위험한 로그인이 감지되면 MFA를 요구하는 정책을 사용하려면 다음 정책을 사용하도록 설정합니다.

1. **Azure Portal**에 로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. **로그인 위험 정책** 을 클릭 합니다.
1. **할당**에서 **사용자** 를 선택 합니다.
   1. **포함**아래에서 **모든 사용자**를 선택 합니다.
   1. **제외**아래에서 **제외 된 사용자 선택**을 선택 하 고 조직의 응급 액세스 또는 손상 없는 계정을 선택한 다음 **선택**을 선택 합니다.
   1. **완료** 를 선택합니다.
1. **조건**에서 **로그인 위험**을 선택한 다음 **보통 이상**을 선택 합니다.
   1. **선택** , **완료**를 차례로 클릭 합니다.
1. **액세스** **제어** > 에서 **액세스 허용**을 선택한 다음 **다단계 인증 필요**를 선택 합니다.
   1. **선택**을 클릭합니다.
1. **정책 적용** 을 **켜기**로 설정 합니다.
1. **Save**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[작동 방법: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 문서를 참조하세요

[Azure Active Directory Identity Protection 이란?](../identity-protection/overview.md)
