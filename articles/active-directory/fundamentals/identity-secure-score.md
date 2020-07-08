---
title: Id 보안 점수는 무엇 인가요? -Azure Active Directory
description: Id 보안 점수를 사용 하 여 디렉터리의 보안 상태를 개선 하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523114"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Azure Active Directory의 ID 보안 점수란?

Azure AD 테넌트는 얼마나 안전하나요? 이 질문에 대답 하는 방법을 모르는 경우이 문서는 id 보안 점수를 사용 하 여 id 보안 상태를 모니터링 하 고 개선 하는 방법을 설명 합니다.

## <a name="what-is-an-identity-secure-score"></a>ID 보안 점수란?

Id 보안 점수는 1에서 223 사이의 숫자 이며, Microsoft의 보안에 대 한 모범 사례 권장 사항에 따라 정렬 된 방법에 대 한 표시기로 작동 합니다. Id 보안 점수의 각 향상 작업은 특정 구성에 맞게 조정 됩니다.  

![보안 점수](./media/identity-secure-score/identity-secure-score-overview.png)

점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

ID 보안 점수 대시보드에서 점수 및 관련 정보에 액세스할 수 있습니다. 이 대시보드에서 다음을 찾을 있습니다.

- Id 보안 점수
- Id 보안 점수가 동일한 업계의 다른 테 넌 트와 비슷한 크기를 비교 하는 방법을 보여 주는 비교 그래프입니다.
- 시간에 따라 Id 보안 점수가 변경 된 방식을 보여 주는 추세 그래프
- 가능한 향상 된 기능 목록

개선 작업을 따라 다음을 수행할 수 있습니다.

- 보안 상태 및 점수 개선
- Id 투자의 일부로 조직에서 사용할 수 있는 기능 활용

## <a name="how-do-i-get-my-secure-score"></a>내 보안 점수는 어떻게 가져오나요?

Id 보안 점수는 모든 버전의 Azure AD에서 사용할 수 있습니다. 조직은 **Azure Portal**  >  **Azure Active Directory**  >  **보안**  >  **id 보안 점수**를 사용 하 여 id 보안 점수에 액세스할 수 있습니다.

## <a name="how-does-it-work"></a>작동 원리

Azure에서는 48시간마다 사용자 보안 구성을 살펴보고 사용자 설정과 권장 모범 사례를 비교합니다. 이 평가 결과에 따라 디렉터리에 대해 새 점수가 계산 됩니다. 보안 구성이 모범 사례 지침과 완전히 맞지 않을 수 있으며, 개선 작업은 부분적 으로만 충족 될 수 있습니다. 이러한 시나리오에서는 컨트롤에 사용할 수 있는 최대 점수 중 일부만 표시 됩니다.

각 권장 사항은 Azure AD 구성에 따라 측정됩니다. 타사 제품을 사용 하 여 모범 사례 권장 사항을 사용 하는 경우 개선 작업의 설정에서이 구성을 나타낼 수 있습니다. 사용자 환경에 적용 되지 않는 경우 무시 되도록 권장 구성 옵션을 설정할 수도 있습니다. 무시된 권장 사항은 점수 계산에 포함되지 않습니다.

![타사에서 적용 되는 동작을 무시 하거나 표시 합니다.](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>보안 점수가 어떻게 도움이 되나요?

보안 점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

## <a name="what-you-should-know"></a>알아야 할 사항

### <a name="who-can-use-the-identity-secure-score"></a>ID 보안 점수는 누가 사용할 수 있나요?

ID 보안 점수는 다음과 같은 역할에 의해 사용될 수 있습니다.

- 글로벌 관리자
- 보안 관리자
- 보안 읽기 권한자

### <a name="how-are-controls-scored"></a>컨트롤의 점수는 어떻게 매겨지지?

컨트롤은 두 가지 방법으로 점수를 매길 수 있습니다. 일부는 이진 방식으로 점수가 매겨집니다. 권장 사항에 따라 기능 또는 설정이 구성 된 경우 점수의 100%가 발생 합니다. 다른 점수는 전체 구성의 백분율로 계산 됩니다. 예를 들어, 향상 된 기능을 사용 하는 경우 MFA를 사용 하 여 모든 사용자를 보호 하 고 5 개 중 100 명의 사용자만 보호 하는 경우 2 개 (2 개 보호/100 총 * 30 최대 포인트 = 2 포인트 부분 점수)에 대 한 부분 점수가 제공 됩니다.

### <a name="what-does-not-scored-mean"></a>[점수가 매겨지지 않음]은 어떤 의미인가요?

[점수가 매겨지지 않음]으로 레이블이 지정된 작업은 조직에서 수행할 수 있지만 (아직!) 도구에 연결되지 않아 점수가 매겨지지 않는 작업입니다. 따라서 여전히 보안을 개선할 수는 있지만 해당 작업에 대한 크레딧을 지금 당장 받을 수는 없습니다.

### <a name="how-often-is-my-score-updated"></a>내 점수가 업데이트되는 빈도는?

점수는 하루에 한 번(약 오전 1시 PST) 계산됩니다. 측정된 작업을 변경하는 경우 점수는 다음날 자동으로 업데이트됩니다. 변경 내용이 점수에 반영되는 데 최대 48시간이 걸립니다.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>내 점수가 변경됐습니다. 변경 이유를 어떻게 알 수 있나요?

[Microsoft 365 security center](https://security.microsoft.com/)로 이동 하 여 완전 한 Microsoft 보안 점수를 찾을 수 있습니다. 기록 탭에서 심층 변화를 검토 하 여 보안 점수에 대 한 모든 변경 내용을 쉽게 확인할 수 있습니다.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>보안 점수가 위반 될 위험을 측정 하나요?

간단히 말해 아닙니다. 보안 점수는 위반 될 가능성을 절대 측정 하지 않습니다. 보안 점수는 보안을 위반할 위험을 상쇄할 수 있는 기능의 채택 정도를 표시합니다. 서비스는 사용자가 위반 하지 않도록 보장할 수 없으며 보안 점수는 어떤 방식으로든 보장으로 해석 되지 않습니다.

### <a name="how-should-i-interpret-my-score"></a>내 점수는 어떻게 해석해야 하나요?

권장된 보안 기능을 구성하거나 보안 관련 작업(예: 보고서 읽기)을 수행하면 점수를 받습니다. 일부 작업은 사용자에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정하는 것처럼 부분 완료에 대해 점수가 매겨집니다. 보안 점수는 사용 하는 Microsoft 보안 서비스를 직접 대표 합니다. 보안은 사용 편의성과 균형을 맞춰야 합니다. 모든 보안 제어에는 사용자 영향 구성 요소가 있습니다. 사용자 영향이 낮은 제어는 사용자의 일상적인 작업에 거의 영향을 미치지 않습니다.

점수 기록을 보려면 [Microsoft 365 security center](https://security.microsoft.com/) 로 이동 하 여 전반적인 Microsoft 보안 점수를 검토 합니다. 전체 보안 점수에 대 한 변경 내용을 검토 하 여 기록을 볼 수 있습니다. 특정 날짜를 선택하여 해당 날짜에 사용하도록 설정된 제어 및 각 제어에 대해 획득한 점수를 확인합니다.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>ID 보안 점수는 Office 365 보안 점수와 어떤 관계가 있나요?

[Microsoft 보안 점수](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) 에는 5 가지 고유한 제어 및 점수 범주가 있습니다.

- ID
- 데이터
- 디바이스
- 인프라
- 앱

Id 보안 점수는 Microsoft 보안 점수의 id 부분을 나타냅니다. 이러한 겹침은 Microsoft의 id 보안 점수와 id 점수에 대 한 권장 사항이 동일 하다는 것을 의미 합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft 보안 점수에 대해 자세히 알아보기](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
