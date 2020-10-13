---
title: Azure AD ID 보호 정책
description: ID 보호가 설정된 세 가지 정책 식별
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7587ed6d414a69cff67aca9446aebf6260c99fcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "83736503"
---
# <a name="identity-protection-policies"></a>ID 보호 정책

Azure Active Directory Identity Protection은 관리자가 사용하도록 설정할 수 있는 세 가지 기본 정책을 포함합니다. 이러한 정책에는 제한된 사용자 지정이 포함되지만 대부분의 조직에 적용됩니다. 모든 정책에서 [긴급 액세스 또는 비상 관리자 계정](../users-groups-roles/directory-emergency-access.md)과 같은 사용자를 제외할 수 있습니다.

![ID 보호 정책](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 등록 정책

ID 보호를 통해 조직에서는 로그인 시 등록하도록 요구하는 조건부 액세스 정책을 사용하여 Azure MFA(Multi-Factor Authentication)를 롤아웃할 수 있습니다. 이 정책을 사용하도록 설정하는 것은 조직의 새 사용자가 첫 날에 MFA에 등록했는지 확인하는 좋은 방법입니다. 다단계 인증은 ID 보호에서 제공되는 위험 이벤트에 대한 자체 수정 방법 중 하나입니다. 자동 수정 기능을 사용하면 사용자가 자체적으로 조치를 취할 수 있으므로 기술 지원팀 문의 전화 횟수를 줄일 수 있습니다.

Azure Multi-Factor Authentication에 대한 자세한 내용은 [작동 방식: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md) 문서를 참조하세요

## <a name="sign-in-risk-policy"></a>로그인 위험 정책

ID 보호는 각 로그인의 신호를 실시간 및 오프라인으로 분석하고 사용자가 로그인하지 못할 확률을 기준으로 위험 점수를 계산합니다. 관리자는 조직의 요구 사항을 적용하기 위해 이 위험 점수 신호를 기준으로 결정을 내릴 수 있습니다. 관리자는 액세스를 차단하거나, 액세스를 허용하거나, 다단계 인증이 필요한 액세스를 허용하도록 선택할 수 있습니다.

위험이 감지되면 사용자는 다단계 인증을 수행하여 위험한 로그인 이벤트를 자동으로 수정하고 닫을 수 있으므로 관리자에게 불필요한 업무 부담이 발생하지 않게 됩니다.

> [!NOTE] 
> 로그인 위험 정책을 트리거하기 전에 사용자는 Azure Multi-Factor Authentication에 등록되어 있어야 합니다.

### <a name="custom-conditional-access-policy"></a>사용자 지정 조건부 액세스 정책

또한 관리자는 로그인 위험을 할당 조건으로 포함하여 사용자 지정 조건부 액세스 정책을 만들도록 선택할 수 있습니다. 조건부 액세스 정책의 조건에 해당하는 위험에 대한 자세한 내용은 [조건부 액세스: 조건](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) 문서에서 찾을 수 있습니다.

![사용자 지정 조건부 액세스 로그인 위험 정책](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>사용자 위험 정책

ID 보호는 사용자의 정상적인 행동을 계산하고 해당 결과를 사용해서 위험에 대한 결정을 내릴 수 있습니다. 사용자 위험은 ID가 손상된 확률을 계산한 결과입니다. 관리자는 조직의 요구 사항을 적용하기 위해 이 위험 점수 신호를 기준으로 결정을 내릴 수 있습니다. 관리자는 [Azure AD 셀프 서비스 암호 재설정](../authentication/howto-sspr-deployment.md)을 사용하여 액세스를 차단하거나, 액세스를 허용하거나, 액세스를 허용하면서 암호 변경을 요구하도록 선택할 수 있습니다.

위험이 감지되면 사용자는 셀프 서비스 암호 재설정을 수행하여 사용자 위험 이벤트를 자동으로 수정하고 닫을 수 있으므로 관리자에게 불필요한 업무 부담이 발생하지 않게 됩니다.

> [!NOTE] 
> 사용자는 사용자 위험 정책을 트리거하기 전에 셀프 서비스 암호 재설정에 이미 등록되어 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)

- [Azure Multi-Factor Authentication 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
