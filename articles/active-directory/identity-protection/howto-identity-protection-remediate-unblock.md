---
title: Azure AD ID 보호에서 위험 해결 및 차단 해제
description: 근접 활성 위험 탐지가 있는 옵션에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382108"
---
# <a name="remediate-risks-and-unblock-users"></a>위험 해결 및 사용자 차단 해제

[조사를](howto-identity-protection-investigate-risk.md)완료한 후에는 위험을 수정하거나 사용자의 차단을 해제하기 위한 조치를 취해야 합니다. 또한 조직은 [위험 정책을](howto-identity-protection-configure-risk-policies.md)사용하여 자동화된 수정을 사용하도록 설정할 수 있습니다. 조직은 조직에서 익숙한 기간에 표시되는 모든 위험 탐지를 종료해야 합니다. 위험으로 작업할 때 시간이 중요하기 때문에 가능한 한 빨리 이벤트를 닫는 것이 좋습니다.

## <a name="remediation"></a>재구성

모든 활성 위험 탐지는 사용자 위험 수준이라는 값을 계산하는 데 기여합니다. 사용자 위험 수준은 계정이 손상된 가능성에 대한 표시기(낮음, 보통, 높음)입니다. 관리자는 영향을 받는 사용자가 더 이상 위험에 노출되지 않도록 모든 위험 검색을 닫으려고 합니다.

이벤트가 더 이상 위험하다고 판단되지 않아 일부 위험 검색은 ID 보호에서 "닫힘(시스템)"으로 표시될 수 있습니다.

관리자는 다음 옵션을 수정할 수 있습니다.

- 위험 정책으로 자체 수정
- 수동 암호 다시 설정
- 사용자 위험 해제
- 개별 위험 탐지를 수동으로 닫습니다.

### <a name="self-remediation-with-risk-policy"></a>위험 정책으로 자체 수정

위험 정책에서 Azure 다단계 인증(MFA) 및 셀프 서비스 암호 재설정(SSPR)을 사용하여 사용자가 자체 수정하도록 허용하는 경우 위험이 감지되면 스스로 차단을 해제할 수 있습니다. 그런 다음 이러한 검색은 닫힌 것으로 간주됩니다. 위험이 감지될 때 사용하려면 Azure MFA 및 SSPR에 이전에 등록해야 합니다.

일부 검색에서는 사용자 자체 수정이 필요한 수준으로 위험을 높이지 않을 수 있지만 관리자는 이러한 검색을 평가해야 합니다. 관리자는 [위치에서 액세스를 차단하거나 정책에서](../conditional-access/howto-conditional-access-policy-location.md) 허용되는 위험을 낮추는 것과 같은 추가 조치가 필요하다고 판단할 수 있습니다.

### <a name="manual-password-reset"></a>수동 암호 다시 설정

사용자 위험 정책을 사용하여 암호를 재설정해야 하는 것이 옵션이 아닌 경우 관리자는 수동 암호 재설정을 사용하여 사용자의 모든 위험 검색을 닫을 수 있습니다.

관리자는 사용자의 암호를 재설정할 때 두 가지 옵션이 제공됩니다.

- **임시 암호 생성** - 임시 암호를 생성하여 ID를 안전한 상태로 즉시 전환할 수 있습니다. 이 방법을 사용하려면 임시 암호가 무엇인지 알아야 하기 때문에 영향을 받는 사용자에게 연락해야 합니다. 암호는 임시이므로 다음 로그인 중에 암호를 새 암호로 변경하라는 메시지가 표시됩니다.

- **사용자가 암호를 재설정해야 함** - 사용자가 암호를 재설정하도록 요구하면 지원 센터 또는 관리자에게 문의하지 않고도 셀프 복구가 가능합니다. 이 메서드는 Azure MFA 및 SSPR에 등록된 사용자에게만 적용됩니다. 등록되지 않은 사용자의 경우 이 옵션을 사용할 수 없습니다.

### <a name="dismiss-user-risk"></a>사용자 위험 해제

암호 재설정이 사용자에게 필요한 옵션이 아닌 경우 예를 들어 사용자가 삭제되었기 때문에 사용자 위험 검색을 해제하도록 선택할 수 있습니다.

**사용자 위험 해제를**클릭하면 모든 이벤트가 닫히고 영향을 받는 사용자가 더 이상 위험에 노출되지 않습니다. 그러나 이 방법은 기존 암호에 영향을 주지 않으므로 관련된 ID를 안전한 상태로 다시 전환하지 않습니다. 

### <a name="close-individual-risk-detections-manually"></a>개별 위험 탐지를 수동으로 닫습니다.

개별 위험 검색을 수동으로 닫을 수 있습니다. 위험 검색을 수동으로 닫으면 사용자 위험 수준을 낮출 수 있습니다. 일반적으로 위험 검색은 관련 조사에 대한 응답으로 수동으로 닫힙입니다. 예를 들어 사용자와 대화할 때 활성 위험 검색이 더 이상 필요하지 않음을 알 수 있습니다. 
 
위험 검색을 수동으로 닫을 때 다음 작업 중 하나를 선택하여 위험 검색 상태를 변경할 수 있습니다.

- 사용자 손상 확인
- 사용자 위험 해제
- 로그인 금고 확인
- 로그인 손상 확인

## <a name="unblocking-users"></a>사용자 차단 해제

관리자는 위험 정책 또는 조사에 따라 로그인을 차단하도록 선택할 수 있습니다. 블록은 로그인 또는 사용자 위험에 따라 발생할 수 있습니다.

### <a name="unblocking-based-on-user-risk"></a>사용자 위험에 따라 차단 해제

사용자 위험으로 인해 차단된 계정의 차단을 해제하려면 관리자는 다음과 같은 옵션을 제공합니다.

1. **암호 재설정** - 사용자의 암호를 다시 설정할 수 있습니다.
1. **사용자 위험 해제** - 액세스를 차단하기 위해 구성된 사용자 위험 수준에 도달하면 사용자 위험 정책이 사용자를 차단합니다. 사용자 위험을 해제하거나 보고된 위험 검색을 수동으로 닫아 사용자의 위험 수준을 줄일 수 있습니다.
1. **정책에서 사용자 제외** - 로그인 정책의 현재 구성으로 인해 특정 사용자에게 문제가 발생했다고 생각되면 사용자를 제외할 수 있습니다. 자세한 내용은 요소: 구성 및 사용 위험 정책에서 제외 섹션을 [참조하세요.](howto-identity-protection-configure-risk-policies.md#exclusions)
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 방법: 구성 및 위험 정책 사용 문서를 [참조하세요.](howto-identity-protection-configure-risk-policies.md)

### <a name="unblocking-based-on-sign-in-risk"></a>로그인 위험에 따른 차단 해제

로그인 위험에 따라 계정 차단을 해제하려면 관리자는 다음과 같은 옵션을 제공합니다.

1. **친숙한 위치 또는 디바이스에서 로그인** - 차단된 의심스러운 로그인에 대한 일반적인 이유는 알 수 없는 위치 또는 디바이스에서의 로그인 시도입니다. 사용자는 익숙한 위치 나 장치에서 로그인을 시도하여 이러한 이유가 차단 이유인지 여부를 신속하게 확인할 수 있습니다.
1. **정책에서 사용자 제외** - 로그인 정책의 현재 구성으로 인해 특정 사용자에게 문제가 발생했다고 생각되면 사용자를 제외할 수 있습니다. 자세한 내용은 요소: 구성 및 사용 위험 정책에서 제외 섹션을 [참조하세요.](howto-identity-protection-configure-risk-policies.md#exclusions)
1. **정책 비활성화** - 정책 구성이 모든 사용자에 대해 문제를 일으킨다고 생각하는 경우 정책을 비활성화할 수 있습니다. 자세한 내용은 방법: 구성 및 위험 정책 사용 문서를 [참조하세요.](howto-identity-protection-configure-risk-policies.md)

## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview-identity-protection.md)를 참조하세요.
