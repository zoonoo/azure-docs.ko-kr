---
title: 일반적인 조건부 액세스 정책-Azure Active Directory
description: 조직에 일반적으로 사용 되는 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1991a3fe844b3a8669a67e491359e1a96658c844
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151260"
---
# <a name="common-conditional-access-policies"></a>일반 조건부 액세스 정책

기준 보호 정책은 유용 하지만 많은 조직에는 제공 되는 것 보다 더 많은 유연성이 필요 합니다. 예를 들어 많은 조직에서 multi-factor authentication을 요구 하는 조건부 액세스 정책에서 비상 액세스 또는 투명 효과 관리 계정과 같은 특정 계정을 제외 하는 기능이 필요 합니다. 이러한 조직에서는이 문서에서 참조 하는 일반적인 정책을 사용할 수 있습니다.

![Azure Portal의 조건부 액세스 정책](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>응급 액세스 계정

응급 액세스 계정에 대 한 자세한 내용 및 중요 한 이유는 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용 하 여 복원 력 있는 액세스 제어 관리 전략 만들기](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>조직에서 배포 하는 일반적인 정책

* [관리자 용 MFA 필요](howto-conditional-access-policy-admin-mfa.md)
* [Azure 관리를 위한 MFA 필요](howto-conditional-access-policy-azure-management.md)
* [모든 사용자에 대해 MFA 필요](howto-conditional-access-policy-all-users-mfa.md)
* [레거시 인증 차단](howto-conditional-access-policy-block-legacy.md)
* [위험 기반 조건부 액세스 (P2 Azure AD Premium 필요)](howto-conditional-access-policy-risk.md)
* [MFA 등록을 위해 신뢰할 수 있는 위치 필요](howto-conditional-access-policy-registration.md)
* [위치별 액세스 차단](howto-conditional-access-policy-location.md)
* [준수 장치 필요](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>다음 단계

[조건부 액세스 What If 도구를 사용 하 여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)
