---
title: 사용자 위험 기반 조건부 액세스 - Azure Active Directory
description: ID 보호 사용자 위험을 사용하여 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5423a61c8febee72f32935f3dee4e9f9e868630
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89049080"
---
# <a name="conditional-access-user-risk-based-conditional-access"></a>조건부 액세스: 사용자 위험 기반 조건부 액세스

Microsoft는 연구원, 법 집행 기관, Microsoft의 다양한 보안 팀, 신뢰할 수 있는 기타 소스와 협력하여 유출된 사용자 이름 및 암호 쌍을 찾습니다. Azure AD Premium P2 라이선스가 있는 조직은 [Azure AD ID 보호 사용자 위험 검색](../identity-protection/concept-identity-protection-risks.md#user-risk)을 통합하여 조건부 액세스 정책을 만들 수 있습니다. 

이 정책을 할당할 수 있는 두 위치가 있습니다. 보안 암호 변경이 필요한 사용자 위험 기반 조건부 액세스 정책을 사용하도록 설정하려면 조직에서 다음 옵션 중 하나를 선택해야 합니다.

## <a name="enable-with-conditional-access-policy"></a>조건부 액세스 정책을 통해 사용

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다. 자세한 내용은 [정책에 대한 명명 표준 설정](./plan-conditional-access.md#set-naming-standards-for-your-policies)을 참조하세요.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택합니다.
1. **조건** > **사용자 위험** 에서 **구성** 을 **예** 로 설정합니다. **정책을 적용하기 위해 필요한 사용자 위험 수준 구성** 에서 **높음**, **완료** 를 차례로 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **암호 변경 필요**, **선택** 을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 사용하도록 생성합니다.

## <a name="enable-through-identity-protection"></a>ID 보호를 통해 사용

1. **Azure Portal** 에 로그인합니다.
1. **모든 서비스** 를 선택하고 **Azure AD ID 보호** 를 찾습니다.
1. **사용자 위험 정책** 을 선택합니다.
1. **할당** 에서 **사용자** 를 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **제외된 사용자 선택**, 조직의 응급 액세스 또는 비상 계정, **선택** 을 차례로 선택합니다.
   1. **완료** 를 선택합니다.
1. **조건** 에서 **사용자 위험**, **높음** 을 차례로 선택합니다.
   1. **선택**, **완료** 를 차례로 선택합니다.
1. **제어** > **액세스** 에서 **액세스 허용**, **암호 변경 필요** 를 차례로 선택합니다.
   1. **선택** 을 선택합니다.
1. **정책 적용** 을 **켜기** 로 설정합니다.
1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

[위험 기반 조건부 액세스 로그인](howto-conditional-access-policy-risk.md)

[조건부 액세스 보고 전용 모드를 사용하여 영향 확인](howto-conditional-access-insights-reporting.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[Azure Active Directory Identity Protection이란?](../identity-protection/overview-identity-protection.md)