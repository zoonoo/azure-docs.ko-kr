---
title: Azure Active Directory Identity Protection에서 로그인 위험 정책을 구성하는 방법 | Microsoft Docs
description: Azure AD ID 보호 로그인 위험 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe9e0a4d481ef7b802c50fdc347872e389fa8ef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60294673"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>방법: 로그인 위험 정책 구성

Azure Active Directory는 [위험 이벤트 유형](../reports-monitoring/concept-risk-events.md#risk-event-types)을 실시간 및 오프라인으로 검색합니다. 사용자 로그인 중에 검색된 각 위험 이벤트는 위험한 로그인이라는 논리적 개념을 파생시킵니다. 위험한 로그인은 사용자 계정의 정당한 소유자가 수행하지 않았을 수 있는 로그인 시도에 대한 지표입니다.


## <a name="what-is-the-sign-in-risk-policy"></a>로그인 위험 정책이란?

Azure AD는 사용자의 각 로그인을 분석합니다. 분석 목표는 로그인과 함께 발생하는 의심스러운 작업을 감지하는 것입니다. 예를 들어 익명 IP 주소를 사용하여 로그인되었거나 알 수 없는 위치에서 로그인이 시작되었나요? Azure AD에서 시스템이 감지할 수 있는 의심스러운 작업을 위험 이벤트라고도 합니다. 로그인하는 동안 감지된 위험 이벤트를 기반으로 Azure AD는 값을 계산합니다. 이 값은 합법적인 사용자가 로그인하지 않았을 확률(낮음, 보통, 높음)을 나타냅니다. 이 확률을 **로그인 위험 수준**이라고 합니다.

로그인 위험 정책은 특정 로그인 위험 수준에 대해 구성할 수 있는 자동화된 응답입니다. 사용자의 응답에서 리소스에 대한 액세스를 차단할 수 있으며 액세스하기 위해 MFA(Multi-Factor Authentication) 시도를 통과해야 할 수 있습니다.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>로그인 위험 정책에 액세스하려면 어떻게 할까요?
   
로그인 위험 정책은 [Azure AD ID 보호 페이지](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy)의 **구성** 섹션에 있습니다.
   
![로그인 위험 정책](./media/howto-sign-in-risk-policy/1014.png "로그인 위험 정책")


## <a name="policy-settings"></a>정책 설정

로그인 위험 정책을 구성할 때 다음을 설정해야 합니다.

- 정책이 적용되는 사용자 및 그룹:

    ![개요](./media/howto-sign-in-risk-policy/11.png)

- 정책을 트리거하는 로그인 위험 수준:

    ![로그인 위험 수준](./media/howto-sign-in-risk-policy/12.png)

- 로그인 위험 수준에 도달할 때 적용할 액세스 유형:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- 정책 상태:

    ![정책 적용](./media/howto-sign-in-risk-policy/14.png)


정책 구성 대화 상자는 재구성의 영향을 예상하는 옵션을 제공합니다.

![예상 영향](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>알아야 할 사항

MFA를 요구하는 로그인 위험 보안 정책을 구성할 수 있습니다.

![MFA 요구](./media/howto-sign-in-risk-policy/16.png)

그러나 보안상의 이유로 이 설정은 MFA에 이미 등록된 사용자에게만 적용됩니다. ID 보호는 사용자가 아직 MFA에 등록되지 않은 경우 MFA 요구 사항의 사용자를 차단합니다.

위험한 로그인에 대해 MFA를 요구하려는 경우 다음을 수행해야 합니다.

1. 영향을 받는 사용자에 대해 [다단계 인증 등록 정책](howto-mfa-policy.md)을 사용하도록 설정합니다.

2. 영향을 받는 사용자가 위험하지 않은 세션에 로그인하여 MFA 등록을 수행해야 합니다.

이러한 단계를 완료하면 위험한 로그인에 대해 다단계 인증이 요구됩니다.

로그인 위험 정책은 다음과 같습니다.

- 최신 인증을 사용하여 모든 브라우저 트래픽 및 로그인에 적용합니다.

- ADFS 등 페더레이션된 IDP에서 WS-Trust 엔드포인트를 사용하지 않도록 설정하여 이전 보안 프로토콜을 사용하는 애플리케이션에 적용하지 않습니다.


관련 사용자 환경에 대한 개요는 다음을 참조하세요.

* [위험한 로그인 복구](flows.md#risky-sign-in-recovery)
* [위험한 로그인 차단됨](flows.md#risky-sign-in-blocked)  
* [Azure AD ID 보호를 사용하는 로그인 환경](flows.md)  

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

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview.md)를 참조하세요.
