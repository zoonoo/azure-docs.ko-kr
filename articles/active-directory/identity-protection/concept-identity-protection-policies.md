---
title: Azure AD ID 보호 정책
description: ID 보호를 사용할 수 있는 세 가지 정책 식별
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
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887417"
---
# <a name="identity-protection-policies"></a>ID 보호 정책

Azure Active Directory ID 보호에는 관리자가 사용하도록 선택할 수 있는 세 가지 기본 정책이 포함되어 있습니다. 이러한 정책에는 제한된 사용자 지정이 포함되지만 대부분의 조직에 적용할 수 있습니다. 모든 정책은 비상 액세스 또는 브레이크 [글래스 관리자 계정과](../users-groups-roles/directory-emergency-access.md)같은 사용자를 제외할 수 있습니다.

![ID 보호 정책](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 등록 정책

ID 보호는 조직에서 로그인 시 등록이 필요한 조건부 액세스 정책을 사용하여 MFA(Azure 다단계 인증)를 롤아웃하는 데 도움이 될 수 있습니다. 이 정책을 사용하도록 설정하는 것은 조직의 새 사용자가 첫날에 MFA에 등록하도록 하는 좋은 방법입니다. 다단계 인증은 ID 보호 내의 위험 이벤트에 대한 자체 수정 방법 중 하나입니다. 자체 수정을 통해 사용자는 스스로 조치를 취하여 헬프 데스크 통화량을 줄일 수 있습니다.

Azure 다단계 인증에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](../authentication/concept-mfa-howitworks.md)

## <a name="sign-in-risk-policy"></a>로그인 위험 정책

ID 보호는 각 로그인의 신호를 실시간 및 오프라인으로 분석하고 사용자가 로그인을 수행하지 않았을 확률을 기반으로 위험 점수를 계산합니다. 관리자는 이 위험 점수 신호를 기반으로 조직의 요구 사항을 적용하여 결정을 내릴 수 있습니다. 관리자는 액세스를 차단하거나, 액세스를 허용하거나, 액세스를 허용하지만 다단계 인증이 필요할 수 있습니다.

위험이 감지되면 사용자는 다단계 인증을 수행하여 자체 수정하고 위험한 로그인 이벤트를 닫아 관리자에게 불필요한 소음을 방지할 수 있습니다.

> [!NOTE] 
> 로그인 위험 정책을 트리거하기 전에 사용자가 이전에 Azure 다단계 인증에 등록해야 합니다.

### <a name="custom-conditional-access-policy"></a>사용자 지정 조건부 액세스 정책

관리자는 할당 조건으로 로그인 위험을 포함하여 사용자 지정 조건부 액세스 정책을 만들도록 선택할 수도 있습니다. 조건부 액세스에 대한 자세한 내용은 문서에서 찾을 수 [있습니다.](../conditional-access/overview.md)

![사용자 지정 조건부 액세스 로그인 위험 정책](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>사용자 위험 정책

ID 보호는 사용자의 행동에 대해 정상이라고 판단되는 것을 계산하고 이를 사용하여 위험에 대한 결정을 내릴 수 있습니다. 사용자 위험은 ID가 손상되었을 확률을 계산하는 것입니다. 관리자는 이 위험 점수 신호를 기반으로 조직의 요구 사항을 적용하여 결정을 내릴 수 있습니다. 관리자는 액세스를 차단하거나, 액세스를 허용하거나, 액세스를 허용하도록 선택할 수 있지만 [Azure AD 셀프 서비스 암호 재설정을](../authentication/howto-sspr-deployment.md)사용하여 암호를 변경해야 합니다.

위험이 감지되면 사용자는 셀프 서비스 암호 재설정을 수행하여 자체 수정을 수행하고 사용자 위험 이벤트를 닫아 관리자에게 불필요한 소음을 방지할 수 있습니다.

> [!NOTE] 
> 사용자는 사용자 위험 정책을 트리거하기 전에 셀프 서비스 암호 재설정에 이전에 등록해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)

- [Azure 다단계 인증 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
