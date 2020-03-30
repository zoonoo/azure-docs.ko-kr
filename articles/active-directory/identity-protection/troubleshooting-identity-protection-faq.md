---
title: Azure 활성 디렉터리에서 ID 보호를 위한 FAQ
description: 자주 묻는 질문 Azure AD ID 보호
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443567"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>자주 묻는 질문 Azure Active Directory에서 ID 보호

## <a name="dismiss-user-risk-known-issues"></a>사용자 위험 알려진 문제 해소

클래식 ID 보호에서 **사용자 위험 해제는** **Azure AD에**대한 ID 보호에서 사용자의 위험 기록에 행위자를 설정합니다.

ID 보호에서 **사용자 위험 해제는** 사용자의 ** \<블레이드를\>가리키는 하이퍼링크와 함께**ID 보호에서 사용자의 위험 기록에 행위자를 관리자 이름으로 설정합니다.

사용자 위험 해고 흐름에서 대기 시간을 일으키는 현재 알려진 문제가 있습니다. “사용자 위험 정책”이 있는 경우 “사용자 위험 해제”를 클릭하면 몇 분 이내에 해제된 사용자에게 정책 적용이 중지됩니다. 그러나 해제된 사용자의 “위험 상태”를 새로 고치는 UX에서 알려진 지연이 있습니다. 해결 방법으로, 브라우저 수준에서 페이지를 새로 고쳐 최신 사용자 “위험 상태”를 확인합니다.

## <a name="risky-users-report-known-issues"></a>위험한 사용자가 알려진 문제를 보고합니다.

**사용자 이름** 필드에 대한 쿼리는 대/소문자를 구분하는 반면 **이름** 필드에 대한 쿼리는 대/소문자를 구분하지 않습니다.

**날짜 표시**를 설정/해제하면 **위험을 마지막으로 업데이트한 날짜** 열이 숨겨집니다. 해당 열을 다시 추가하려면 위험한 사용자 블레이드 맨 위의 **열**을 클릭합니다.

클래식 ID 보호의 **모든 이벤트를 해제해제하여** 위험 검색 상태를 **종료(해결됨)로**설정합니다.

## <a name="risky-sign-ins-report-known-issues"></a>위험한 로그인으로 알려진 문제 보고

위험 검색에 대한 **해결은** **위험 기반 정책에 의해 구동되는 MFA를 통과한 사용자에게**상태를 설정합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="why-is-a-user-is-at-risk"></a>사용자가 위험에 처한 이유는 무엇입니까?

Azure AD ID 보호 고객인 경우 [위험한 사용자](howto-identity-protection-investigate-risk.md#risky-users) 보기로 이동하여 위험에 처한 사용자를 클릭합니다. 하단의 서랍에서 '위험 기록' 탭에는 사용자 위험 변경으로 이어진 모든 이벤트가 표시됩니다. 사용자의 모든 위험한 로그인을 보려면 '사용자의 위험한 로그인'을 클릭합니다. 이 사용자의 모든 위험 탐지를 보려면 '사용자의 위험 감지'를 클릭합니다.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>특정 유형의 탐지 에 대한 보고서를 받으시면 어떻게 해야 합니까?

위험 검색 보기로 이동하여 '검색 유형'으로 필터링합니다. 그런 다음 에서 이 보고서를 다운로드할 수 있습니다. CSV 또는 . 상단의 **다운로드** 버튼을 사용하여 JSON 형식. 자세한 내용은 방법: 위험 조사 문서를 [참조하세요.](howto-identity-protection-investigate-risk.md#risk-detections)

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>각 위험 탐지에 대해 자체 위험 수준을 설정할 수 없는 이유는 무엇입니까?

검색 정밀도를 기준으로 하는 ID 보호의 위험 수준은 Microsoft에서 감독하는 기계 학습을 통해 제공됩니다. 사용자에게 제공되는 환경을 사용자 지정하려는 경우 관리자는 사용자 위험 및 로그인 위험 정책에서 특정 사용자/그룹을 포함/제외할 수 있습니다.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>사용자가 실제로 로그인한 위치와 로그인 위치가 일치하지 않는 이유는 무엇인가요?

IP 지리적 위치 매핑은 업계 전체에서 사용되는 챌린지입니다. 로그인 보고서에 나열된 위치가 실제 위치와 일치하지 않는다고 생각되면 Microsoft 지원에 연락하십시오. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>이전 UI에서와 같이 특정 위험 검색을 닫을 수 있는 방법은 무엇입니까?

연결된 로그인이 손상또는 안전한것으로 확인하여 위험 검색에 대한 피드백을 제공할 수 있습니다. 로그인에 대한 피드백은 로그인시 만들어진 모든 검색으로 흘러내려합니다. 로그인에 연결되지 않은 검색을 닫으려는 경우 사용자 수준에서 해당 피드백을 제공할 수 있습니다. 자세한 내용은 [Azure AD ID 보호에서 위험 피드백 제공](howto-identity-protection-risk-feedback.md)방법 문서를 참조하세요.

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>사용자와 무슨 일이 일어나고 있는지 이해하기 위해 시간을 얼마나 거슬러 올라갈 수 있습니까?

- [위험한 사용자](howto-identity-protection-investigate-risk.md#risky-users) 보기는 모든 과거의 로그인을 기반으로 사용자의 위험 서 있는 표시 합니다. 
- [위험한 로그인](howto-identity-protection-investigate-risk.md#risky-sign-ins) 보기는 지난 30일 동안 위험에 처한 징후를 보여줍니다. 
- [위험 감지](howto-identity-protection-investigate-risk.md#risk-detections) 보기에는 지난 90일 동안의 위험 감지가 표시됩니다.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>특정 탐지에 대해 자세히 알아보려면 어떻게 해야 합니까?

모든 위험 탐지는 위험에 [대해](concept-identity-protection-risks.md#risk-types-and-detection)설명합니다. Azure 포털의 검색 옆에 있는 (i) 기호 위로 마우스를 가져가서 검색에 대해 자세히 알아볼 수 있습니다.

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
    > 사용자의 보안이 손상되지 않았다고 생각되면 로그인 수준에서 **안전 확인**을 사용하는 대신 사용자 수준에서 **사용자 위험 해제**를 사용합니다. 사용자 수준에서 **사용자 위험 해제는** 사용자 위험과 과거의 모든 위험한 로그인 및 위험 검색을 닫습니다.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>ID 보호에 위험한 로그인 또는 위험 검색이 표시되지 않더라도 위험 점수가 낮거나 그 이상인 사용자가 표시되는 이유는 무엇입니까?

사용자 위험이 본질적으로 누적되고 만료되지 않는 점을 감안할 때 ID 보호에 표시된 최근 위험한 로그인 또는 위험 검색이 없는 경우에도 사용자 위험이 낮거나 그 이상일 수 있습니다. 이 상황은 사용자에 대한 유일한 악의적인 활동이 위험한 로그인 및 위험 탐지의 세부 정보를 저장하는 기간을 초과하여 발생한 경우 발생할 수 있습니다. 악의적인 행위자는 추가로 공격을 하기 전에 고객 환경 내에서 손상된 ID에 대해 140일 이상 공격을 준비하는 것으로 확인되었으므로, 사용자 위험은 만료되지 않습니다. 고객은 다음 위치로 이동해 사용자 위험 시간 표시줄을 확인하여 특정 사용자가 위험한 것으로 표시되는 이유를 파악할 수 있습니다. `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>로그인과 관련한 검색 결과에는 위험 수준이 낮음/중간으로 표시되는데 로그인의 "로그인 위험(집계)" 점수는 높음인 이유는 무엇인가요?

로그인에 대해 여러 위험이 검색되었거나 로그인의 기타 기능이 사용되면 집계 위험 점수가 높아질 수 있습니다. 반면 로그인과 관련하여 검색된 위험의 점수가 높음이라도 로그인 위험(집계) 점수는 중간일 수 있습니다. 
