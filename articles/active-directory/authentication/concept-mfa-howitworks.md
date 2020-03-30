---
title: Azure MFA - Azure Active 디렉터리 작동 방식
description: 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 애플리케이션에 대한 액세스를 보호합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484063"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>작동 방법: Azure Multi-Factor Authentication

2단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 추가 인증 메서드가 없다면 소용이 없습니다. 이러한 인증에서는 다음 중 두 가지 이상의 인증 메서드를 요구합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 디바이스(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 신원 정보(생체 인식)

<center>

![개념적 인증 방법 이미지](./media/concept-mfa-howitworks/methods.png)</center>

사용자에 대해 단순성을 유지하는 동안 Azure MFA(Multi-Factor Authentication)를 통해 데이터와 애플리케이션에 대한 액세스를 보호할 수 있습니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 사용하기 쉬운 다양한 [인증 방법](concept-authentication-methods.md)을 통해 강력한 인증을 제공합니다. 관리자가 결정한 구성에 따라 사용자에게 MFA 챌린지가 표시될 수도 있고 그렇지 않을 수도 있습니다.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication을 가져오는 방법

Multi-Factor Authentication은 다음과 같은 제품의 일부로 제공됩니다.

* **Azure Active Directory 프리미엄** 또는 **Microsoft 365 비즈니스** - 다단계 인증을 요구하는 조건부 액세스 정책을 사용하는 Azure 다단계 인증의 전체 기능을 사용합니다.

* **Azure AD 무료** 또는 독립 실행형 **Office 365** 라이선스 - 보안 기본값을 사용하여 사용자 및 관리자에 대한 다단계 인증을 [요구합니다.](../fundamentals/concept-fundamentals-security-defaults.md)

* **Azure Active Directory 전역 관리자** - Azure Multi-factor Authentication 기능의 하위 집합을 전역 관리자 계정을 보호하는 수단으로 사용할 수 있습니다.

> [!NOTE]
> 신규 고객은 2018년 9월 1일부터 제공되는 독립 제품 형태의 Azure Multi-Factor Authentication을 더 이상 구매할 수 없습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

## <a name="supportability"></a>지원 가능성

사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자와 통신하는 것이 중요합니다. 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 다음 지침을 따릅니다.

* 사용자에게 해당 인증 방법에 대한 액세스 권한이 없거나 인증 방법이 제대로 작동하지 않기 때문에 사용자가 로그인할 수 없는 시나리오를 처리하도록 지원 담당자를 교육합니다.
   * Azure MFA 서비스에 대한 조건부 액세스 정책을 사용하여 지원 담당자는 MFA가 필요한 정책에서 제외된 그룹에 사용자를 추가할 수 있습니다.
* 2단계 확인 프롬프트를 최소화하는 방법으로 조건부 Access 명명된 위치를 사용하는 것이 좋습니다. 이 기능을 통해 관리자는 새 사용자 온보딩에 사용되는 네트워크 세그먼트와 같이 신뢰할 수 있는 안전한 네트워크 위치에서 로그인하는 사용자에 대한 2단계 인증을 우회할 수 있습니다.
* [Azure AD ID 보호를](../active-directory-identityprotection.md) 배포하고 위험 검색을 기반으로 2단계 인증을 트리거합니다.

## <a name="next-steps"></a>다음 단계

- [단계별 Azure 다단계 인증 배포](howto-mfa-getstarted.md)
