---
title: Azure AD ID 보호를 사용한 사용자 환경
description: Azure AD ID 보호의 사용자 환경
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
ms.openlocfilehash: cc10fb4f9894a355c9eed024ae9f87747214999b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72887001"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD ID 보호를 사용한 사용자 환경

Azure Active Directory ID 보호를 사용하여 다음을 수행할 수 있습니다.

* 사용자가 MFA(Azure 다단계 인증)에 등록하도록 요구합니다.
* 위험한 로그인 및 손상된 사용자 수정 자동화

모든 ID 보호 정책은 사용자의 로그인 환경에 영향을 미칩니다. 사용자가 Azure MFA 및 셀프 서비스 암호 재설정과 같은 도구를 등록하고 사용할 수 있도록 허용하면 영향을 줄 수 있습니다. 이러한 도구는 적절한 정책 선택과 함께 필요할 때 자체 수정 옵션을 제공합니다.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication 등록

다단계 인증 등록을 요구하고 모든 사용자를 대상으로 하는 ID 보호 정책을 사용하면 Azure MFA를 사용하여 나중에 자체 수정할 수 있습니다. 이 정책을 구성하면 사용자가 등록을 선택할 수 있는 14일의 기간이 제공되며, 결국 에는 강제로 등록할 수 있습니다. 사용자에 대한 환경은 아래에 설명되어 있습니다. 자세한 내용은 문서의 최종 사용자 설명서, [2단계 확인 개요 및 직장 또는 학교 계정에서](../user-help/user-help-two-step-verification-overview.md)확인할 수 있습니다.

### <a name="registration-interrupt"></a>등록 인터럽트

1. Azure AD 통합 응용 프로그램에 로그인할 때 사용자는 다단계 인증을 위해 계정을 설정해야 하는 요구 사항에 대한 알림을 받습니다. 이 정책은 새 장치를 가진 새 사용자를 위한 Windows 10 즉시 사용할 수 있는 환경에서도 트리거됩니다.
   
    ![추가 정보 필요](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Azure 다단계 인증에 등록하고 로그인을 완료하는 안내 단계를 완료합니다.

## <a name="risky-sign-in-remediation"></a>위험한 로그인 수정

관리자가 로그인 위험에 대한 정책을 구성하면 영향을 받는 사용자에게 로그인을 시도하고 정책 위험 수준을 트리거할 때 알림을 받습니다. 

### <a name="risky-sign-in-self-remediation"></a>위험한 로그인 자체 수정

1. 사용자는 새 위치, 디바이스 또는 앱에서 로그인과 같은 해당 로그인에 대해 비정상적인 점이 검색되었음을 통보받습니다.
   
    ![뭔가 특이한 프롬프트](./media/concept-identity-protection-user-experience/120.png)

1. 사용자는 이전에 등록된 메서드 중 하나를 사용하여 Azure MFA를 완료하여 자신의 ID를 증명해야 합니다. 

### <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단하도록 선택할 수 있습니다. 차단을 해제하려면 최종 사용자가 IT 직원에게 문의하거나 익숙한 위치 나 장치에서 로그인을 시도할 수 있습니다. 이 경우 다단계 인증을 수행하여 자체 수정을 수행할 수 있는 옵션이 아닙니다.

![로그인 위험 정책에 의해 차단됨](./media/concept-identity-protection-user-experience/200.png)

IT 직원은 사용자가 다시 로그인할 수 있도록 [사용자를 차단 해제](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) 섹션의 지침을 따를 수 있습니다.

## <a name="risky-user-remediation"></a>위험한 사용자 수정

사용자 위험 정책이 구성된 경우 사용자 위험 수준 손상 가능성을 충족하는 사용자는 로그인하기 전에 사용자 손상 복구 흐름을 거쳐야 합니다. 

### <a name="risky-user-self-remediation"></a>위험한 사용자 자체 수정

1. 사용자는 해당 계정 보안이 의심스러운 작업 또는 유출된 자격 증명으로 인해 위험에 노출되었다는 알림을 받습니다.
   
    ![재구성](./media/concept-identity-protection-user-experience/101.png)

1. 사용자는 이전에 등록된 메서드 중 하나를 사용하여 Azure MFA를 완료하여 자신의 ID를 증명해야 합니다. 
1. 마지막으로, 사용자는 다른 사람이 자신의 계정에 액세스 할 수 있기 때문에 셀프 서비스 암호 재설정을 사용하여 암호를 변경해야합니다.

## <a name="risky-sign-in-administrator-unblock"></a>위험한 로그인 관리자 차단 해제

관리자는 위험 수준에 따라 로그인 시 사용자를 차단하도록 선택할 수 있습니다. 차단을 해제하려면 최종 사용자가 IT 직원에게 문의해야 합니다. 다단계 인증 및 셀프 서비스 암호 재설정을 수행 하여 자체 수정이 이 경우 옵션이 아닙니다.

![사용자 위험 정책에 의해 차단됨](./media/concept-identity-protection-user-experience/104.png)

IT 직원은 사용자가 다시 로그인할 수 있도록 [사용자를 차단 해제](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) 섹션의 지침을 따를 수 있습니다.

## <a name="see-also"></a>참조

- [위험 해결 및 사용자 차단 해제](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md) 
