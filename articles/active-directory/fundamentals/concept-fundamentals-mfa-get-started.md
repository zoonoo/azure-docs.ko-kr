---
title: 조직의 Azure AD Multi-Factor Authentication-Azure Active Directory
description: 라이선스 모델을 기반으로 조직에 대 한 Azure AD Multi-Factor Authentication의 사용 가능한 기능에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94836745"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>조직의 Azure AD Multi-Factor Authentication 개요

조직에서 소유 하는 라이선스에 따라 AD (Azure Active Directory) 사용자에 대해 Azure AD Multi-Factor Authentication를 사용 하도록 설정 하는 방법에는 여러 가지가 있습니다. 

![신호 조사 및 필요한 경우 MFA 적용](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Microsoft의 연구에 따르면, MFA(다단계 인증)를 사용하는 경우 계정이 99.9% 이상 손상될 가능성이 적습니다.

이것이 통계적 수치가 되기 전에 조직에서 무료이더라도 MFA를 켜려면 어떻게 해야 하나요?

## <a name="free-option"></a>무료 옵션

Azure AD의 무료 혜택을 활용하는 고객은 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md)을 사용하여 환경에서 다단계 인증을 사용하도록 설정할 수 있습니다.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 또는 E5

Microsoft 365 있는 고객의 경우 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* 모든 로그인 이벤트에 대해 Azure AD Multi-Factor Authentication는 모든 사용자에 대해 사용 되거나 사용 하지 않도록 설정 됩니다. 사용자의 일부에 대해서만 또는 특정 시나리오에서만 다단계 인증을 사용하도록 설정하는 기능은 없습니다. Office 365 포털을 통해 관리할 수 있습니다. 
* 향상된 사용자 환경을 위해 Azure AD Premium P1 또는 P2로 업그레이드하고 조건부 액세스를 사용합니다. 자세한 내용은 multi-factor authentication을 사용 하 여 리소스 Microsoft 365 보안을 참조 하세요.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Enterprise Mobility + Security E3, Microsoft 365 F1 또는 Microsoft 365 E3와 같이 이 기능을 포함하는 Azure AD Premium P1 또는 유사한 라이선스를 사용하는 고객의 경우: 

[Azure AD 조건부 액세스](../authentication/tutorial-enable-azure-mfa.md)를 사용하여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 사용자에게 다단계 인증을 사용할지 묻는 메시지를 표시합니다.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Enterprise Mobility + Security E5 또는 Microsoft 365 F5와 같이 이 기능을 포함하는 Azure AD Premium P2 또는 유사한 라이선스를 사용하는 고객의 경우: 

가장 강력한 보안 위치 및 향상된 사용자 환경을 제공합니다. Azure AD Premium P1 기능에 사용자의 패턴에 맞게 조정되고 다단계 인증 프롬프트를 최소화하는 [위험 기반 조건부 액세스](../conditional-access/howto-conditional-access-policy-risk.md)를 추가합니다.

## <a name="authentication-methods"></a>인증 방법

| 메서드 | 보안 기본값 | 다른 모든 방법 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |   | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |

## <a name="next-steps"></a>다음 단계

시작 하려면 [AZURE AD Multi-Factor Authentication를 사용 하 여 사용자 로그인 이벤트를 보호](../authentication/tutorial-enable-azure-mfa.md)하기 위한 자습서를 참조 하세요.

라이선스에 대 한 자세한 내용은 [AZURE AD Multi-Factor Authentication의 기능 및 라이선스](../authentication/concept-mfa-licensing.md)를 참조 하세요.
