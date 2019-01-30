---
title: Azure Active Directory Identity Protection에서 사용자 위험 정책을 구성하는 방법 | Microsoft Docs
description: Azure AD ID 보호 사용자 위험 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 3f9a3746417e278b720230b6125650c4a86557de
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54466837"
---
# <a name="how-to-configure-the-user-risk-policy"></a>방법: 사용자 위험 정책 구성

사용자 위험을 통해 Azure AD는 사용자 계정이 손상되었을 확률을 감지합니다. 관리자는 특정 사용자 위험 수준에 자동으로 대응하도록 사용자 위험 조건부 액세스 정책을 구성할 수 있습니다.
 
이 문서에서는 사용자 위험 정책을 구성하는 데 필요한 정보를 제공합니다.


## <a name="what-is-a-user-risk-policy"></a>사용자 위험 정책이란?

Azure AD는 사용자의 각 로그인을 분석합니다. 분석 목표는 로그인과 함께 발생하는 의심스러운 작업을 감지하는 것입니다. Azure AD에서 시스템이 감지할 수 있는 의심스러운 작업을 위험 이벤트라고도 합니다. 일부 위험 이벤트를 실시간으로 감지할 수 있지만 더 많은 시간이 필요한 위험 이벤트도 있습니다. 예를 들어, 비정상적인 위치로 불가능한 이동을 감지하기 위해 시스템은 사용자의 일반 동작을 학습하기 위해 14일의 초기 학습 기간을 필요로 합니다. 감지된 위험 이벤트를 해결하기 위한 몇 가지 옵션이 있습니다. 예를 들어 개별 위험 이벤트를 수동으로 해결하거나 로그인 위험이나 사용자 위험 조건부 액세스 정책을 사용하여 해결할 수 있습니다.

사용자에 대해 감지되었지만 해결되지 않은 모든 위험 이벤트를 활성 위험 이벤트라고 합니다. 사용자와 관련된 활성 위험 이벤트를 사용자 위험이라고 합니다. 사용자 위험에 따라 Azure AD는 사용자가 손상되었을 확률(낮음, 보통, 높음)을 계산합니다. 이 확률을 사용자 위험 수준이라고 합니다.

![사용자 위험](./media/howto-user-risk-policy/1031.png)

사용자 위험 정책은 특정 사용자 위험 수준에 대해 구성할 수 있는 자동화된 응답입니다. 사용자 위험 정책을 사용하여 리소스에 대한 액세스를 차단하거나 암호 변경을 요구하여 사용자 계정을 정상 상태로 되돌릴 수 있습니다.


## <a name="how-do-i-access-the-user-risk-policy"></a>사용자 위험 정책에 어떻게 액세스할 수 있나요?
   
로그인 위험 정책은 [Azure AD ID 보호 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)의 **구성** 섹션에 있습니다.
   
![사용자 위험 정책](./media/howto-user-risk-policy/1014.png)



## <a name="policy-settings"></a>정책 설정

로그인 위험 정책을 구성할 때 다음을 설정해야 합니다.

- 정책이 적용되는 사용자 및 그룹:

    ![개요](./media/howto-user-risk-policy/11.png)

- 정책을 트리거하는 로그인 위험 수준:

    ![사용자 위험 수준](./media/howto-user-risk-policy/12.png)

- 로그인 위험 수준에 도달할 때 적용할 액세스 유형:  

    ![Access](./media/howto-user-risk-policy/13.png)

- 정책 상태:

    ![정책 적용](./media/howto-user-risk-policy/14.png)

정책 구성 대화 상자는 구성의 영향을 예상하는 옵션을 제공합니다.

![예상 영향](./media/howto-user-risk-policy/15.png)

## <a name="what-you-should-know"></a>알아야 할 사항

사용자는 사용자 위험 보안 정책을 설정하여 위험 수준에 따라 로그인 시 사용자를 차단할 수 있습니다.

![차단 중](./media/howto-user-risk-policy/16.png)


로그인 차단:

* 영향을 받는 사용자에 대한 새 사용자 위험 이벤트의 생성 방지
* 관리자가 사용자의 ID에 영향을 주는 위험 이벤트를 수동으로 수정하고 안전한 상태로 복원할 수 있음

## <a name="best-practices"></a>모범 사례

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다.
그러나 정책에서 위험 플래그가 지정된 **낮음** 및 **보통** 사용자를 제외하며, 이는 이전에 손상이 의심되거나 손상된 것으로 확인된 ID 또는 디바이스를 보호하지 못할 수 있습니다.

정책을 설정 하는 경우

* 많은 가양성(개발자, 보안 분석가)을 생성할 수 있는 사용자를 제외합니다.
* 정책을 사용하지 않는 것이 실용적이지 않은 로캘에서 사용자를 제외합니다(예: 기술 지원팀에 액세스 권한 없음).
* 초기 정책이 롤아웃하는 동안 또는 최종 사용자가 확인하는 과제를 최소화해야 하는 경우 **높음** 임계값을 사용합니다.
* 조직이 보안을 강화해야 하는 경우 **낮음** 임계값을 사용합니다. **낮은** 임계값을 선택하면 추가 사용자 로그인 과제가 발생하지만 보안이 강화됩니다.

대부분의 조직에 권장되는 기본값은 **보통** 임계값에 대한 규칙을 구성하여 유용성과 보안 간의 균형을 유지할 수 있습니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

* [손상된 계정 복구 흐름](flows.md#compromised-account-recovery)  
* [손상된 계정 차단됨 흐름](flows.md#compromised-account-blocked)  

**관련 구성 대화 상자를 열려면**

- **Azure AD ID 보호** 블레이드의 **구성** 섹션에서 **사용자 위험 정책**을 클릭합니다.

    ![사용자 위험 정책](./media/howto-user-risk-policy/1009.png "사용자 위험 정책")




## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview.md)를 참조하세요.
