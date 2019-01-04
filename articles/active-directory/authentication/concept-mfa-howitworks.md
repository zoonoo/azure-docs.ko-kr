---
title: Azure Multi-Factor Authentication - 작동 방법
description: 간단한 로그인 프로세스에 대한 사용자 요구를 충족하는 동안 Azure Multi-Factor Authentication을 사용하면 데이터와 애플리케이션에 대한 액세스를 보호합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4a90dc1d97121426e7b161b1d5c92df78b0925a6
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114161"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>작동 방법: Azure Multi-Factor Authentication

2단계 인증의 보안은 계층화된 접근 방식을 기반으로 합니다. 다단계 인증 요소를 손상시키는 일은 공격자에게 매우 어렵습니다. 공격자가 사용자의 암호를 알게 된 경우에도 추가 인증 메서드가 없다면 소용이 없습니다. 이러한 인증에서는 다음 중 두 가지 이상의 인증 메서드를 요구합니다.

* 사용자가 알고 있는 정보(일반적으로 암호)
* 사용자가 보유한 디바이스(예: 휴대폰과 같이 쉽게 복제되지 않는 신뢰할 수 있는 디바이스)
* 사용자의 신원 정보(생체 인식)

<center>![개념적 인증 방법 이미지](./media/concept-mfa-howitworks/methods.png)</center>

사용자에 대해 단순성을 유지하는 동안 Azure MFA(Multi-Factor Authentication)를 통해 데이터와 애플리케이션에 대한 액세스를 보호할 수 있습니다. 두 번째 형식의 인증을 요구하여 추가 보안을 제공하고 사용하기 쉬운 다양한 [인증 방법](concept-authentication-methods.md)을 통해 강력한 인증을 제공합니다.

## <a name="how-to-get-multi-factor-authentication"></a>Multi-Factor Authentication을 가져오는 방법

Multi-Factor Authentication은 다음과 같은 제품의 일부로 제공됩니다.

* **Azure Active Directory Premium 라이선스** - 전체 기능을 갖춘 Azure Multi-Factor Authentication Service(클라우드) 또는 Azure Multi-Factor Authentication 서버(온-프레미스)를 사용합니다.
   * **Azure MFA Service(클라우드)** - **이 옵션은 새 배포에 권장되는 경로입니다**. 클라우드의 Azure MFA에는 온-프레미스 인프라가 필요하지 않고 페더레이션되거나 클라우드 전용 사용자가 사용할 수 있습니다.
   * **Azure MFA 서버** - 조직이 연관된 인프라 요소를 관리하려고 하고 온-프레미스 환경에서 AD FS를 배포한 경우 이 옵션을 사용할 수 있습니다.
* **Office 365용 Multi-Factor Authentication** - Azure Multi-Factor Authentication 기능 중 하위 집합을 구독의 일부로 사용할 수 있습니다. Office 365용 MFA에 대한 자세한 내용은 [Office 365용 Multi-Factor Authentication 배포에 대한 계획](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba) 아티클을 참조하세요.
* **Azure Active Directory 전역 관리자** - Azure Multi-factor Authentication 기능의 하위 집합을 전역 관리자 계정을 보호하는 수단으로 사용할 수 있습니다.

> [!NOTE]
> 신규 고객은 2018년 9월 1일부터 제공되는 독립 제품 형태의 Azure Multi-Factor Authentication을 더 이상 구매할 수 없습니다. Multi-Factor Authentication은 Azure AD Premium 라이선스에서 계속 사용할 수 있게 지원됩니다.

## <a name="supportability"></a>지원 가능성

사용자의 대부분은 암호만 사용하여 인증하는 데 익숙하므로 회사가 이 프로세스에 관하여 모든 사용자와 통신하는 것이 중요합니다. 인식하면 사용자가 MFA와 관련된 경미한 문제에 대해 지원 센터에 전화할 가능성을 줄일 수 있습니다. 그러나 MFA를 일시적으로 비활성화가 필요한 시나리오도 있습니다. 그러한 시나리오를 처리하는 방법에 대해 이해하려면 다음 지침을 따릅니다.

* 사용자에게 해당 인증 방법에 대한 액세스 권한이 없거나 인증 방법이 제대로 작동하지 않기 때문에 사용자가 로그인할 수 없는 시나리오를 처리하도록 지원 담당자를 교육합니다.
   * 지원 담당자는 Azure MFA Service에 대한 조건부 액세스 정책을 사용하여 MFA를 요구하는 정책에서 제외된 그룹에 사용자를 추가할 수 있습니다.
   * 지원 담당자는 사용자가 2단계 인증 없이 인증할 수 있도록 Azure MFA 서버 사용자에 대한 임시 일회성 바이패스를 설정할 수 있습니다. 바이패스는 임시적이며 지정된 시간(초) 이후 만료됩니다.   
* 2단계 확인 프롬프트를 최소화하는 방법으로 신뢰할 수 있는 IP 또는 명명된 위치를 사용하는 것이 좋습니다. 이 기능을 사용하여 관리되거나 페더레이션된 테넌트의 관리자가 해당 조직의 인트라넷 등 신뢰할 수 있는 네트워크 위치에서 로그인하는 사용자를 위해 2단계 인증을 바이패스할 수 있습니다.
* [Azure AD ID 보호](../active-directory-identityprotection.md)를 배포하고 위험 이벤트에 따라 2단계 인증을 트리거합니다.

## <a name="next-steps"></a>다음 단계

- 단계별 MFA [배포 계획](https://aka.ms/MFADeploymentPlan) 가져오기

- [사용자 라이선스](concept-mfa-licensing.md)에 대한 세부 정보 찾기

- [배포 버전](concept-mfa-whichversion.md)에 대한 세부 정보 가져오기

- [자주 묻는 질문](multi-factor-authentication-faq.md)에 대한 대답 찾기
