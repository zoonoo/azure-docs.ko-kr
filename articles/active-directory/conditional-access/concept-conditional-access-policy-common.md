---
title: 공통 조건부 액세스 정책 - Azure Active Directory
description: 조직에 일반적으로 사용되는 조건부 액세스 정책
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
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295302"
---
# <a name="common-conditional-access-policies"></a>일반 조건부 액세스 정책

[보안 기본값은](../fundamentals/concept-fundamentals-security-defaults.md) 일부 조직에서는 좋지만 대부분의 조직에서는 제공하는 것보다 더 많은 유연성이 필요합니다. 예를 들어 많은 사람들이 다단계 인증을 요구하는 조건부 액세스 정책에서 비상 액세스 또는 브레이크 글래스 관리 계정과 같은 특정 계정을 제외할 수 있어야 합니다. 이러한 조직의 경우 이 문서에서 참조하는 일반적인 정책을 사용할 수 있습니다.

![Azure 포털의 조건부 액세스 정책](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>응급 액세스 계정

긴급 액세스 계정에 대한 자세한 정보와 중요한 이유에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다. 

* [Azure AD에서 응급 액세스 계정 관리](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory를 사용하여 복원력 있는 액세스 제어 관리 전략 수립](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>조직에서 배포하는 일반적인 정책

* [레거시 인증 차단](howto-conditional-access-policy-block-legacy.md)\*
* [관리자를 위한 MFA 필요](howto-conditional-access-policy-admin-mfa.md)\*
* [Azure 관리를 위한 MFA 필요](howto-conditional-access-policy-azure-management.md)\*
* [모든 사용자에 대한 MFA 필요](howto-conditional-access-policy-all-users-mfa.md)\*

\*이러한 네 가지 정책은 함께 구성할 때 [보안 기본값으로](../fundamentals/concept-fundamentals-security-defaults.md)활성화된 기능을 모방합니다.

## <a name="additional-policies"></a>추가 정책

* [위험 기반 조건부 액세스(Azure AD Premium P2 필요)](howto-conditional-access-policy-risk.md)
* [MFA 등록을 위해 신뢰할 수 있는 위치 필요](howto-conditional-access-policy-registration.md)
* [위치별 액세스 차단](howto-conditional-access-policy-location.md)
* [규정 준수 디바이스 필요](howto-conditional-access-policy-compliant-device.md)
* [특정 앱을 제외한 액세스 차단](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 What If 도구를 사용하여 로그인 동작을 시뮬레이션합니다.](troubleshoot-conditional-access-what-if.md)

- [조건부 액세스에 보고서 전용 모드를 사용하여 새 정책 결정의 영향을 확인합니다.](concept-conditional-access-report-only.md)
