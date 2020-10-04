---
title: 일반적인 조건부 액세스 정책-Azure Active Directory
description: 조직에 일반적으로 사용 되는 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 114f26c71ee1aecdb1a0ad3d1c01e558843c0969
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91705061"
---
# <a name="common-conditional-access-policies"></a>일반 조건부 액세스 정책

[보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md) 은 일부 조직에는 유용 하지만 많은 조직에는 제공 되는 것 보다 더 많은 유연성이 필요 합니다. 예를 들어 많은 조직에서 multi-factor authentication을 요구 하는 조건부 액세스 정책에서 비상 액세스 또는 투명 효과 관리 계정과 같은 특정 계정을 제외 하는 기능이 필요 합니다. 이러한 조직에서는이 문서에서 참조 하는 일반적인 정책을 사용할 수 있습니다.

![Azure Portal의 조건부 액세스 정책](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>응급 액세스 계정

응급 액세스 계정에 대 한 자세한 내용 및 중요 한 이유는 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>조직에서 배포 하는 일반적인 정책

* [레거시 인증 차단](howto-conditional-access-policy-block-legacy.md)\*
* [관리자 용 MFA 필요](howto-conditional-access-policy-admin-mfa.md)\*
* [Azure 관리를 위한 MFA 필요](howto-conditional-access-policy-azure-management.md)\*
* [모든 사용자에 대해 MFA 필요](howto-conditional-access-policy-all-users-mfa.md)\*

\* 이러한 네 가지 정책은 함께 구성 된 경우 [보안 기본값으로](../fundamentals/concept-fundamentals-security-defaults.md)사용 하도록 설정 된 기능을 모방 합니다.

## <a name="additional-policies"></a>추가 정책

* [로그인 위험 기반 조건부 액세스 (P2 Azure AD Premium 필요)](howto-conditional-access-policy-risk.md)
* [사용자 위험 기반 조건부 액세스 (P2 Azure AD Premium 필요)](howto-conditional-access-policy-risk-user.md)
* [MFA 등록을 위해 신뢰할 수 있는 위치 필요](howto-conditional-access-policy-registration.md)
* [위치별 액세스 차단](howto-conditional-access-policy-location.md)
* [규정 준수 디바이스 필요](howto-conditional-access-policy-compliant-device.md)
* [특정 앱을 제외한 액세스 차단](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 What If 도구를 사용 하 여 로그인 동작을 시뮬레이션 합니다.](troubleshoot-conditional-access-what-if.md)

- [조건부 액세스에 대 한 보고서 전용 모드를 사용 하 여 새로운 정책 결정의 영향을 확인 합니다.](concept-conditional-access-report-only.md)
