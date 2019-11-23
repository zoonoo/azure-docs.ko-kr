---
title: How it works Azure MFA - Azure Active Directory
description: Azure Multi-Factor Authentication은 간편한 로그인 프로세스를 원하는 사용자의 요구를 충족시키는 동시에 데이터와 애플리케이션에 대한 액세스 보호를 지원합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1036d7e8aef29e3185452d5088e660d474726e4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381966"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>작동 방법: Azure Multi-Factor Authentication

2단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 추가 인증 메서드가 없다면 소용이 없습니다. 이러한 인증에서는 다음 중 두 가지 이상의 인증 메서드를 요구합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 디바이스(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 신원 정보(생체 인식)

<center>

![Conceptual authentication methods image](./media/concept-mfa-howitworks/methods.png)</center>

사용자에 대해 단순성을 유지하는 동안 Azure MFA(Multi-Factor Authentication)를 통해 데이터와 애플리케이션에 대한 액세스를 보호할 수 있습니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 사용하기 쉬운 다양한 [인증 방법](concept-authentication-methods.md)을 통해 강력한 인증을 제공합니다. 관리자가 결정한 구성에 따라 사용자에게 MFA 챌린지가 표시될 수도 있고 그렇지 않을 수도 있습니다.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication을 가져오는 방법

Multi-Factor Authentication은 다음과 같은 제품의 일부로 제공됩니다.

* **Azure Active Directory Premium** or **Microsoft 365 Business** - Full featured use of Azure Multi-Factor Authentication using Conditional Access policies to require multi-factor authentication.

* **Azure AD Free** or standalone **Office 365** licenses - Use pre-created [Conditional Access baseline protection policies](../conditional-access/concept-baseline-protection.md) to require multi-factor authentication for your users and administrators.

* **Azure Active Directory 전역 관리자** - Azure Multi-factor Authentication 기능의 하위 집합을 전역 관리자 계정을 보호하는 수단으로 사용할 수 있습니다.

> [!NOTE]
> 신규 고객은 2018년 9월 1일부터 제공되는 독립 제품 형태의 Azure Multi-Factor Authentication을 더 이상 구매할 수 없습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

## <a name="supportability"></a>지원 가능성

사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자와 통신하는 것이 중요합니다. 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 다음 지침을 따릅니다.

* 사용자에게 해당 인증 방법에 대한 액세스 권한이 없거나 인증 방법이 제대로 작동하지 않기 때문에 사용자가 로그인할 수 없는 시나리오를 처리하도록 지원 담당자를 교육합니다.
   * Using Conditional Access policies for Azure MFA Service, your support staff can add a user to a group that is excluded from a policy requiring MFA.
* Consider using Conditional Access named locations as a way to minimize two-step verification prompts. With this functionality, administrators can bypass two-step verification for users that are signing in from a secure trusted network location such as a network segment used for new user onboarding.
* Deploy [Azure AD Identity Protection](../active-directory-identityprotection.md) and trigger two-step verification based on risk detections.

## <a name="next-steps"></a>다음 단계

- [Step-by-step Azure Multi-Factor Authentication deployment](howto-mfa-getstarted.md)
