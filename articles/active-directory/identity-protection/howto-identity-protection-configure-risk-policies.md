---
title: 위험 정책 - Azure Active 디렉터리 ID 보호
description: Azure Active 디렉터리 ID 보호에서 위험 정책 활성화 및 구성
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707008"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>방법: 위험 정책 구성 및 사용

이전 문서에서 배운 것처럼 [ID 보호 정책에는](concept-identity-protection-policies.md) 디렉터리에서 사용할 수 있는 두 가지 위험 정책이 있습니다. 

- 로그인 위험 정책
- 사용자 위험 정책

![사용자 및 로그인 위험 정책을 사용하도록 설정하는 보안 개요 페이지](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

두 정책 모두 사용자 환경의 위험 감지에 대한 응답을 자동화하고 위험이 감지되면 사용자가 자체 수정할 수 있도록 합니다. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>사전 요구 사항 

조직에서 위험이 감지될 때 사용자가 자체 수정할 수 있도록 허용하려면 셀프 서비스 암호 재설정 및 Azure 다단계 인증 모두에 대해 사용자를 등록해야 합니다. 최상의 환경을 위해 [통합된 보안 정보 등록 환경을 사용하도록 설정하는](../authentication/howto-registration-mfa-sspr-combined.md) 것이 좋습니다. 사용자가 자체 수정할 수 있도록 허용하면 관리자의 개입 없이 생산성이 더 빠르게 다시 향상됩니다. 관리자는 여전히 이러한 이벤트를 보고 사실 이후에 조사할 수 있습니다. 

## <a name="choosing-acceptable-risk-levels"></a>허용 가능한 위험 수준 선택

조직은 사용자 경험과 보안 태세의 균형을 맞출 수 있는 위험 수준을 결정해야 합니다. 

Microsoft의 권장 사항은 사용자 위험 정책 임계값을 **높음으로** 설정하고 로그인 위험 정책을 중간 이상으로 설정하는 **것입니다.**

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. 그러나 공격자가 손상된 ID를 악용하는 것을 차단하지 않을 수 있는 정책에서 **낮음** 및 **중간** 위험 검색을 제외합니다. **낮은** 임계값을 선택하면 추가 사용자 인터럽트가 발생하지만 보안 태세가 향상됩니다.

## <a name="exclusions"></a>제외

모든 정책은 비상 액세스 또는 브레이크 [글래스 관리자 계정과](../users-groups-roles/directory-emergency-access.md)같은 사용자를 제외할 수 있습니다. 조직에서는 계정 사용 방식에 따라 특정 정책에서 다른 계정을 제외해야 한다고 결정할 수 있습니다. 모든 제외 사항은 여전히 적용 가능한지 확인하기 위해 정기적으로 검토해야 합니다.

구성된 신뢰할 수 있는 [네트워크 위치는](../conditional-access/location-condition.md) 일부 위험 검색에서 ID 보호에 의해 사용되어 오탐지를 줄입니다.

## <a name="enable-policies"></a>정책 사용

사용자 위험 및 로그인 위험 정책을 활성화하려면 다음 단계를 완료합니다.

1. [Azure 포털로](https://portal.azure.com)이동합니다.
1. Azure **Active 디렉터리** > **보안** > **ID 보호** > **개요로**이동합니다.
1. **사용자 위험 정책 구성을**선택합니다.
   1. **과제** 중
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자를** 선택하거나 개인 및 **그룹을 선택합니다.**
         1. 선택적으로 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **사용자 위험** 마이크로 소프트의 권장 사항은 **높은으로이**옵션을 설정하는 것입니다 .
   1. **언더 컨트롤**
      1. **액세스** - Microsoft의 권장 사항은 **액세스를 허용하고** **암호를 변경해야 하는**것입니다.
   1. **정책** - **적용**
   1. **저장** - 이 작업은 **개요** 페이지로 돌아갑니다.
1. **로그인 위험 정책 구성을 선택합니다.**
   1. **과제** 중
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자를** 선택하거나 개인 및 **그룹을 선택합니다.**
         1. 선택적으로 정책에서 사용자를 제외하도록 선택할 수 있습니다.
      1. **조건** - **로그인 위험** Microsoft의 권장 사항은 이 옵션을 중간 **이상으로**설정하는 것입니다.
   1. **언더 컨트롤**
      1. **액세스** - Microsoft의 권장 사항은 **액세스를 허용하고** **다단계 인증을 요구하는 것입니다.**
   1. **정책** - **적용**
   1. **저장**

## <a name="next-steps"></a>다음 단계

- [Azure 다단계 인증 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [위험이란?](concept-identity-protection-risks.md)

- [위험 검색 조사](howto-identity-protection-investigate-risk.md)

- [위험 탐지 시뮬레이션](howto-identity-protection-simulate-risk.md)
