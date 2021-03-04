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
ms.openlocfilehash: b19a7c156abf32e2a0f6d70717145a6ed5ab42ce
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099678"
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


> [!div class="nextstepaction"]
> [보안 점수 액세스 및 추적](secure-score-access-and-track.md)