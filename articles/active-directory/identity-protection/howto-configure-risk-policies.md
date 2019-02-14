---
title: Azure Active Directory Identity Protection에서 위험 정책을 구성하는 방법(새로 고침) | Microsoft Docs
description: Azure Active Directory Identity Protection에서 위험 정책을 구성하는 방법(새로 고침)
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: f9ffbebee06ab7b9e010c7e6c84fee533611fb38
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567658"
---
# <a name="how-to-configure-risk-policies-in-azure-active-directory-identity-protection-refreshed"></a>방법: Azure Active Directory Identity Protection에서 위험 정책 구성(새로 고침)


Azure AD는 잠재적으로 손상된 ID를 나타내는 위험 이벤트를 탐지합니다. 위험 정책을 구성하여 탐지 결과에 대한 자동화된 응답을 정의할 수 있습니다.

- 로그인 위험 정책을 사용하면 사용자의 로그인 중에 탐지된 실시간 위험 이벤트에 대한 응답을 구성할 수 있습니다. 
- 사용자 위험 정책을 사용하면 시간에 경과에 따라 사용자에 대해 탐지된 모든 활성 사용자 위험에 대한 응답을 구성할 수 있습니다.  


## <a name="what-is-the-sign-in-risk-policy"></a>로그인 위험 정책이란?

Azure AD는 사용자의 각 로그인을 분석합니다. 분석 목표는 로그인과 함께 발생하는 의심스러운 작업을 감지하는 것입니다. 예를 들어 익명 IP 주소를 사용하여 로그인되었거나 알 수 없는 위치에서 로그인이 시작되었나요? Azure AD에서 시스템이 감지할 수 있는 의심스러운 작업을 위험 이벤트라고도 합니다. 로그인하는 동안 감지된 위험 이벤트를 기반으로 Azure AD는 값을 계산합니다. 이 값은 합법적인 사용자가 로그인하지 않았을 확률(낮음, 보통, 높음)을 나타냅니다. 이 확률을 **로그인 위험 수준**이라고 합니다.

로그인 위험 정책은 특정 로그인 위험 수준에 대해 구성할 수 있는 자동화된 응답입니다. 사용자의 응답에서 리소스에 대한 액세스를 차단할 수 있으며 액세스하기 위해 MFA(Multi-Factor Authentication) 시도를 통과해야 할 수 있습니다.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>로그인 위험 정책에 액세스하려면 어떻게 할까요?
   
로그인 위험 정책은 [Azure AD ID 보호 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)의 **구성** 섹션에 있습니다.
   
![로그인 위험 정책](./media/howto-configure-risk-policies/1014.png "로그인 위험 정책")


## <a name="sign-in-risk-policy-settings"></a>로그인 위험 정책 설정

로그인 위험 정책을 구성할 때 다음을 설정해야 합니다.

- 정책이 적용되는 사용자 및 그룹:

    ![개요](./media/howto-configure-risk-policies/11.png)

- 정책을 트리거하는 로그인 위험 수준:

    ![로그인 위험 수준](./media/howto-configure-risk-policies/12.png)

- 로그인 위험 수준에 도달할 때 적용할 액세스 유형:  

    ![Access](./media/howto-configure-risk-policies/13.png)

- 정책 상태:

    ![정책 적용](./media/howto-configure-risk-policies/14.png)


정책 구성 대화 상자는 재구성의 영향을 예상하는 옵션을 제공합니다.

![예상 영향](./media/howto-configure-risk-policies/15.png)

## <a name="what-you-should-know-about-sign-in-risk-policies"></a>로그인 위험 정책에 대해 알아야 할 내용

MFA를 요구하는 로그인 위험 보안 정책을 구성할 수 있습니다.

![MFA 요구](./media/howto-configure-risk-policies/16.png)

그러나 보안상의 이유로 이 설정은 MFA에 이미 등록된 사용자에게만 적용됩니다. ID 보호는 사용자가 아직 MFA에 등록되지 않은 경우 MFA 요구 사항의 사용자를 차단합니다.

위험한 로그인에 대해 MFA를 요구하려는 경우 다음을 수행해야 합니다.

1. 영향을 받는 사용자에 대해 [다단계 인증 등록 정책](#multi-factor-authentication-registration-policy)을 사용하도록 설정합니다.

2. 영향을 받는 사용자가 위험하지 않은 세션에 로그인하여 MFA 등록을 수행해야 합니다.

이러한 단계를 완료하면 위험한 로그인에 대해 다단계 인증이 요구됩니다.

로그인 위험 정책은 다음과 같습니다.

- 최신 인증을 사용하여 모든 브라우저 트래픽 및 로그인에 적용합니다.

- ADFS 등 페더레이션된 IDP에서 WS-Trust 엔드포인트를 사용하지 않도록 설정하여 이전 보안 프로토콜을 사용하는 애플리케이션에 적용하지 않습니다.


관련 사용자 환경에 대한 개요는 다음을 참조하세요.

* [위험한 로그인 복구](flows.md#risky-sign-in-recovery)
* [위험한 로그인 차단됨](flows.md#risky-sign-in-blocked)  
* [Azure AD ID 보호를 사용하는 로그인 환경](flows.md)  









## <a name="what-is-a-user-risk-policy"></a>사용자 위험 정책이란?

Azure AD는 사용자의 각 로그인을 분석합니다. 분석 목표는 로그인과 함께 발생하는 의심스러운 작업을 감지하는 것입니다. Azure AD에서 시스템이 감지할 수 있는 의심스러운 작업을 위험 이벤트라고도 합니다. 일부 위험 이벤트를 실시간으로 감지할 수 있지만 더 많은 시간이 필요한 위험 이벤트도 있습니다. 예를 들어, 비정상적인 위치로 불가능한 이동을 감지하기 위해 시스템은 사용자의 일반 동작을 학습하기 위해 14일의 초기 학습 기간을 필요로 합니다. 감지된 위험 이벤트를 해결하기 위한 몇 가지 옵션이 있습니다. 예를 들어 개별 위험 이벤트를 수동으로 해결하거나 로그인 위험이나 사용자 위험 조건부 액세스 정책을 사용하여 해결할 수 있습니다.

사용자에 대해 감지되었지만 해결되지 않은 모든 위험 이벤트를 활성 위험 이벤트라고 합니다. 사용자와 관련된 활성 위험 이벤트를 사용자 위험이라고 합니다. 사용자 위험에 따라 Azure AD는 사용자가 손상되었을 확률(낮음, 보통, 높음)을 계산합니다. 이 확률을 사용자 위험 수준이라고 합니다.

![사용자 위험](./media/howto-configure-risk-policies/11031.png)

사용자 위험 정책은 특정 사용자 위험 수준에 대해 구성할 수 있는 자동화된 응답입니다. 사용자 위험 정책을 사용하여 리소스에 대한 액세스를 차단하거나 암호 변경을 요구하여 사용자 계정을 정상 상태로 되돌릴 수 있습니다.


## <a name="how-do-i-access-the-user-risk-policy"></a>사용자 위험 정책에 어떻게 액세스할 수 있나요?
   
사용자 위험 정책은 [Azure AD ID 보호 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)의 **구성** 섹션에 있습니다.
   
![사용자 위험 정책](./media/howto-configure-risk-policies/11014.png)



## <a name="user-risk-policy-settings"></a>사용자 위험 정책 설정

사용자 위험 정책을 구성하는 경우 다음을 설정해야 합니다.

- 정책이 적용되는 사용자 및 그룹:

    ![개요](./media/howto-configure-risk-policies/111.png)

- 정책을 트리거하는 로그인 위험 수준:

    ![사용자 위험 수준](./media/howto-configure-risk-policies/112.png)

- 로그인 위험 수준에 도달할 때 적용할 액세스 유형:  

    ![Access](./media/howto-configure-risk-policies/113.png)

- 정책 상태:

    ![정책 적용](./media/howto-configure-risk-policies/114.png)

정책 구성 대화 상자는 구성의 영향을 예상하는 옵션을 제공합니다.

![예상 영향](./media/howto-configure-risk-policies/115.png)

## <a name="what-you-should-know-about-user-risk-polices"></a>사용자 위험 정책에 대해 알아야 할 내용

사용자는 사용자 위험 보안 정책을 설정하여 위험 수준에 따라 로그인 시 사용자를 차단할 수 있습니다.

![차단 중](./media/howto-configure-risk-policies/116.png)


로그인 차단:

* 영향을 받는 사용자에 대한 새 사용자 위험 이벤트의 생성 방지
* 관리자가 사용자의 ID에 영향을 주는 위험 이벤트를 수동으로 수정하고 안전한 상태로 복원할 수 있음


























## <a name="best-practices"></a>모범 사례

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다.  

그러나 정책에서 위험 플래그가 지정된 **낮음** 및 **보통** 로그인을 제외하며, 이는 공격자가 손상된 ID를 악용하지 못하도록 차단하지 않을 수 있습니다.

정책을 설정 하는 경우

- 다단계 인증이 없는/있을 수 없는 사용자 제외

- 정책을 사용하지 않는 것이 실용적이지 않은 로캘에서 사용자를 제외합니다(예: 기술 지원팀에 액세스 권한 없음).

- 많은 가양성(개발자, 보안 분석가)을 생성할 수 있는 사용자를 제외합니다.

- 초기 정책이 롤아웃하는 동안 또는 최종 사용자가 확인하는 과제를 최소화해야 하는 경우 **높음** 임계값을 사용합니다.

- 조직에서 보안을 강화해야 하는 경우 **낮음** 임계값을 사용합니다. **낮은** 임계값을 선택하면 추가 사용자 로그인 과제가 발생하지만 보안이 강화됩니다.

대부분의 조직에 권장되는 기본값은 **보통** 임계값에 대한 규칙을 구성하여 유용성과 보안 간의 균형을 유지할 수 있습니다.





## <a name="next-steps"></a>다음 단계

 [Channel 9: Azure AD 및 ID 쇼: ID 보호 미리 보기](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

