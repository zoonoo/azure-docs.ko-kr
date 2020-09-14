---
title: Advisor 점수를 사용 하 여 Azure 워크 로드 최적화
description: Advisor 점수를 사용 하 여 Azure를 최대한 활용 하세요.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056237"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Advisor 점수를 사용 하 여 Azure 워크 로드 최적화

## <a name="introduction-to-advisor-score"></a>Advisor 점수 소개

Azure Advisor는 워크 로드에 대 한 모범 사례 권장 사항을 제공 합니다. 이러한 권장 사항은 다음과 같은 도움을 주는 맞춤형 이며 조치를 취할 수 있습니다.
* 워크 로드의 상태를 개선 하 고 Azure 배포를 최적화 합니다.
* 모범 사례에 따라 주요 문제를 사전에 방지
* [Microsoft Azure 잘 설계 된 프레임 워크](https://docs.microsoft.com/azure/architecture/framework/) 의 5 핵심 요소에 대해 Azure 워크 로드를 평가 합니다.

Advisor의 핵심 기능인 **advisor 점수** 는 이러한 목표를 효과적이 고 효율적으로 달성 하는 데 도움이 됩니다. 

Azure를 최대한 활용 하려면 워크 로드 최적화 과정에서 어떤 서비스/리소스를 사용 하 고 있는지를 이해 하는 것이 중요 합니다. 또한 권장 사항에 따라 작업의 우선 순위를 지정 하 여 결과를 최대화 하는 방법을 알아야 합니다. 이 최적화 과정에서 수행 하는 진행 상황을 추적 하 고 보고 하는 것도 중요 합니다. **Advisor 점수**를 사용 하면 새로운 gamification 환경을 통해 이러한 모든 작업을 쉽게 수행할 수 있습니다. 개인 설정 된 클라우드 컨설턴트는 사용 원격 분석 및 리소스 구성을 지속적으로 평가 하 여 업계 모범 사례를 확인 Azure Advisor. 그런 다음 Advisor는 해당 결과를 단일 점수에 집계 하 여 신뢰할 수 있고 안전 하며 비용 효율적인 솔루션을 구축 하는 데 필요한 단계를 수행 하는 경우를 한눈에 파악할 수 있습니다. Advisor 점수는 전체 점수로 구성 되며,이 점수는 잘 구성 된 프레임 워크의 5 핵심 요소을 나타내는 각 Azure Advisor 범주에 대해 하나씩 5 개의 범주 점수를 추가로 나눌 수 있습니다. 매일, 매주, 매월 추세를 사용 하 여 전체 점수 및 범주 점수를 확인 하 여 시간에 따른 진행 상황을 추적할 수 있으며, 목표를 달성 하는 데 도움이 되도록 벤치 마크를 설정할 수 있습니다. 

 ![Advisor 점수 매기기 환경](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Advisor 점수를 사용 하는 방법
Advisor에는 Advisor 범주의 전체 Advisor 점수와 분석 결과가 백분율로 표시 됩니다. 모든 범주에서 100%의 점수는 advisor가 권장 하는 모범 사례에 따라 Advisor에서 평가한 모든 리소스를 의미 합니다. 스펙트럼의 다른 쪽 끝에서 점수가 0% 이면 Advisor에서 평가한 리소스가 Advisor의 권장 사항을 따릅니다. 이러한 점수를 사용 하 여 조직 하면 다음과 같은 흐름을 쉽게 달성할 수 있습니다.
* **Advisor 점수를 사용** 하면 작업/구독이 advisor 점수를 기준으로 수행 하는 방식을 기준으로 할 수 있습니다. 추세를 파악 하는 추세를 확인할 수도 있습니다.
* **Advisor 범주 점수** 는 추가 주의가 필요한 범주를 이해 하 고 우선 순위를 지정 하는 데 도움이 됩니다.
* 각 범주에 대 한 재구성 작업의 우선 순위를 지정 하는 데 도움이 되는 각 권장 사항의 **잠재적 점수 증가**

점수에 대 한 각 권장 사항의 기여도는 Azure Portal의 개요 페이지에 명확 하 게 표시 됩니다. 모범 사례를 채택 하 여 점수를 늘릴 수 있으며, **가능한 점수가 증가** 하는 권장 사항의 우선 순위를 지정 하 여 사용자가 보유 한 시간으로 가장 빠르게 진행 되도록 할 수 있습니다.  

![Advisor 점수 영향](./media/advisor-score-2.png)

Advisor의 점수 매기기 방법은 장기 권장 사항을 사용 하 여 비용이 많이 드는 리소스에 추가 가중치를 적용 하므로 가장 높은 소매 비용으로 리소스를 수정 하 여 가장 많은 진행률을 만들 수 있습니다. Advisor 권장 사항이 개별 리소스와 관련이 없는 경우 이러한 권장 사항을 해제 하 여 점수 계산에서 해당 권장 사항을 제외 하 고 Advisor에 의견을 보내 권장 사항을 향상할 수 있습니다. 

## <a name="how-is-advisor-score-calculated"></a>Advisor 점수는 어떻게 계산 되나요?
Advisor는 범주 점수와 전체 Advisor 점수를 백분율로 표시 합니다. 모든 범주의 100% 점수는 advisor *가*권장 하는 모범 사례를 따릅니다. 스펙트럼의 다른 쪽 끝에서 0%의 점수는 Advisor의 권장 사항에 따라 모든 리소스 (Advisor에 의해 평가 됨)가 없음을 의미 합니다. 
**5 개의 범주는 각각 100의 잠재적 점수가 가장 높습니다.** 전체 Advisor 점수는 적용 가능한 모든 범주에서 가장 높은 잠재 점수의 합계로 나누어 적용 가능한 각 범주 점수의 합계로 계산 됩니다. 대부분의 구독에서 Advisor는 각 범주의 점수를 추가 하 고 500으로 나눕니다. 그러나 **각 범주 점수는 Advisor에서 평가한 리소스를 사용 하는 경우에만 계산 됩니다.**

### <a name="scoring-methodology"></a>점수 매기기 방법: 
Advisor 점수 계산은 다음 네 단계로 요약할 수 있습니다.
1. Advisor는 관리자의 권장 사항이 하나 이상 있는 구독에 대 한 리소스 인 **영향을 받는 리소스의 일별 소매 비용**을 계산 합니다.
2. Advisor는 권장 사항이 있는지 여부에 관계 없이 Advisor에 의해 모니터링 되는 리소스를 평가 하는 **리소스의 일일 소매 비용**을 계산 합니다. 
3. Advisor는 각 권장 사항 유형에 대해 **정상 리소스 비율**을 계산 하며,이는 영향을 받는 리소스의 비용을 평가 된 리소스의 비용으로 나눈 값입니다.
4. Advisor는 각 범주의 정상 리소스 비율에 세 개의 추가 가중치를 적용 합니다.
* 영향이 큰 권장 사항은 낮은 영향을 가진 권장 사항 보다 가중치가 높습니다.
* 장기 권장 사항이 있는 리소스는 점수에 대해 더 많이 계산 됩니다.
* Advisor에서 해제 한 리소스는 점수 계산에서 완전히 제거 됩니다. 
    
Advisor는 Advisor 범주 수준에서이 모델을 적용 하 여 (보안은 보안 [점수](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) 모델 사용) 각 범주에 대 한 us Advisor 점수를 제공 하 고, 간단한 평균은 최종 Advisor 점수를 생성 합니다.


## <a name="advisor-score-faq"></a>Advisor 점수 FAQ
* **점수를 얼마나 자주 새로 고치 나요?**
점수는 하루에 한 번 이상 새로 고쳐집니다. 
* **내 점수를 얻기 위해 Advisor의 권장 사항을 확인 해야 하나요?**
아닙니다. Advisor에서 권장 사항을 보지 않으며 모범 사례를 사전에 채택 하지 않더라도 Advisor에서 권장 하는 모범 사례를 채택 하 고 있는지 여부는 점수가 반영 됩니다.  
* **Advisor에서 구독에 대 한 리소스의 일일 소매 비용을 계산 하는 방법**
Advisor는 Azure.com 가격 책정 페이지에 게시 된 *종 량* 제 요금을 사용 합니다 .이 페이지에는 해당 할인이 반영 되지 않으며 리소스를 마지막으로 할당 된 날의 사용량의 수량을 곱합니다. 리소스 비용 계산에서 할인을 생략 하면 구독, 테 넌 트 및 등록에서 할인이 달라질 수 있는 관리자 점수가 비교 됩니다. 
* **권장 사항이 관련이 없는 경우는 어떻게 되나요?**
Advisor의 권장 사항을 해제 하면 점수 계산에서 생략 됩니다. 권장 사항을 해제 하면 Advisor가 권장 구성의 품질을 향상 시킬 수 있습니다.
* **점수가 변경 되는 이유는 무엇 인가요?** Advisor에서 권장 하는 모범 사례를 채택 하 여 영향을 받는 리소스를 재구성 하는 경우 점수를 변경할 수 있습니다. 사용자 또는 구독에 대 한 사용 권한이 있는 모든 사용자가 새 리소스를 수정 하거나 만든 경우 점수는 모든 리소스의 총 비용을 기준으로 영향을 받는 리소스의 비율을 기준으로 하므로 점수가 변동 될 수도 있습니다.
* **내 점수가 Azure에 얼마나 많은 영향을 하나요?**
아닙니다. 점수는 구독 및 서비스 조합의 크기를 제어 하도록 설계 되었습니다. 
* **점수 매기기 방법이 프로덕션 테스트 작업과 개발-테스트 워크 로드를 구분 하나요?**
아니요, 지금은 그렇지는 않지만, 개발 및 테스트에 사용 되 고 권장 사항이 적용 되지 않는 경우 개별 리소스에 대 한 권장 사항을 해제할 수 있습니다.
* **100 리소스를 사용 하는 구독과 10만 리소스가 있는 구독 간의 점수를 비교할 수 있나요?**
점수 매기기 방법은 구독 및 서비스 조합에서 리소스 수를 제어 하도록 설계 되었으므로 리소스가 적은 구독에는 더 많은 리소스가 있는 구독 보다 더 높거나 낮은 점수가 있을 수 있습니다. 

## <a name="how-to-access-advisor"></a>Advisor에 액세스 하는 방법
Advisor 점수는 Azure Portal에서 공개 미리 보기로 제공 됩니다. Advisor 섹션으로 이동 하 고, Advisor 점수를 왼쪽 탐색 창에서 두 번째 메뉴 항목으로 찾을 수 있습니다. 

![Advisor 점수 항목-포인트](./media/advisor-score-3.png)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
