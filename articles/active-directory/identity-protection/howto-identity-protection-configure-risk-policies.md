---
title: 위험 정책-Azure Active Directory Identity Protection
description: Azure Active Directory Identity Protection에서 위험 정책 사용 및 구성
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37091b2551d68e241c7179949c3eb1db9a381de6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382171"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>방법: 위험 정책 구성 및 사용

이전 문서에서 설명한 대로 [Id 보호 정책](concept-identity-protection-policies.md) 에는 디렉터리에서 사용할 수 있는 두 가지 위험 정책이 있습니다. 

- 로그인 위험 정책
- 사용자 위험 정책

![사용자 및 로그인 위험 정책을 사용 하도록 설정 하는 보안 개요 페이지](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

두 정책은 모두 사용자 환경의 위험 검색에 대 한 응답을 자동화 하 고 사용자가 위험이 검색 되 면 스스로를 수정할 수 있도록 하는 데 사용 됩니다. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>선행 조건 

사용자가 위험 검색 시 자체 재구성을 허용 하려는 경우 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication 모두에 대해 사용자를 등록 해야 합니다. 최상의 환경을 위해 [결합 된 보안 정보 등록 환경을 사용 하는](../authentication/howto-registration-mfa-sspr-combined.md) 것이 좋습니다. 사용자가 직접 수정할 수 있도록 하면 관리자의 개입 없이도 신속 하 게 생산적인 상태로 다시 전환할 수 있습니다. 관리자는 이러한 이벤트를 계속 확인 하 고 팩트 후에 조사할 수 있습니다. 

## <a name="choosing-acceptable-risk-levels"></a>허용 가능한 위험 수준 선택

조직은 사용자 환경 및 보안 상태를 분산 하는 데 사용할 위험 수준을 결정 해야 합니다. 

Microsoft는 사용자 위험 정책 임계값을 **높음** 으로, 로그인 위험 정책을 **중간 이상**으로 설정 하는 것이 좋습니다.

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. 그러나 정책에서 **낮은** 및 **중간** 위험 검색은 제외 되므로 공격자가 손상 된 id를 악용 하지 못하도록 차단 하지 못할 수 있습니다. **낮은** 임계값을 선택 하면 추가 사용자 인터럽트가 도입 되지만 보안 상태가 향상 됩니다.

## <a name="exclusions"></a>제외

모든 정책을 사용 하면 사용자가 [응급 액세스 또는](../users-groups-roles/directory-emergency-access.md)사용자에 게 투명 한 관리자 계정과 같은 사용자를 제외할 수 있습니다. 조직에서 계정이 사용 되는 방식에 따라 특정 정책에서 다른 계정을 제외 해야 하는지 결정할 수 있습니다. 모든 제외를 정기적으로 검토 하 여 해당 사항이 여전히 적용 되는지 확인 해야 합니다.

## <a name="enable-policies"></a>정책 사용

사용자 위험 및 로그인 위험 정책을 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory** > **보안** > **id 보호** > **개요**로 이동 합니다.
1. **사용자 위험 정책 구성**을 선택 합니다.
   1. **할당** 아래
      1. **사용자** -출시를 제한 하는 경우 **모든 사용자** 를 선택 하거나 **개인 및 그룹을 선택** 합니다.
         1. 필요에 따라 정책에서 사용자를 제외 하도록 선택할 수 있습니다.
      1. **조건** - **사용자 위험** Microsoft의 권장 사항은이 옵션을 **높음**으로 설정 하는 것입니다.
   1. **컨트롤** 에서
      1. **액세스** - **액세스를 허용** 하 고 **암호 변경을 요구**하는 것이 Microsoft의 권장 사항입니다.
   1. **정책** - 적용
   1. **저장** -이 작업을 수행 하면 **개요** 페이지로 돌아갑니다.
1. **로그인 위험 정책 구성**을 선택 합니다.
   1. **할당** 아래
      1. **사용자** -출시를 제한 하는 경우 **모든 사용자** 를 선택 하거나 **개인 및 그룹을 선택** 합니다.
         1. 필요에 따라 정책에서 사용자를 제외 하도록 선택할 수 있습니다.
      1. **조건** - **로그인 위험** Microsoft의 권장 사항은이 옵션을 **중간 이상**으로 설정 하는 것입니다.
   1. **컨트롤** 에서
      1. **액세스** - **액세스를 허용** 하 고 **multi-factor authentication을 요구**하는 것이 Microsoft의 권장 사항입니다.
   1. **정책** - 적용
   1. **저장**

## <a name="next-steps"></a>다음 단계

- [Azure Multi-Factor Authentication 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [위험이란?](concept-identity-protection-risks.md)

- [위험 검색 조사](howto-identity-protection-investigate-risk.md)

- [위험 감지 시뮬레이션](howto-identity-protection-simulate-risk.md)
