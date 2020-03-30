---
title: 조건부 액세스 - 위험 기반 조건부 액세스 - Azure Active Directory
description: 정책에 대한 ID 보호 향상을 가능하게 하는 조건부 액세스 정책 만들기
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295149"
---
# <a name="conditional-access-risk-based-conditional-access"></a>조건부 액세스: 위험 기반 조건부 액세스

Azure AD 프리미엄 P2 라이선스가 있는 조직은 Azure AD ID 보호 위험 검색을 통합하는 조건부 액세스 정책을 만들 수 있습니다. 기본적으로 사용할 수 있는 세 가지 기본 정책이 있습니다. 

* 모든 사용자가 Azure 다단계 인증에 등록하도록 요구합니다.
* 위험이 높은 사용자를 위해 암호를 변경해야 합니다.
* 중간 또는 높은 로그인 위험이 있는 사용자에 대해 다단계 인증이 필요합니다.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>모든 사용자가 Azure 다단계 인증에 등록하도록 요구합니다.

이 정책을 사용하도록 설정하려면 모든 사용자가 14일 이내에 Azure 다단계 인증에 등록해야 합니다. 

1. **Azure 포털에**로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. **MFA 등록**을 클릭합니다.
1. **할당에서** **사용자를**선택합니다.
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **에서 제외**: **제외된 사용자 선택을**선택하고 조직의 긴급 액세스 또는 브레이크 글래스 계정을 선택하고 선택 을 **선택합니다.** 
   1. **완료를 선택합니다.**
1. **정책 적용을** **설정 합니다.**
1. **저장**을 클릭합니다.

## <a name="require-a-password-change-high-risk-users"></a>암호 변경 위험이 높은 사용자 필요

Microsoft는 연구원, 법 집행 기관, Microsoft의 다양한 보안 팀, 신뢰할 수 있는 기타 소스와 협력하여 사용자 이름 및 암호 쌍을 찾습니다. 이러한 쌍 중의 하나가 환경의 계정과 일치하는 경우 다음 정책을 사용하여 위험 기반 암호 변경을 트리거할 수 있습니다.

1. **Azure 포털에**로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. 사용자 **위험 정책을**클릭합니다.
1. 할당에서 **사용자를** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **에서 제외**: **제외된 사용자 선택을**선택하고 조직의 긴급 액세스 또는 브레이크 글래스 계정을 선택하고 선택 을 **선택합니다.**
   1. **완료를 선택합니다.**
1. **조건에서**사용자 **위험을**선택한 다음 **높음**을 선택합니다.
   1. **다음 완료** **를 선택을** 클릭합니다.
1. **컨트롤 액세스에서** >  **액세스 허용을**선택한 다음 **암호 변경 필요를**선택합니다.**Access**
   1. **선택**을 클릭합니다.
1. **정책 적용을** **설정 합니다.**
1. **저장**을 클릭합니다.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>MFA 중간 또는 높은 로그인 위험 사용자 필요

대부분의 사용자는 추적 가능한 일반 동작을 갖고 있으며, 정상 범위를 벗어나면 사용자가 로그인하도록 허용하는 것이 위험할 수 있습니다. 해당 사용자를 차단하거나 다단계 인증을 수행하여 실제로 자신이 누구인지 증명하도록 요청할 수 있습니다. 위험한 로그인이 감지되면 MFA를 요구하는 정책을 사용하려면 다음 정책을 사용하도록 설정합니다.

1. **Azure 포털에**로그인합니다.
1. **모든 서비스**를 클릭하고 **Azure AD ID 보호**로 이동합니다.
1. 로그인 **위험 정책을 클릭합니다.**
1. 할당에서 **사용자를** **선택합니다.**
   1. **포함**에서 **모든 사용자를**선택합니다.
   1. **에서 제외**: **제외된 사용자 선택을**선택하고 조직의 긴급 액세스 또는 브레이크 글래스 계정을 선택하고 선택 을 **선택합니다.**
   1. **완료를 선택합니다.**
1. **조건에서**로그인 **위험을**선택한 다음 **중간 이상**을 선택합니다.
   1. **다음 완료** **를 선택을** 클릭합니다.
1. **컨트롤 액세스에서** > **액세스** **허용을**선택한 다음 **다단계 인증 필요를**선택합니다.
   1. **선택**을 클릭합니다.
1. **정책 적용을** **설정 합니다.**
1. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)

[조건부 액세스 보고서 전용 모드를 사용하여 영향 확인](howto-conditional-access-report-only.md)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[작동 방법: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Azure Active Directory Identity Protection이란?](../identity-protection/overview.md)
