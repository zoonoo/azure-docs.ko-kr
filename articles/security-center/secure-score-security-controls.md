---
title: Azure Security Center의 보안 점수
description: Azure Security Center의 보안 점수 및 보안 컨트롤에 대한 설명
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007682"
---
# <a name="secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수

## <a name="introduction-to-secure-score"></a>보안 점수 소개

Azure Security Center에는 두 가지 주요 목표가 있습니다. 

- 현재 보안 상황을 이해 하는 데 도움을 줍니다.
- 효율적이 고 효과적으로 보안을 개선 하는 데 도움이 됩니다.

이러한 목표를 달성 하는 데 사용할 수 있는 Security Center의 중앙 기능은 **보안 점수** 입니다.

Security Center는 리소스, 구독 및 조직의 보안 이슈를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다.

보안 점수는 Azure Portal 페이지에 백분율 값으로 표시 되지만 기본 값도 명확 하 게 표시 됩니다.

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="포털에 표시 된 전체 보안 점수":::

보안을 강화 하려면 점수를 올리는 데 필요한 처리 중인 작업에 대해 Security Center의 권장 사항 페이지를 검토 합니다. 각 권장 사항에는 특정 문제를 해결 하는 데 도움이 되는 지침이 포함 되어 있습니다.

권장 사항은 **보안 제어** 로 그룹화 됩니다. 각 컨트롤은 관련 된 보안 권장 사항의 논리적 그룹 이며 취약 한 공격 노출 영역을 반영 합니다. 점수는 컨트롤 내의 단일 리소스에 대한 *모든* 권장 사항에 따라 수정해야만 향상됩니다. 조직이 각 개별 공격 노출 영역을 얼마나 잘 보호 하 고 있는지 확인 하려면 각 보안 제어의 점수를 검토 합니다.

자세한 내용은 아래의 [보안 점수를 계산 하는 방법](secure-score-security-controls.md#how-your-secure-score-is-calculated) 을 참조 하세요. 


## <a name="access-your-secure-score"></a>보안 점수 액세스

다음 섹션에 설명 된 대로 Azure Portal를 통해 또는 프로그래밍 방식으로 전체 보안 점수 뿐만 아니라 구독 당 점수를 확인할 수 있습니다.

- [포털에서 보안 점수 얻기](#get-your-secure-score-from-the-portal)
- [REST API에서 보안 점수 얻기](#get-your-secure-score-from-the-rest-api)
- [Azure 리소스 그래프 (ARG)에서 보안 점수 가져오기](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>포털에서 보안 점수 얻기

Security Center는 포털에서 점수를 두드러지게 표시 합니다. Security Center 개요 페이지의 첫 번째 주 타일입니다. 이 타일을 선택 하면 전용 보안 점수 페이지로 이동 하 여 구독 별로 세분화 된 점수를 볼 수 있습니다. 단일 구독을 선택 하 여 우선 순위가 지정 된 권장 사항의 세부 목록과 구독의 점수를 수정 하 게 될 잠재적 영향을 확인 합니다. 

Security Center의 포털 페이지에서 다음 위치에 보안 점수가 표시 됩니다.

- Security Center **개요** 의 타일 (주 대시보드):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Security Center 대시보드의 보안 점수":::

- 전용 **보안 점수** 페이지에서 구독 및 관리 그룹에 대 한 보안 점수를 볼 수 있습니다.

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Security Center의 보안 점수 페이지에서 구독에 대 한 보안 점수":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Security Center의 보안 점수 페이지에서 관리 그룹의 보안 점수":::

    > [!NOTE]
    > 충분 한 권한이 없는 모든 관리 그룹은 해당 점수를 "제한 됨"으로 표시 합니다. 

- **권장 사항** 페이지의 맨 위에:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Security Center 권장 사항 페이지의 보안 점수":::

### <a name="get-your-secure-score-from-the-rest-api"></a>REST API에서 보안 점수 얻기

보안 점수 API를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들어 [보안 점수 API](/rest/api/securitycenter/securescores) 를 사용 하 여 특정 구독에 대 한 점수를 가져올 수 있습니다. 또한 보안 [점수 컨트롤 API](/rest/api/securitycenter/securescorecontrols) 를 사용 하 여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

![API를 통해 단일 보안 점수 검색](media/secure-score-security-controls/single-secure-score-via-api.png)

보안 점수 API를 기반으로 하는 도구에 대 한 예제는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조 하세요. 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Azure 리소스 그래프 (ARG)에서 보안 점수 가져오기

Azure 리소스 그래프를 사용 하면 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).

인수를 사용 하 여 여러 구독의 보안 점수에 액세스 하려면:

1. Azure Portal에서 **Azure 리소스 그래프 탐색기** 를 엽니다.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure 리소스 그래프 탐색기 시작 * * 권장 사항 페이지" :::

1. (지침을 보려면 아래 예제를 사용 하 여) Kusto 쿼리를 입력 합니다.

    - 이 쿼리는 구독 ID, 점의 현재 점수 및 백분율을 반환 하 고 구독의 최대 점수를 반환 합니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 이 쿼리는 모든 보안 컨트롤의 상태를 반환 합니다. 각 컨트롤에 대해 비정상 리소스의 수, 현재 점수 및 최대 점수를 얻을 수 있습니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. **쿼리 실행** 을 선택 합니다.




## <a name="tracking-your-secure-score-over-time"></a>시간에 따른 보안 점수 추적

Pro 계정이 있는 사용자 Power BI 경우 **시간** 에 따른 보안 점수 Power BI 대시보드를 사용 하 여 시간에 따른 보안 점수를 추적 하 고 변경 내용을 조사할 수 있습니다.

> [!TIP]
> GitHub의 Azure Security Center 커뮤니티 전용 영역에서 보안 점수를 사용 하 여 프로그래밍 방식으로 작업 하는 데 사용할 수 있는 다른 도구 뿐만 아니라이 대시보드를 찾을 수 있습니다. https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

대시보드에는 보안 상태를 분석 하는 데 도움이 되는 다음과 같은 두 가지 보고서가 포함 되어 있습니다.

- **리소스 요약** -리소스 상태와 관련 하 여 요약 된 데이터를 제공 합니다.
- **보안 점수 요약** -점수 진행률과 관련 하 여 요약 된 데이터를 제공 합니다. "구독 당 시간에 따른 보안 점수" 차트를 사용 하 여 점수의 변화를 확인 합니다. 점수가 크게 변경 되는 경우 변경 될 수 있는 가능한 변경 내용에 대해서는 "보안 점수에 영향을 줄 수 있는 검색 된 변경 내용" 표를 확인 합니다. 이 테이블에는 권장 사항 중 하나에 대해 보안 상태가 변경 된 리소스, 새로 배포 된 리소스 또는 리소스가 삭제 됩니다.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="시간에 따른 보안 점수를 추적 하 고 변경 내용을 조사 하는 시간에 따른 선택적 보안 점수 Power BI 대시보드":::





## <a name="how-your-secure-score-is-calculated"></a>보안 점수를 계산 하는 방법 

전체적인 보안 점수에 대한 각 보안 컨트롤의 기여도는 권장 사항 페이지에 명확히 표시됩니다.

[![향상된 보안 점수로 보안 컨트롤 도입](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

보안 컨트롤을 위한 가능한 모든 포인트를 구하려면 모든 리소스가 보안 컨트롤 내의 모든 보안 권장 사항을 준수해야 합니다. 예를 들어, Security Center에는 관리 포트를 보호하는 방법에 대한 여러 권장 사항이 있습니다. 보안 점수를 변경 하려면 모두 수정 해야 합니다.

예를 들어 "시스템 업데이트 적용"이라는 보안 컨트롤의 최대 점수는 6포인트이며, 이 컨트롤의 잠재적 증가 값을 도구 설명에서 볼 수 있습니다.

[![보안 컨트롤 "시스템 업데이트 적용"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

시스템 업데이트 적용 컨트롤의 최대 점수는 항상 6입니다. 이 예제에는 50개의 리소스가 있습니다. 따라서 최대 점수를 50으로 나누면 결과적으로 모든 리소스는 0.12포인트를 기여합니다. 

* **잠재적 증가**(0.12 x 8개의 비정상 리소스 = 0.96) - 컨트롤 내에서 사용할 수 있는 나머지 포인트입니다. 이 컨트롤의 모든 권장 사항을 수정하면 점수가 2%씩 증가합니다(이 경우 0.96포인트가 1포인트로 반올림됨). 
* **현재 점수**(0.12 x 42개의 정상 리소스 = 5.04) - 이 컨트롤의 현재 점수입니다. 각 컨트롤의 점수로 전체 점수를 계산합니다. 이 예제에서 이 컨트롤은 현재 보안 합계에서 5.04포인트를 차지합니다.
* **최대 점수** - 컨트롤 내의 모든 권장 사항을 완료하여 얻을 수 있는 최대 포인트입니다. 컨트롤의 최대 점수는 해당 컨트롤의 상대적 중요도를 나타냅니다. 가장 먼저 작업할 이슈를 심사하려면 최대 점수 값을 사용합니다. 


### <a name="calculations---understanding-your-score"></a>계산 - 점수 이해

|메트릭|수식 및 예제|
|-|-|
|**보안 컨트롤의 현재 점수**|<br>![보안 컨트롤의 점수를 계산 하는 수식](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>각 개별 보안 컨트롤은 보안 점수에 기여합니다. 컨트롤 내의 권장 구성에 의해 영향을 받는 각 리소스는 컨트롤의 현재 점수에 기여합니다. 각 컨트롤의 현재 점수는 컨트롤 *내* 리소스의 상태를 측정한 것입니다.<br>![보안 컨트롤의 현재 점수를 계산할 때 사용되는 값을 보여 주는 도구 설명](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>이 예제에서 최대 점수 6은 정상 및 비정상 리소스의 합계인 78로 나뉩니다.<br>6 / 78 = 0.0769<br>이 값에 정상 리소스의 수(4)를 곱하여 현재 점수가 됩니다.<br>0.0769 * 4 = **0.31**<br><br>|
|**보안 점수**<br>단일 구독|<br>![구독의 보안 점수를 계산 하는 수식](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![모든 컨트롤이 사용하도록 설정된 단일 구독 보안 점수](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>이 예제에는 모든 보안 컨트롤을 사용할 수 있는 단일 구독이 있습니다(잠재적 최대 점수 60포인트). 점수로는 가능한 60포인트 중에서 28포인트가 표시되고 나머지 32포인트는 보안 컨트롤의 "잠재적 점수 증가" 수치에 반영됩니다.<br>![컨트롤 목록 및 잠재적 점수 증가](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**보안 점수**<br>여러 구독|<br>![여러 구독에 대 한 보안 점수를 계산 하는 수식](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>여러 구독에 대 한 결합 된 점수를 계산할 때 Security Center는 각 구독에 대 한 *가중치* 를 포함 합니다. 구독에 대 한 상대적 가중치는 리소스 수와 같은 요소에 따라 Security Center에 의해 결정 됩니다.<br>각 구독에 대 한 현재 점수는 단일 구독에 대 한 것과 동일한 방식으로 계산 되지만, 그 다음에는 수식에 표시 된 대로 가중치가 적용 됩니다.<br>여러 구독을 볼 때 보안 점수는 설정된 모든 정책 내의 모든 리소스를 평가하고, 각 보안 컨트롤의 최대 점수에 미치는 조합된 영향을 그룹화합니다.<br>![모든 컨트롤이 사용하도록 설정된 여러 구독의 보안 점수](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>조합된 점수는 평균이 **아니며**, 모든 구독에서 모든 리소스의 상태를 평가한 것입니다.<br>여기서도 권장 사항 페이지로 이동하여 사용할 수 있는 잠재적 포인트를 추가하면 현재 점수(24)와 사용 가능한 최대 점수(60) 간에 차이가 있다는 것을 알 수 있습니다.|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>보안 점수 계산에 포함 되는 권장 사항은 무엇입니까?

기본 제공 권장 사항만 보안 점수에 영향을 줍니다.

**미리 보기로** 플래그가 지정 된 권장 사항은 보안 점수 계산에 포함 되지 않습니다. 미리 보기 기간이 종료되면 점수에 기여할 수 있도록 가능한 경우 언제든지 수정해야 합니다.

미리 보기 추천 사항의 예는 다음과 같습니다.

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="미리 보기 플래그가 있는 추천 사항":::

## <a name="improve-your-secure-score"></a>보안 점수 향상

보안 점수를 향상시키려면 권장 사항 목록에서 보안 권장 사항을 수정하세요. 각 리소스에 대해 각 권장 사항을 수동으로 수정하거나 **빠른 수정!** 옵션(사용 가능한 경우)을 사용하여 권장 사항의 수정 작업을 리소스 그룹에 빠르게 적용할 수 있습니다. 자세한 내용은 [권장 사항에 따라 수정](security-center-remediate-recommendations.md)을 참조하세요.

점수를 개선 하 고 사용자가 점수에 부정적인 영향을 주는 리소스를 만들지 않도록 하는 또 다른 방법은 관련 권장 사항에 대해 적용 및 거부 옵션을 구성 하는 것입니다. [적용/거부 추천 사항을 사용하여 구성 오류 방지](prevent-misconfigurations.md)에서 자세히 알아보세요.

## <a name="security-controls-and-their-recommendations"></a>보안 제어 및 해당 권장 사항

다음 표에서는 Azure Security Center의 보안 컨트롤을 나열합니다. 각 컨트롤에 대해 *모든* 리소스의 컨트롤에 나열된 *모든* 권장 사항에 따라 수정할 경우 보안 점수에 추가될 수 있는 최대 포인트를 확인할 수 있습니다. 

Security Center와 함께 제공 되는 보안 권장 사항 집합은 각 조직의 환경에서 사용할 수 있는 리소스에 맞게 조정 됩니다. 권장 사항에서 [정책을 사용 하지 않도록 설정](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) 하 고 [특정 리소스를 제외](exempt-resource.md)하 여 권장 사항을 추가로 사용자 지정할 수 있습니다. 
 
모든 조직에서 할당 된 Azure Policy 이니셔티브를 신중 하 게 검토 하는 것이 좋습니다. 

> [!TIP]
> 이니셔티브 검토 및 편집에 대 한 자세한 내용은 [보안 정책 사용](tutorial-security-policy.md)을 참조 하세요. 

Security Center의 기본 보안 이니셔티브는 업계 모범 사례 및 표준을 기반으로 하지만 아래에 나열 된 기본 제공 권장 사항이 조직에 완전히 맞지 않는 시나리오가 있습니다. 따라서 보안을 손상 시 키 지 않고 기본 이니셔티브를 조정 해야 하는 경우가 있습니다. 충족 해야 하는 업계 표준, 규정 표준 및 벤치 마크입니다.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>보안 점수 FAQ

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>보안 컨트롤의 4가지 권장 사항 중 3가지를 해결하는 경우 보안 점수가 변경되나요?
아니요. 단일 리소스에 대한 모든 권장 사항을 수정할 때까지 변경되지 않습니다. 컨트롤에 대해 최대 점수를 얻으려면 모든 리소스에 대해 모든 권장 사항을 수정해야 합니다.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>권장 사항이 나에게 적용되지 않는 경우 정책에서 사용하지 않도록 설정해도 보안 컨트롤이 이행되고 보안 점수가 업데이트되나요?
예. 사용자 환경에 적용할 수 없는 경우 권장 사항을 사용하지 않도록 설정하는 것이 좋습니다. 특정 권장 사항을 사용하지 않도록 설정하는 방법에 대한 지침은 [보안 정책 사용 안 함](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)을 참조하세요.

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>보안 컨트롤에서 보안 점수에 대해 0포인트를 제공하면 무시해도 되나요?
경우에 따라 컨트롤 최대 점수가 0보다 클 수 있지만 영향은 0입니다. 리소스 수정을 위한 증분 점수가 크지 않은 경우 0으로 반올림됩니다. 이러한 권장 사항은 여전히 보안을 향상시키므로 무시하지 마세요. 유일한 예외는 "추가 모범 사례" 컨트롤입니다. 이러한 권장 사항을 수정하면 점수가 증가되지 않지만 전반적인 보안은 향상됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수와 해당 점수가 적용되는 보안 컨트롤에 대해 설명했습니다. 관련 자료는 다음 문서를 참조하세요.

- [권장 사항의 다양한 요소에 대해 알아보기](security-center-recommendations.md)
- [권장 사항을 수정하는 방법 알아보기](security-center-remediate-recommendations.md)
- [안전한 점수를 사용 하 여 프로그래밍 방식으로 작업 하기 위한 GitHub 기반 도구 보기](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)