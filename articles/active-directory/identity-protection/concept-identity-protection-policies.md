---
title: Azure AD ID 보호 정책
description: Id 보호를 사용 하도록 설정 된 세 가지 정책 식별
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
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "72887417"
---
# <a name="identity-protection-policies"></a>ID 보호 정책

Azure Active Directory Identity Protection는 관리자가 사용 하도록 선택할 수 있는 세 가지 기본 정책을 포함 합니다. 이러한 정책에는 제한 된 사용자 지정이 포함 되지만 대부분의 조직에 적용 됩니다. 모든 정책을 사용 하면 사용자가 [응급 액세스 또는](../users-groups-roles/directory-emergency-access.md)사용자에 게 투명 한 관리자 계정과 같은 사용자를 제외할 수 있습니다.

![ID 보호 정책](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA 등록 정책

Id 보호를 통해 조직에서는 로그인 시 등록 해야 하는 조건부 액세스 정책을 사용 하 여 MFA (Azure Multi-Factor Authentication)를 롤아웃할 수 있습니다. 이 정책을 사용 하도록 설정 하는 것은 조직의 새 사용자가 처음으로 MFA에 등록 했는지 확인 하는 좋은 방법입니다. Multi-factor authentication은 Id 보호 내에서 위험 이벤트에 대 한 자체 수정 방법 중 하나입니다. 자동 수정 기능을 사용 하면 사용자가 기술 지원팀 통화 볼륨을 줄이기 위해 스스로 조치를 취할 수 있습니다.

Azure Multi-Factor Authentication에 대 한 자세한 내용은 [작동 방법: azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)문서에서 찾을 수 있습니다.

## <a name="sign-in-risk-policy"></a>로그인 위험 정책

Id 보호는 각 로그인의 신호를 실시간 및 오프 라인으로 분석 하 고 사용자가 로그인을 수행할 확률을 기준으로 위험 점수를 계산 합니다. 관리자는 조직의 요구 사항을 적용 하기 위해이 위험 점수 신호를 기반으로 결정을 내릴 수 있습니다. 관리자는 액세스를 차단 하거나 액세스를 허용 하거나 multi-factor authentication이 필요한 액세스를 허용 하도록 선택할 수 있습니다.

위험이 감지 되 면 사용자는 다단계 인증을 수행 하 여 위험한 로그인 이벤트를 자동으로 재구성 하 고 닫을 수 있으므로 관리자가 불필요 한 의미를 방지할 수 있습니다.

> [!NOTE] 
> 로그인 위험 정책을 트리거하기 전에 사용자가 이전에 Azure Multi-Factor Authentication에 등록 되어 있어야 합니다.

### <a name="custom-conditional-access-policy"></a>사용자 지정 조건부 액세스 정책

또한 관리자는 할당 조건으로 로그인 위험을 포함 하 여 사용자 지정 조건부 액세스 정책을 만들도록 선택할 수 있습니다. 조건부 액세스에 대 한 자세한 내용은 [조건부 액세스](../conditional-access/overview.md) 란 문서에서 찾을 수 있습니다.

![사용자 지정 조건부 액세스 로그인 위험 정책](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>사용자 위험 정책

Id 보호는 사용자의 행동에 대해 정상적인 것으로 간주 되는 것을 계산 하 고이를 사용 하 여 위험에 대 한 결정을 내릴 수 있습니다. 사용자 위험은 id가 손상 된 확률을 계산 하는 것입니다. 관리자는 조직의 요구 사항을 적용 하기 위해이 위험 점수 신호를 기반으로 결정을 내릴 수 있습니다. 관리자는 액세스를 차단 하거나 액세스를 허용 하거나 [AZURE AD 셀프 서비스 암호 재설정](../authentication/howto-sspr-deployment.md)을 사용 하 여 암호 변경을 요구 하도록 선택할 수 있습니다.

위험이 감지 되 면 사용자는 셀프 서비스 암호 재설정을 수행 하 여 자동으로 재구성 하 고 사용자 위험 이벤트를 닫아 관리자의 불필요 한 소음을 방지할 수 있습니다.

> [!NOTE] 
> 사용자가 사용자 위험 정책을 트리거하기 전에 셀프 서비스 암호 재설정을 위해 이미 등록 되어 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)

- [Azure Multi-Factor Authentication 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)
