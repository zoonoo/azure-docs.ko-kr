---
title: 조직의 Azure Multi-Factor Authentication-Azure Active Directory
description: 라이선스 모델을 기반으로 조직에 대 한 Azure Multi-Factor Authentication의 사용 가능한 기능에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79530396"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>조직의 Azure Multi-Factor Authentication 개요

조직에서 소유 하는 라이선스에 따라 AD (Azure Active Directory) 사용자에 대해 Azure Multi-Factor Authentication를 사용 하도록 설정 하는 여러 가지 방법이 있습니다. 

![신호 조사 및 필요한 경우 MFA 적용](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Microsoft의 연구에 따라 MFA (multi-factor authentication)를 사용 하는 경우 계정이 99.9% 이상 손상 될 가능성이 적습니다.

따라서 조직에서 통계를 만들기 전에 MFA를 무료로 사용 하려면 어떻게 해야 하나요?

## <a name="free-option"></a>Free 옵션

Azure AD의 무료 혜택을 활용 하는 고객은 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md) 을 사용 하 여 환경에서 multi-factor authentication을 사용 하도록 설정할 수 있습니다.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 비즈니스 프리미엄, E3 또는 E5

Office 365를 사용 하는 고객의 경우 다음 두 가지 옵션을 사용할 수 있습니다.

* 모든 로그인 이벤트에 대해 Azure Multi-Factor Authentication는 모든 사용자에 대해 사용 하거나 사용 하지 않도록 설정 됩니다. 사용자의 하위 집합 또는 특정 시나리오 에서만 multi-factor authentication을 사용 하도록 설정할 수 있는 기능은 없습니다. Office 365 포털을 통해 관리할 수 있습니다. 
* 향상 된 사용자 환경을 위해 Azure AD Premium P1 또는 P2로 업그레이드 하 고 조건부 액세스를 사용 합니다. 자세한 내용은 multi-factor authentication을 사용 하 여 Office 365 리소스 보호를 참조 하세요.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Enterprise Mobility + Security E3, Microsoft 365 F1 또는 Microsoft 365 E3와 같이이 기능을 포함 하는 Azure AD Premium P1 또는 유사한 라이선스를 사용 하는 고객의 경우: 

[AZURE AD 조건부 액세스](../conditional-access/overview.md) 를 사용 하 여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 사용자에 게 multi-factor authentication을 요청 합니다.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Enterprise Mobility + Security E5 또는 Microsoft 365 E5와 같이이 기능을 포함 하는 Azure AD Premium P2 또는 유사한 라이선스를 사용 하는 고객의 경우: 

가장 강력한 보안 위치 및 향상 된 사용자 환경을 제공 합니다. 사용자의 패턴에 맞게 조정 되 고 multi-factor authentication 프롬프트를 최소화 하는 Azure AD Premium P1 기능에 [위험 기반 조건부 액세스](../conditional-access/howto-conditional-access-policy-risk.md) 를 추가 합니다.

## <a name="authentication-methods"></a>인증 방법

|   | 보안 기본값 | 다른 모든 메서드 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |   | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |

## <a name="next-steps"></a>다음 단계

시작 하려면 [Azure Multi-Factor Authentication를 사용 하 여 사용자 로그인 이벤트 보안](../authentication/tutorial-enable-azure-mfa.md)을 위한 자습서를 참조 하세요.

라이선스에 대 한 자세한 내용은 [Azure Multi-Factor Authentication의 기능 및 라이선스](../authentication/concept-mfa-licensing.md)를 참조 하세요.
