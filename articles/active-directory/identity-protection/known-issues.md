---
title: Azure Active Directory의 갱신된 ID 보호 기능 관련 FAQ 및 알려진 문제 | Microsoft Docs
description: Azure Active Directory의 갱신된 ID 보호 기능 관련 FAQ 및 알려진 문제에 대해 설명합니다.
services: active-directory
keywords: Azure Active Directory ID 보호, 클라우드 앱 검색, 애플리케이션 관리, 보안, 위험, 위험 수준, 취약점, 보안 정책
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 392b7a08d9422658c5620f60e9c1caca074bc85e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60452686"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>Azure Active Directory의 갱신된 ID 보호 기능 관련 FAQ 및 알려진 문제에 대해 설명합니다.


## <a name="dismiss-user-risk-known-issues"></a>사용자 위험 알려진 문제를 해제 합니다.

클래식 ID 보호의 **사용자 위험 해제** 기능은 갱신된 ID 보호의 사용자 위험 기록에 포함된 행위자를 **Azure AD**로 설정합니다.


갱신된 ID 보호의 **사용자 위험 해제** 기능은 갱신된 ID 보호의 사용자 위험 기록에 포함된 행위자를 **\<사용자 블레이드를 가리키는 하이퍼링크가 적용된 관리자 이름\>** 으로 설정합니다.

사용자 위험 해제 흐름에서 대기 시간을 일으키는 현재 알려진 문제가 있습니다. “사용자 위험 정책”이 있는 경우 “사용자 위험 해제”를 클릭하면 몇 분 이내에 해제된 사용자에게 정책 적용이 중지됩니다. 그러나 해제된 사용자의 “위험 상태”를 새로 고치는 UX에서 알려진 지연이 있습니다. 해결 방법으로, 브라우저 수준에서 페이지를 새로 고쳐 최신 사용자 “위험 상태”를 확인합니다.


## <a name="risky-users-report-known-issues"></a>알려진된 문제를 보고 하는 위험한 사용자

**사용자 이름** 필드에 대한 쿼리는 대/소문자를 구분하는 반면 **이름** 필드에 대한 쿼리는 대/소문자를 구분하지 않습니다.

**날짜 표시**를 설정/해제하면 **위험을 마지막으로 업데이트한 날짜** 열이 숨겨집니다. 해당 열을 다시 추가하려면 위험한 사용자 블레이드 맨 위의 **열**을 클릭합니다.

클래식 ID 보호의 **모든 이벤트 해제** 기능은 위험 이벤트의 상태를 **닫힘(해결됨)** 으로 설정합니다.


## <a name="risky-sign-ins-report-known-issues"></a>알려진 문제는 위험한 로그인 보고서

위험 이벤트 **해결** 시에는 상태가 **사용자가 위험 기반 정책으로 구동되는 MFA를 통과함**으로 설정됩니다.


## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>각 위험 이벤트에 대해 위험 수준을 직접 설정할 수 없는 이유는 무엇인가요?

검색 정밀도를 기준으로 하는 ID 보호의 위험 수준은 Microsoft에서 감독하는 기계 학습을 통해 제공됩니다. 사용자에게 제공되는 환경을 사용자 지정하려는 경우 관리자는 사용자 위험 및 로그인 위험 정책에서 특정 사용자/그룹을 포함/제외할 수 있습니다.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>사용자가 실제로 로그인한 위치와 로그인 위치가 일치하지 않는 이유는 무엇인가요?

IP 지리적 위치 매핑은 업계 전체에서 사용되는 챌린지입니다. 로그인 보고서에 나와 있는 위치가 실제 위치가 일치하지 않는다고 생각되면 지원을 요청하세요. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>ID 보호의 피드백 메커니즘은 어떤 방식으로 작동하나요?

**손상됨 확인**(로그인 시) – ID 소유자가 로그인을 수행하지 않았음을 Azure AD ID 보호에 알리고 손상을 표시합니다.

- 이 피드백이 수신되면 로그인 및 사용자 위험 상태가 **손상됨 확인**으로 전환되며 위험 수준은 **높음**으로 설정됩니다.

- 또한 향후에 위험 평가를 개선할 수 있도록 Microsoft 기계 학습 시스템에도 정보가 제공됩니다.

    > [!NOTE]
    > 사용자의 문제가 이미 수정된 경우에는 **손상됨 확인**을 클릭하면 안 됩니다. 이렇게 하면 로그인 및 사용자 위험 상태가 **손상됨 확인**으로 전환되고 위험 수준이 **높음**으로 설정되기 때문입니다.

**안전 확인**(로그인 시) – ID 소유자가 로그인을 수행했음을 Azure AD ID 보호에 알리고 손상을 표시하지 않습니다.

- 이 피드백이 수신되면 사용자가 아닌 로그인 위험 상태가 **안전 확인**으로 전환되며 위험 수준은 **-** 로 설정됩니다.

- 또한 향후에 위험 평가를 개선할 수 있도록 Microsoft 기계 학습 시스템에도 정보가 제공됩니다.

    > [!NOTE]
    > 사용자의 보안이 손상되지 않았다고 생각되면 로그인 수준에서 **안전 확인**을 사용하는 대신 사용자 수준에서 **사용자 위험 해제**를 사용합니다. 사용자 수준에서 **사용자 위험 해제**를 사용하는 경우에는 사용자 위험 및 이전의 모든 위험한 로그인 및 위험 이벤트가 닫힙니다.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>ID 보호에 위험한 로그인 또는 위험 이벤트가 표시되지 않는데 위험 점수가 더 낮거나 높은 사용자가 표시되는 이유는 무엇인가요?

사용자 위험 점수는 기본적으로 누적되며 만료되지 않으므로, ID 보호에는 최근의 위험한 로그인 또는 위험 이벤트가 표시되지 않더라도 점수가 더 낮거나 높은 사용자 위험이 표시될 수 있습니다. 위험한 로그인 및 위험 이벤트 세부 정보가 저장되는 기간이 아닐 때만 사용자에 대해 악의적인 활동이 수행된 경우 이러한 현상이 발생할 수 있습니다. 악의적인 행위자는 추가로 공격을 하기 전에 고객 환경 내에서 손상된 ID에 대해 140일 이상 공격을 준비하는 것으로 확인되었으므로, 사용자 위험은 만료되지 않습니다. 고객은 다음 위치로 이동해 사용자 위험 시간 표시줄을 확인하여 특정 사용자가 위험한 것으로 표시되는 이유를 파악할 수 있습니다. `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>로그인과 관련한 검색 결과에는 위험 수준이 낮음/중간으로 표시되는데 로그인의 "로그인 위험(집계)" 점수는 높음인 이유는 무엇인가요?

로그인에 대해 여러 위험이 검색되었거나 로그인의 기타 기능이 사용되면 집계 위험 점수가 높아질 수 있습니다. 반면 로그인과 관련하여 검색된 위험의 점수가 높음이라도 로그인 위험(집계) 점수는 중간일 수 있습니다. 
