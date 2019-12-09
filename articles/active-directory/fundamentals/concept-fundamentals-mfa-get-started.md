---
title: 조직에서 Multi-Factor Authentication 사용-Azure Active Directory
description: 라이선스에 따라 조직에 Azure MFA를 사용 하도록 설정
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932451"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>조직에서 Multi-Factor Authentication 사용

조직에서 소유 하는 라이선스에 따라 AD (Azure Active Directory) 사용자에 대해 MFA (Azure Multi-Factor Authentication)를 사용 하도록 설정 하는 방법에는 여러 가지가 있습니다. 

![신호 조사 및 필요한 경우 MFA 적용](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Microsoft의 연구에 따라 MFA를 사용 하는 경우 계정이 99.9% 이상 손상 될 가능성이 적습니다.

따라서 조직에서 통계를 만들기 전에 multi-factor authentication을 무료로 켜는 방법

## <a name="free-option"></a>Free 옵션

Azure AD의 무료 혜택을 활용 하는 고객은 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md) 을 사용 하 여 환경에서 multi-factor authentication을 사용 하도록 설정할 수 있습니다.

## <a name="office-365"></a>PowerApps

Office 365를 사용 하는 고객의 경우 다음 두 가지 옵션을 사용할 수 있습니다.

- Azure AD를 통해 [보안 기본값](concept-fundamentals-security-defaults.md) 을 사용 하도록 설정 하 여 모든 사용자를 azure Multi-Factor Authentication로 보호할 수 있습니다.
- 조직에서 multi-factor authentication을 제공 하는 데 더 많은 세분성이 필요한 경우 Office 라이선스에는 사용자별 [MFA](../authentication/howto-mfa-userstates.md) 기능이 포함 됩니다. 사용자별 MFA는 관리자가 개별적으로 사용 하도록 설정 하 고 각 사용자에 게 적용 됩니다.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Enterprise Mobility + Security E3, Microsoft 365 F1 또는 Microsoft 365 E3와 같이이 기능을 포함 하는 Azure AD Premium P1 또는 유사한 라이선스를 사용 하는 고객의 경우: 

최상의 사용자 환경에서 [조건부 액세스 정책을](../conditional-access/concept-conditional-access-policy-common.md) 사용 하는 것이 좋습니다.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Enterprise Mobility + Security E5 또는 Microsoft 365 E5와 같이이 기능을 포함 하는 Azure AD Premium P2 또는 유사한 라이선스를 사용 하는 고객의 경우: 

최상의 사용자 환경 및 적용 유연성을 위해 [Id 보호](../identity-protection/overview-v2.md) 위험 정책과 함께 [조건부 액세스 정책을](../conditional-access/concept-conditional-access-policy-common.md) 사용 하는 것이 좋습니다.

## <a name="authentication-methods"></a>인증 방법

|   | 보안 기본값 | 다른 모든 메서드 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |   | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |
| 앱 암호 |   | X * * |

\* * 앱 암호는 관리자가 사용 하도록 설정한 경우에만 레거시 인증 시나리오를 사용 하 여 사용자별 MFA에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/)
