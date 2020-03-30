---
title: 조직에 대한 Azure 다단계 인증 - Azure Active Directory
description: 라이선스 모델을 기반으로 조직에 대해 사용 가능한 Azure 다단계 인증기능에 대해 알아보기
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530396"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>조직에 대한 Azure 다단계 인증 개요

조직에서 소유한 라이선스에 따라 Azure Active Directory(AD) 사용자에 대해 Azure 다단계 인증을 사용하도록 설정하는 방법에는 여러 가지가 있습니다. 

![신호 조사 및 필요한 경우 MFA 적용](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

당사의 연구에 따르면 MFA(다단계 인증)를 사용하는 경우 계정이 99.9% 이상 손상될 가능성이 낮습니다.

그렇다면 통계가 되기 전에 조직에서 MFA를 무료로 켜는 방법은 무엇입니까?

## <a name="free-option"></a>무료 옵션

Azure AD의 무료 이점을 활용하는 고객은 [보안 기본값을](../fundamentals/concept-fundamentals-security-defaults.md) 사용하여 해당 환경에서 다단계 인증을 활성화할 수 있습니다.

## <a name="office-365-business-premium-e3-or-e5"></a>오피스 365 비즈니스 프리미엄, E3 또는 E5

Office 365를 사용 하는 고객의 경우 두 가지 옵션이 있습니다.

* Azure 다단계 인증은 모든 로그인 이벤트에 대해 모든 사용자에 대해 활성화되거나 비활성화됩니다. 사용자의 하위 집합에 대해서만 또는 특정 시나리오에서만 다단계 인증을 사용하도록 설정할 수 있는 기능은 없습니다. 관리는 Office 365 포털을 통해 관리됩니다. 
* 향상된 사용자 환경을 위해 Azure AD 프리미엄 P1 또는 P2로 업그레이드하고 조건부 액세스를 사용합니다. 자세한 내용은 다단계 인증을 통해 안전한 Office 365 리소스를 참조하세요.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

엔터프라이즈 이동성 + 보안 E3, Microsoft 365 F1 또는 Microsoft 365 E3와 같은 기능을 포함하는 Azure AD Premium P1 또는 유사한 라이선스를 보유한 고객의 경우: 

[Azure AD 조건부 액세스를](../conditional-access/overview.md) 사용하여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 다단계 인증을 요청하는 사용자에게 메시지를 표시합니다.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

엔터프라이즈 이동성 + 보안 E5 또는 Microsoft 365 E5와 같은 이 기능을 포함하는 Azure AD 프리미엄 P2 또는 유사한 라이선스를 보유한 고객의 경우: 

가장 강력한 보안 위치와 향상된 사용자 환경을 제공합니다. 사용자의 패턴에 적응하고 다단계 인증 프롬프트를 최소화하는 Azure AD Premium P1 기능에 [위험 기반 조건부 액세스를](../conditional-access/howto-conditional-access-policy-risk.md) 추가합니다.

## <a name="authentication-methods"></a>인증 방법

|   | 보안 기본값 | 기타 모든 방법 |
| --- | --- | --- |
| 모바일 앱을 통한 알림 | X | X |
| 모바일 앱 또는 하드웨어 토큰의 확인 코드 |   | X |
| 휴대폰에 문자 메시지 전송 |   | X |
| 휴대폰에 전화 걸기 |   | X |

## <a name="next-steps"></a>다음 단계

시작하려면 [Azure 다단계 인증을 사용하여 사용자 로그인 이벤트를 보호하는](../authentication/tutorial-enable-azure-mfa.md)자습서를 참조하십시오.

라이선스에 대한 자세한 내용은 [Azure 다단계 인증에 대한 기능 및 라이선스를](../authentication/concept-mfa-licensing.md)참조하십시오.
