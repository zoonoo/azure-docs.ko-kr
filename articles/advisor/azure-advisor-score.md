---
title: Advisor 점수를 사용 하 여 Azure 워크 로드 최적화
description: Azure Advisor 점수를 사용 하 여 Azure를 최대한 활용 하세요.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630125"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Advisor 점수를 사용 하 여 Azure 워크 로드 최적화

## <a name="introduction-to-advisor-score"></a>Advisor 점수 소개

Azure Advisor는 워크 로드에 대 한 모범 사례 권장 사항을 제공 합니다. 이러한 권장 사항은 다음과 같은 도움을 주는 맞춤형 이며 조치를 취할 수 있습니다.

* 워크 로드의 상태를 개선 하 고 Azure 배포를 최적화 합니다.
* 모범 사례에 따라 인기 있는 문제를 사전에 방지 합니다.
* [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)의 5 가지 핵심 요소를 기준으로 Azure 워크 로드를 평가 합니다.

Advisor의 핵심 기능인 Advisor 점수는 이러한 목표를 효과적이 고 효율적으로 달성 하는 데 도움이 될 수 있습니다.

Azure를 최대한 활용 하려면 워크 로드 최적화 경험의 위치를 이해 하는 것이 중요 합니다. 어떤 서비스 또는 리소스를 사용 하 고 있지도 알고 있어야 합니다. 또한 권장 사항에 따라 작업의 우선 순위를 지정 하 여 결과를 최대화 하는 방법을 알아야 합니다.

이 최적화 과정에서 수행 하는 진행 상황을 추적 하 고 보고 하는 것도 중요 합니다. Advisor 점수를 사용 하면 새로운 gamification 환경을 통해 이러한 모든 작업을 쉽게 수행할 수 있습니다.

개인 설정 된 클라우드 컨설턴트는 사용 원격 분석 및 리소스 구성을 지속적으로 평가 하 여 업계 모범 사례를 확인 Azure Advisor. Advisor는 결과를 단일 점수에 집계 합니다. 이 점수를 사용 하면 신뢰할 수 있고 안전 하며 비용 효율적인 솔루션을 구축 하는 데 필요한 단계를 수행 하 고 있는지를 한눈에 파악할 수 있습니다.

Advisor 점수는 전체 점수로 구성 되며 5 개의 범주 점수를 추가로 나눌 수 있습니다. Advisor의 각 범주에 대 한 점수 하나는 Well-Architected 프레임 워크의 5 핵심 요소을 나타냅니다.

매일, 매주, 매월 추세를 사용 하 여 전체 점수 및 범주 점수를 확인 하 여 시간이 지남에 따라 진행 상황을 추적할 수 있습니다. 또한 목표를 달성 하는 데 도움이 되는 벤치 마크를 설정할 수 있습니다.

 ![Advisor 점수 페이지를 보여 주는 스크린샷](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Advisor 점수 해석

Advisor는 전체 Advisor 점수와 Advisor 범주의 분석 (백분율)을 표시 합니다. 모든 범주에서 100%의 점수는 advisor가 권장 하는 모범 사례에 따라 Advisor에서 평가한 모든 리소스를 의미 합니다. 스펙트럼의 다른 쪽 끝에서 점수가 0% 이면 Advisor에서 평가한 리소스가 Advisor의 권장 사항을 따릅니다. 이러한 점수 조직를 사용 하 여 다음과 같은 흐름을 쉽게 달성할 수 있습니다.

* **Advisor 점수** 를 사용 하면 advisor 점수에 따라 작업 또는 구독이 수행 하는 방식을 기준으로 할 수 있습니다. 추세를 파악 하는 추세를 확인할 수도 있습니다.
* **범주별 점수 매기기** 는 각 권장 사항에 대 한 점수를 가장 많이 높일 수 있는 해결 방법을 알려줍니다. 이러한 값은 권장 사항의 가중치와 예측 된 구현의 용이성을 모두 반영 합니다. 이러한 요소를 통해 시간을 최대한 활용 하 여 최대한의 가치를 얻을 수 있습니다. 또한 우선 순위를 지정 하는 데 도움이 됩니다.
* **범주 점수 영향** 각 권장 사항에 대 한 영향을 각 범주에 대 한 수정 작업의 우선 순위를 지정 하는

범주 점수에 대 한 각 권장 사항의 기여도는 Azure Portal의 **Advisor 점수** 페이지에서 명확 하 게 표시 됩니다. **잠재적 점수 증가** 열에 나열 된 비율을 기준으로 각 범주 점수를 늘릴 수 있습니다. 이 값은 범주 내의 권장 사항에 대 한 가중치와 예측 된 구현의 용이성을 모두 반영 하 여 잠재적으로 쉬운 작업을 해결 합니다. 점수가 가장 높은 권장 사항에 중점을 둘 경우 시간을 사용 하 여 가장 많은 진행률을 쉽게 파악할 수 있습니다.

![Advisor 점수 영향을 보여 주는 스크린샷](./media/advisor-score-2.png)

Advisor 권장 사항이 개별 리소스와 관련이 없는 경우에는 해당 권장 사항을 연기 하거나 해제할 수 있습니다. 다음 새로 고침을 사용 하 여 점수 계산에서 제외 됩니다. 또한 Advisor는이 입력을 추가 피드백으로 사용 하 여 모델을 개선 합니다.

## <a name="how-is-an-advisor-score-calculated"></a>Advisor 점수는 어떻게 계산 되나요?

Advisor는 범주 점수와 전체 Advisor 점수를 백분율로 표시 합니다. 모든 범주의 100% 점수는 advisor *가* 권장 하는 모범 사례를 따릅니다. 스펙트럼의 다른 쪽 끝에서 점수가 0% 이면 Advisor에 의해 평가 되는 모든 리소스가 Advisor 권장 사항을 따릅니다.

**각 5개 범주에서 최고 가능한 점수는 100%입니다.** 전체 Advisor 점수는 적용 가능한 모든 범주에서 가장 높은 잠재 점수의 합계로 나누어 적용 가능한 각 범주 점수의 합계로 계산 됩니다. 대부분 구독에서 이는 Advisor가 각 범주의 점수를 더하고 500으로 나누는 것을 의미합니다. 하지만 *각 범주 점수는 Advisor에서 평가한 리소스를 사용 하는 경우에만 계산 됩니다*.

### <a name="advisor-score-calculation-example"></a>Advisor 점수 계산 예

* **단일 구독 점수:** 이 예는 구독에 대 한 모든 Advisor 범주 점수의 간단한 평균입니다. Advisor 범주 점수가 **Cost** = 73, **안정성** = 85, **Operational op=** 77 및 **Performance** = 100 이면 advisor 점수는 (73 + 85 + 77 + 100)/(4x100) = 0.84% 또는 84%입니다.
* **여러 구독 점수:** 여러 구독을 선택 하면 생성 되는 전체 Advisor 점수가 가중치가 적용 된 집계 범주 점수입니다. 여기서 각 Advisor 범주 점수는 구독에서 사용 하는 리소스를 기준으로 집계 됩니다. Advisor가가 중 집계 범주 점수를 가지 면 Advisor는 간단한 평균 계산을 수행 하 여 구독에 대 한 전체 점수를 제공 합니다.

### <a name="scoring-methodology"></a>채점 방법론

Advisor 점수 계산은 다음 네 단계로 요약할 수 있습니다.

1. Advisor는 *영향을 받는 리소스의 소매 비용* 을 계산 합니다. 해당 리소스는 Advisor에 하나 이상의 권장 사항이 있는 구독의 리소스입니다.
1. Advisor는 *평가 된 리소스의 소매 비용* 을 계산 합니다. 해당 리소스는 권장 사항이 있는지 여부와 관계없이 Advisor에서 모니터링되는 리소스입니다.
1. Advisor는 각 권장 사항 유형에 대해 *정상 리소스 비율* 을 계산 합니다. 해당 비율은 영향을 받는 리소스의 소매 비용을 평가된 리소스의 소매 비용으로 나눈 값입니다.
1. Advisor는 각 범주의 정상 리소스 비율에 세 가지 추가 가중치를 적용합니다.

   * 영향이 큰 권장 사항은 영향이 낮은 권장 사항 보다 가중치가 높습니다.
   * 장기 권장 사항이 있는 리소스는 점수에 대해 더 많이 계산 됩니다.
   * Advisor에서 연기하거나 해제하는 리소스는 점수 계산에서 완전히 제거됩니다.

Advisor는 advisor 범주 수준에이 모델을 적용 하 여 각 범주에 대 한 Advisor 점수를 제공 합니다. **보안** 은 보안 [점수](../security-center/secure-score-security-controls.md#introduction-to-secure-score) 모델을 사용 합니다. 간단한 평균은 최종 Advisor 점수를 생성 합니다.

## <a name="advisor-score-faqs"></a>Advisor 점수 Faq

### <a name="how-often-is-my-score-refreshed"></a>점수를 얼마나 자주 새로 고치 나요?

점수는 하루에 한 번 이상 새로 고쳐집니다.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>일부 권장 구성에서 범주 점수 영향 열에 빈 "-" 값이 있는 이유는 무엇 인가요?

Advisor는 점수 매기기 모델의 최근 변경 내용에 대 한 새로운 권장 사항 또는 권장 사항을 즉시 포함 하지 않습니다. 일반적으로 몇 주 동안 짧은 평가 기간이 지나면 점수에 포함 됩니다.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>잠재적인 절감 액이 적은 경우에도 몇 가지 권장 사항에 대해 비용 점수가 더 큰 영향을 주는 이유는 무엇 인가요?

**비용** 점수는 미달 사용 리소스의 잠재적 절감 및 권장 사항을 구현 하는 용이성을 모두 반영 합니다. 예를 들어 잠재적 절감 액이 낮은 경우에도 더 오랜 시간 동안 유휴 상태인 영향을 받는 리소스에 추가 가중치가 적용 됩니다.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>하나 이상의 범주 또는 구독에 대 한 점수가 없는 이유는 무엇입니까?

Advisor는 Advisor에서 평가한 리소스를 포함 하는 범주 및 구독에 대해서만 점수를 생성 합니다.

### <a name="what-if-a-recommendation-isnt-relevant"></a>권장 사항이 관련이 없는 경우는 어떻게 되나요?

Advisor의 권장 사항을 해제 하면 점수 계산에서 생략 됩니다. 권장 사항을 해제 하면 Advisor가 권장 구성의 품질을 향상 시킬 수 있습니다.

### <a name="why-did-my-score-change"></a>점수가 변경 되는 이유는 무엇 인가요?

Advisor에서 권장 하는 모범 사례를 채택 하 여 영향을 받는 리소스를 재구성 하는 경우 점수를 변경할 수 있습니다. 사용자 또는 구독에 대 한 사용 권한이 있는 모든 사용자가 새 리소스를 수정 하거나 만든 경우 점수 변동도 표시 될 수 있습니다. 점수는 모든 리소스의 총 비용을 기준으로 비용에 영향을 받는 리소스의 비율을 기준으로 합니다.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Advisor가 구독에 대 한 리소스의 소매 비용을 어떻게 계산 하나요?

Advisor는 [Azure 가격 책정](https://azure.microsoft.com/pricing/)에 게시 된 종 량 제 요금을 사용 합니다. 이러한 요금에는 적용 가능한 할인이 반영 되지 않습니다. 그런 다음 리소스를 마지막으로 할당 한 날의 사용량 수량으로 속도를 곱합니다. 리소스 비용을 계산 하 여 할인을 생략 하면 구독, 테 넌 트 및 등록에서 할인이 달라질 수 있는 관리자 점수가 비교 됩니다.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>내 점수에 대 한 점수를 얻으려면 Advisor의 권장 사항을 확인 해야 하나요?

아니요. 이러한 모범 사례를 사전에 채택 하 고 Advisor에서 권장 사항을 보지 않은 경우에도 Advisor가 권장 하는 모범 사례를 채택 하는 것이 점수를 반영 합니다.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>점수 매기기 방법이 프로덕션 테스트 작업과 개발-테스트 워크 로드를 구분 하나요?

아니요, 지금은 불가능 합니다. 그러나 이러한 리소스가 개발 및 테스트에 사용 되 고 권장 사항이 적용 되지 않는 경우 개별 리소스에 대 한 권장 사항을 해제할 수 있습니다.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>100 리소스를 사용 하는 구독과 10만 리소스가 있는 구독 간의 점수를 비교할 수 있나요?

점수 매기기 방법은 구독 및 서비스 조합에서 리소스의 수를 제어 하도록 설계 되었습니다. 리소스가 적은 구독에는 더 많은 리소스가 있는 구독 보다 더 높거나 낮은 점수가 있을 수 있습니다.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>점수 영향 열에 "서비스 예정"이 표시 되 면 무엇을 의미 하나요?

이 메시지는 새로운 권장 사항이 며 Advisor 점수 모델로 가져오기 위해 노력 하 고 있습니다. 이 새로운 권장 사항을 점수 계산에 고려 하 고 나면 권장 사항에 대 한 점수 영향 값이 표시 됩니다.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>내 점수가 Azure에 얼마나 많은 영향을 하나요?

아니요. 점수가 반드시 필요한 것은 아닙니다. 불필요 한 지출으로 인해 **비용** 점수가 낮아집니다.

## <a name="access-advisor-score"></a>액세스 관리자 점수

Advisor 점수는 Azure Portal에서 공개 미리 보기로 제공 됩니다. 왼쪽 창의 **advisor** 섹션 아래에서 **advisor 점수** 를 참조 하십시오.

![Advisor 점수 진입점을 보여 주는 스크린샷](./media/advisor-score-3.png)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 작동의 뛰어난 권장 사항](advisor-operational-excellence-recommendations.md)
