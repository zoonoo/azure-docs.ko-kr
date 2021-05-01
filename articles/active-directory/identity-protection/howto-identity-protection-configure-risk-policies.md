---
title: 위험 정책 - Azure Active Directory Identity Protection
description: Azure Active Directory Identity Protection에서 위험 정책 사용 및 구성
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 366d68be1a7f115980973015e363da6095876754
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997633"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>방법: 위험 정책 구성 및 사용

이전 문서에서 설명한 대로 [ID 보호 정책](concept-identity-protection-policies.md)에는 디렉터리에서 사용할 수 있는 두 가지 위험 정책이 있습니다. 

- 로그인 위험 정책
- 사용자 위험 정책

![사용자 및 로그인 위험 정책을 사용하도록 설정하는 보안 개요 페이지](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

두 정책은 모두 해당 환경에서 위험 검색에 대한 대응을 자동화하며 위험이 검색될 때 사용자가 직접 수정할 수 있도록 합니다. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>필수 구성 요소 

조직에서 위험 검색 시 사용자가 직접 수정할 수 있게 하려면 셀프 서비스 암호 재설정 및 Azure AD Multi-Factor Authentication에 둘 다 사용자를 등록해야 합니다. [결합된 보안 정보 등록 환경을 사용하도록 설정](../authentication/howto-registration-mfa-sspr-combined.md)하는 것이 좋습니다. 사용자가 직접 수정할 수 있게 하면 관리자 개입 없이도 더 빠르게 생산성 높은 상태로 돌아갑니다. 관리자는 여전히 관련 이벤트를 확인하고 발생 후에 조사할 수 있습니다. 

## <a name="choosing-acceptable-risk-levels"></a>허용 가능한 위험 수준 선택

조직은 허용 가능한 위험 수준을 결정하여 사용자 환경과 보안 상태의 균형을 맞추어야 합니다. 

사용자 위험 정책 임계값을 **높음** 으로 설정하고 로그인 위험 정책을 **중간 이상** 으로 설정하는 것이 좋습니다.

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. 그러나 정책에서 **낮음** 및 **중간** 위험 검색을 제외하므로 손상된 ID를 악용하지 못하도록 공격자를 차단하지 못할 수 있습니다. **낮음** 임계값을 선택하면 추가적인 사용자 중단이 발생하지만 보안 상태가 강화됩니다.

## <a name="exclusions"></a>제외

모든 정책에서 [긴급 액세스 또는 비상 관리자 계정](../roles/security-emergency-access.md)과 같은 사용자를 제외할 수 있습니다. 조직은 계정이 사용되는 방식에 따라 사용자가 특정 정책에서 다른 계정을 제외하도록 결정할 수 있습니다. 모든 제외 항목을 정기적으로 검토하여 해당 항목이 계속 적용되는지 확인해야 합니다.

구성된 신뢰할 수 있는 [네트워크 위치](../conditional-access/location-condition.md)는 일부 위험 검색에서 Identity Protection이 가양성을 줄이는 데 사용됩니다.

## <a name="enable-policies"></a>정책 사용

사용자 위험 및 로그인 위험 정책을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory** > **보안** > **Identity Protection** > **개요** 로 이동합니다.
1. **사용자 위험 정책** 을 선택합니다.
   1. **할당** 에서
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자** 또는 **개인 및 그룹 선택** 중 선택할 수 있습니다.
         1. 원하는 경우 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **사용자 위험** 이 옵션을 **높음** 으로 설정하는 것이 Microsoft의 권장 사항입니다.
   1. **제어** 에서
      1. **액세스** - **액세스를 허용** 하고 **암호 변경을 요청** 하는 것이 Microsoft의 권장 사항입니다.
   1. **정책 적용** - **설정**
   1. **저장** - 이 작업을 수행하면 **개요** 페이지로 돌아갑니다.
1. **로그인 위험 정책 설정** 을 선택합니다.
   1. **할당** 에서
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자** 또는 **개인 및 그룹 선택** 중 선택할 수 있습니다.
         1. 원하는 경우 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **로그인 위험** 이 옵션을 **보통 이상** 으로 설정하는 것이 Microsoft의 권장 사항입니다.
   1. **제어** 에서
      1. **액세스** - **액세스를 허용** 하고 **다단계 인증을 요청** 하는 것이 Microsoft의 권장 사항입니다.
   1. **정책 적용** - **설정**
   1. **저장**

## <a name="next-steps"></a>다음 단계

- [Azure AD Multi-Factor Authentication 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [위험이란?](concept-identity-protection-risks.md)

- [위험 검색 조사](howto-identity-protection-investigate-risk.md)

- [위험 검색 시뮬레이션](howto-identity-protection-simulate-risk.md)
