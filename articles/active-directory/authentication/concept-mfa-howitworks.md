---
title: Azure Multi-factor Authentication-작동 방법-Azure Active Directory
description: 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 애플리케이션에 대한 액세스를 보호합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa25e8a965b89c4e97263e3767a9400079fcad7a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496800"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>작동 방법: Azure Multi-Factor Authentication

2단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 추가 인증 메서드가 없다면 소용이 없습니다. 이러한 인증에서는 다음 중 두 가지 이상의 인증 메서드를 요구합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 디바이스(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 신원 정보(생체 인식)

<center>

![개념적 인증 메서드 이미지](./media/concept-mfa-howitworks/methods.png)</center>

사용자에 대해 단순성을 유지하는 동안 Azure MFA(Multi-Factor Authentication)를 통해 데이터와 애플리케이션에 대한 액세스를 보호할 수 있습니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 사용하기 쉬운 다양한 [인증 방법](concept-authentication-methods.md)을 통해 강력한 인증을 제공합니다. 관리자가 결정한 구성에 따라 사용자에게 MFA 챌린지가 표시될 수도 있고 그렇지 않을 수도 있습니다.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication을 가져오는 방법

Multi-Factor Authentication은 다음과 같은 제품의 일부로 제공됩니다.

* **Azure Active Directory Premium** 나 **Microsoft 365 Business** -완전 한 다단계 인증을 요구 하도록 조건부 액세스 정책을 사용 하 여 Azure Multi-factor Authentication 사용을 제공 합니다.

* **Azure AD Free**, **Azure AD Basic**, 또는 독립 실행형 **Office 365** 라이선스-이 사용 하 여 미리 만든 [조건부 액세스의 기본 보호 정책은](../conditional-access/concept-baseline-protection.md) 할 사용자와 관리자에 대 한 다단계 인증입니다.

* **Azure Active Directory 전역 관리자** - Azure Multi-factor Authentication 기능의 하위 집합을 전역 관리자 계정을 보호하는 수단으로 사용할 수 있습니다.

> [!NOTE]
> 신규 고객은 2018년 9월 1일부터 제공되는 독립 제품 형태의 Azure Multi-Factor Authentication을 더 이상 구매할 수 없습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

## <a name="supportability"></a>지원 가능성

사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자와 통신하는 것이 중요합니다. 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 다음 지침을 따릅니다.

* 사용자에게 해당 인증 방법에 대한 액세스 권한이 없거나 인증 방법이 제대로 작동하지 않기 때문에 사용자가 로그인할 수 없는 시나리오를 처리하도록 지원 담당자를 교육합니다.
   * 지원 담당자는 Azure MFA Service에 대한 조건부 액세스 정책을 사용하여 MFA를 요구하는 정책에서 제외된 그룹에 사용자를 추가할 수 있습니다.
* 2 단계 인증을 최소화 하는 방법에 프롬프트와 명명 된 위치는 조건부 액세스를 사용 하는 것이 좋습니다. 관리자 수 네트워크와 같은 안전한 신뢰할 수 있는 네트워크 위치에서 로그인 하는 사용자에 대 한 2 단계 인증을 바이패스 하는 데이 기능을 사용 하 여 새 사용자 등록을 위해 사용 되는 세그먼트입니다.
* [Azure AD ID 보호](../active-directory-identityprotection.md)를 배포하고 위험 이벤트에 따라 2단계 인증을 트리거합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Multi-factor Authentication의 단계별 배포](howto-mfa-getstarted.md)
