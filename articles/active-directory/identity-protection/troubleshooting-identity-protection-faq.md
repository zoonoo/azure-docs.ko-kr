---
title: Azure Active Directory의 Id 보호에 대 한 Faq
description: 질문과 대답 Azure AD ID 보호
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443567"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory의 질문과 대답 Id 보호

## <a name="dismiss-user-risk-known-issues"></a>사용자 위험 알려진 문제 해제

클래식 Id 보호에서 **사용자 위험 해제** 를 사용 하면 id 보호에서 사용자의 위험 기록에 있는 행위자가 **Azure AD**로 설정 됩니다.

Id 보호에서 **사용자 위험 해제** 사용자 **의 블레이드\>를 가리키는 하이퍼링크를 사용 하 여** id 보호에서 사용자의 위험 기록에 있는 행위자를\<관리자 이름으로 설정 합니다.

사용자 위험 해제 흐름에서 대기 시간이 발생 하는 현재 알려진 문제가 있습니다. “사용자 위험 정책”이 있는 경우 “사용자 위험 해제”를 클릭하면 몇 분 이내에 해제된 사용자에게 정책 적용이 중지됩니다. 그러나 해제된 사용자의 “위험 상태”를 새로 고치는 UX에서 알려진 지연이 있습니다. 해결 방법으로, 브라우저 수준에서 페이지를 새로 고쳐 최신 사용자 “위험 상태”를 확인합니다.

## <a name="risky-users-report-known-issues"></a>위험한 사용자가 알려진 문제를 보고 함

**사용자 이름** 필드에 대한 쿼리는 대/소문자를 구분하는 반면 **이름** 필드에 대한 쿼리는 대/소문자를 구분하지 않습니다.

**날짜 표시**를 설정/해제하면 **위험을 마지막으로 업데이트한 날짜** 열이 숨겨집니다. 해당 열을 다시 추가하려면 위험한 사용자 블레이드 맨 위의 **열**을 클릭합니다.

클래식 Id 보호에서 **모든 이벤트 해제** 위험 검색의 상태를 **닫힘 (확인 됨)** 으로 설정 합니다.

## <a name="risky-sign-ins-report-known-issues"></a>위험한 로그인 보고서의 알려진 문제

위험 검색에 대 한 **해결** 은 **위험 기반 정책에 따라**발생 하는 MFA를 사용자에 게 설정 합니다.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="why-is-a-user-is-at-risk"></a>사용자가 위험에 노출 되는 이유는 무엇 인가요?

Azure AD ID 보호 고객 인 경우 [위험한 사용자](howto-identity-protection-investigate-risk.md#risky-users) 보기로 이동 하 고 위험 사용자를 클릭 합니다. 아래쪽의 서랍에 ' 위험 기록 ' 탭에는 사용자 위험 변경에 대 한 모든 이벤트가 표시 됩니다. 사용자에 대 한 모든 위험한 로그인을 보려면 ' 사용자의 위험한 로그인 '을 클릭 합니다. 이 사용자에 대 한 모든 위험 검색을 보려면 ' 사용자의 위험 검색 '을 클릭 합니다.

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>특정 형식의 검색 보고서를 어떻게 가져올 수 있나요?

위험 검색 보기로 이동 하 고 ' 검색 유형 '으로 필터링 합니다. 그런 다음에서이 보고서를 다운로드할 수 있습니다. CSV 또는 맨 위에 있는 **다운로드** 단추를 사용 하는 JSON 형식입니다. 자세한 내용은 [방법: 위험 조사](howto-identity-protection-investigate-risk.md#risk-detections)문서를 참조 하세요.

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>각 위험 검색에 대해 고유한 위험 수준을 설정 하지 못한 이유는 무엇 인가요?

검색 정밀도를 기준으로 하는 ID 보호의 위험 수준은 Microsoft에서 감독하는 기계 학습을 통해 제공됩니다. 사용자에게 제공되는 환경을 사용자 지정하려는 경우 관리자는 사용자 위험 및 로그인 위험 정책에서 특정 사용자/그룹을 포함/제외할 수 있습니다.

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>사용자가 실제로 로그인한 위치와 로그인 위치가 일치하지 않는 이유는 무엇인가요?

IP 지리적 위치 매핑은 업계 전체에서 사용되는 챌린지입니다. 로그인 보고서에 나열 된 위치가 실제 위치와 일치 하지 않는 것으로 보이면 Microsoft 지원에 문의 하세요. 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>이전 UI에서와 같이 특정 위험 검색을 닫으려면 어떻게 해야 하나요?

연결 된 로그인을 손상 또는 안전 하 게 확인 하 여 위험 검색에 대 한 피드백을 제공할 수 있습니다. 로그인에 지정 된 피드백은 해당 로그인에 대 한 모든 검색으로 trickles 됩니다. 로그인에 연결 되지 않은 검색을 종료 하려는 경우 사용자 수준에 대 한 피드백을 제공할 수 있습니다. 자세한 내용은 [방법: Azure AD ID 보호에서 위험 피드백 제공](howto-identity-protection-risk-feedback.md)문서를 참조 하세요.

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>내 사용자의 진행 상황을 이해 하는 데 얼마나 많은 시간이 소요 될 수 있나요?

- [위험한 사용자](howto-identity-protection-investigate-risk.md#risky-users) 보기에는 모든 이전 로그인에 따라 사용자의 위험이 표시 됩니다. 
- [위험한 로그인](howto-identity-protection-investigate-risk.md#risky-sign-ins) 보기에는 지난 30 일 동안 위험에 노출 된 로그인이 표시 됩니다. 
- [위험](howto-identity-protection-investigate-risk.md#risk-detections) 검색 보기에는 지난 90 일 동안 발생 한 위험 검색이 표시 됩니다.

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>특정 검색에 대해 자세히 알아보려면 어떻게 해야 하나요?

모든 위험 검색은 [위험 이란?](concept-identity-protection-risks.md#risk-types-and-detection)문서에 설명 되어 있습니다. 검색에 대 한 자세한 내용을 보려면 Azure Portal 검색 옆의 (i) 기호를 마우스로 가리킵니다.

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
    > 사용자의 보안이 손상되지 않았다고 생각되면 로그인 수준에서 **안전 확인**을 사용하는 대신 사용자 수준에서 **사용자 위험 해제**를 사용합니다. 사용자 수준에 대 한 **해제 사용자 위험은** 사용자 위험과 모든 이전 위험한 로그인 및 위험 검색을 종료 합니다.

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>Id 보호에 위험한 로그인 또는 위험 검색이 표시 되지 않는 경우에도 위험 점수가 낮음 인 사용자가 표시 되는 이유는 무엇 인가요?

사용자 위험은 본질적으로 누적 되 고 만료 되지 않습니다. 사용자는 Id 보호에 표시 된 최근 위험한 로그인 또는 위험 검색이 없더라도 사용자에 게 낮은 사용자의 위험을 초래할 수 있습니다. 이 상황은 사용자의 악성 활동이 위험한 로그인 및 위험 검색의 세부 정보를 저장 하는 기간을 초과 하 여 발생 한 경우에 발생할 수 있습니다. 악의적인 행위자는 추가로 공격을 하기 전에 고객 환경 내에서 손상된 ID에 대해 140일 이상 공격을 준비하는 것으로 확인되었으므로, 사용자 위험은 만료되지 않습니다. 고객은 다음 위치로 이동해 사용자 위험 시간 표시줄을 확인하여 특정 사용자가 위험한 것으로 표시되는 이유를 파악할 수 있습니다. `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>로그인과 관련한 검색 결과에는 위험 수준이 낮음/중간으로 표시되는데 로그인의 "로그인 위험(집계)" 점수는 높음인 이유는 무엇인가요?

로그인에 대해 여러 위험이 검색되었거나 로그인의 기타 기능이 사용되면 집계 위험 점수가 높아질 수 있습니다. 반면 로그인과 관련하여 검색된 위험의 점수가 높음이라도 로그인 위험(집계) 점수는 중간일 수 있습니다. 
