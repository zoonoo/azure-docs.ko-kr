---
title: ID 보안 점수란? - Azure Active Directory
description: ID 보안 점수를 사용하여 디렉터리의 보안 상태를 개선하는 방법
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: guptashi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23832d9f1205105f1f9711cdf3260b74ee4a9bb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952278"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Azure Active Directory의 ID 보안 점수란?

Azure AD 테넌트는 얼마나 안전하나요? 이 질문에 답변하는 방법을 모르는 경우 이 문서에서 ID 보안 점수가 ID 보안 상태를 모니터링하고 개선하는 데 어떻게 도움이 되는지 설명합니다.

## <a name="what-is-an-identity-secure-score"></a>ID 보안 점수란?

ID 보안 점수는 확률로서 사용자가 보안에 대한 Microsoft의 모범 사례 권장 사항과 얼마나 일치하는지를 나타냅니다. ID 보안 점수의 각 향상 작업은 특정 구성에 맞게 조정됩니다.  

![보안 점수](./media/identity-secure-score/identity-secure-score-overview.png)

점수는 다음에 도움이 됩니다.

- ID 보안 상태를 객관적으로 측정
- ID 보안 개선 계획
- 개선 계획의 성공 여부 검토

ID 보안 점수 대시보드에서 점수 및 관련 정보에 액세스할 수 있습니다. 이 대시보드에서 다음을 찾을 있습니다.

- ID 보안 점수
- 동일한 업계 및 비슷한 크기의 다른 테넌트와 ID 보안 점수에 어떤 차이가 있는지 보여 주는 비교 그래프
- 시간에 따라 ID 보안 점수가 어떻게 변경되는지 보여 주는 추세 그래프
- 가능한 개선 목록

개선 작업을 따라 다음을 수행할 수 있습니다.

- 보안 상태 및 점수 개선
- ID 투자의 일부로 조직에서 사용할 수 있는 기능 활용

## <a name="how-do-i-get-my-secure-score"></a>내 보안 점수는 어떻게 가져오나요?

ID 보안 점수는 Azure AD의 모든 버전에서 사용할 수 있습니다. 조직은 **Azure Portal** > **Azure Active Directory** > **Security** > **ID 보안 점수** 를 사용하여 ID 보안 점수에 액세스할 수 있습니다.

## <a name="how-does-it-work"></a>작동 원리

Azure에서는 48시간마다 사용자 보안 구성을 살펴보고 사용자 설정과 권장 모범 사례를 비교합니다. 이 평가 결과에 따라 디렉터리에 대한 새 점수가 계산됩니다. 보안 구성이 모범 사례 지침과 완전히 맞지 않을 수 있으며, 개선 작업은 부분적으로만 충족될 수 있습니다. 이러한 시나리오에서는 컨트롤에 사용할 수 있는 최대 점수 중 일부만 표시됩니다.

각 권장 사항은 Azure AD 구성에 따라 측정됩니다. 타사 제품을 사용하여 모범 사례 권장 사항을 사용하도록 설정하는 경우 개선 작업의 설정에서 이 구성 권장 사항을 지정할 수 있습니다. 또한 권장 사항이 사용자 환경에 적용되지 않는 경우 이를 무시하도록 설정하는 옵션도 있습니다. 무시된 권장 사항은 점수 계산에 포함되지 않습니다.

![타사에서 적용되는 동작을 무시하거나 표시](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>컨트롤의 점수를 매기는 방법

컨트롤은 두 가지 방법으로 점수를 매길 수 있습니다. 일부는 이진 방식으로 점수가 매겨지며, 이진 방식에서는 권장 사항에 따라 기능 또는 설정이 구성된 경우 점수의 100%를 받습니다. 다른 점수는 전체 구성의 백분율로 계산됩니다. 예를 들어, 개선 권장 사항이 있을 경우 MFA를 사용하여 모든 사용자를 보호하면 최대 10.71%를 받고 총 사용자 100명 중 5명만 보호할 경우 약 0.53%의 부분 점수만 주어집니다(보호된 5명 / 총 100명 * 최대 10.71% = 부분 점수 0.53%).

### <a name="what-does-not-scored-mean"></a>[점수가 매겨지지 않음]은 어떤 의미인가요?

[점수가 매겨지지 않음]으로 레이블이 지정된 작업은 조직에서 수행할 수 있지만 (아직!) 도구에 연결되지 않아 점수가 매겨지지 않는 작업입니다. 따라서 여전히 보안을 개선할 수는 있지만 해당 작업에 대한 크레딧을 지금 당장 받을 수는 없습니다.

### <a name="how-often-is-my-score-updated"></a>내 점수가 업데이트되는 빈도는?

점수는 하루에 한 번(약 오전 1시 PST) 계산됩니다. 측정된 작업을 변경하는 경우 점수는 다음날 자동으로 업데이트됩니다. 변경 내용이 점수에 반영되는 데 최대 48시간이 걸립니다.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>내 점수가 변경됐습니다. 변경 이유를 어떻게 알 수 있나요?

[Microsoft 365 보안 센터](https://security.microsoft.com/)로 이동하여 완전한 Microsoft 보안 점수를 찾을 수 있습니다. 기록 탭에서 심층 변화를 검토하여 보안 점수에 대한 모든 변경 내용을 쉽게 확인할 수 있습니다.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>보안 점수는 내가 보안을 위반할 위험도 측정하나요?

간단히 말해 아닙니다. 보안 점수는 위반할 가능성에 대한 측정을 절대값으로 표시하지 않습니다. 보안 점수는 보안을 위반할 위험을 상쇄할 수 있는 기능의 채택 정도를 표시합니다. 어떤 서비스도 위반하지 않는다는 것을 보장하지 못합니다. 보안 점수는 결코 이를 보장하는 것으로 해석해서는 안 됩니다.

### <a name="how-should-i-interpret-my-score"></a>내 점수는 어떻게 해석해야 하나요?

권장된 보안 기능을 구성하거나 보안 관련 작업(예: 보고서 읽기)을 수행하면 점수가 개선됩니다. 일부 작업은 사용자에 대해 MFA(Multi-Factor Authentication)를 사용하도록 설정하는 것처럼 부분 완료에 대해 점수가 매겨집니다. 보안 점수는 사용하는 Microsoft 보안 서비스를 직접적으로 상징합니다. 보안은 유용성과 균형을 이루어야 합니다. 모든 보안 제어에는 사용자 영향 구성 요소가 있습니다. 사용자 영향이 낮은 제어는 사용자의 일상적인 작업에 거의 영향을 미치지 않습니다.

점수 기록을 보려면 [Microsoft 365 보안 센터](https://security.microsoft.com/)로 이동하여 전반적인 Microsoft 보안 점수를 검토합니다. 전체 보안 점수에 대한 변경내용을 검토하여 기록을 볼 수 있습니다. 특정 날짜를 선택하여 해당 날짜에 사용하도록 설정된 제어 및 각 제어에 대해 획득한 점수를 확인합니다.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>ID 보안 점수는 Microsoft 365 보안 점수와 어떤 관계가 있나요?

[Microsoft 보안 점수](/office365/securitycompliance/microsoft-secure-score)에는 5가지 고유한 제어 및 점수 범주가 있습니다.

- ID
- 데이터
- 디바이스
- 인프라
- 앱

ID 보안 점수는 Microsoft 보안 점수의 ID 부분을 나타냅니다. 이와 같이 겹치기 때문에 ID 보안 점수 및 Microsoft의 보안 점수에 대한 권장 사항은 동일합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft 보안 점수에 대해 자세히 알아보세요](/office365/securitycompliance/microsoft-secure-score).