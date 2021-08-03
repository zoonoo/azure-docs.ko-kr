---
title: Azure ExpressRoute에 대한 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용하여 Azure ExpressRoute에 대한 비용을 계획하고 관리하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: c4a2113a5aa95c1bacf7be0f4b376b9377412371
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971691"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Azure ExpressRoute에 대한 비용 계획 및 관리

이 문서에서는 Azure ExpressRoute에 대한 비용을 계획하고 관리하는 방법을 설명합니다. 먼저 Azure 가격 계산기를 사용하여 ExpressRoute 비용을 계획한 후 서비스에 대한 리소스를 추가하여 비용을 추정합니다. 그런 다음, Azure 리소스를 추가할 때 예상 비용을 검토합니다. 

Azure ExpressRoute 리소스 사용을 시작한 후에는 Cost Management 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 예상 비용을 검토하고 지출 추세를 파악하여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure ExpressRoute 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure ExpressRoute 비용을 계획하고 관리하는 방법을 설명하지만 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management에서의 비용 분석은 대부분의 Azure 계정 유형을 지원하지만 일부는 지원하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="local-vs-standard-vs-premium"></a>로컬, 표준 및 프리미엄

Azure ExpressRoute에는 [*로컬*](./expressroute-faqs.md#expressroute-local), *표준* 및 [*프리미엄*](./expressroute-faqs.md#expressroute-premium)의 세 가지 회로 SKU가 있습니다. ExpressRoute 사용량에 대한 요금이 청구되는 방식은 이러한 세 가지 SKU 유형에 따라 달라집니다. 로컬 SKU를 사용하면 무제한 데이터 요금제를 통해 자동으로 요금이 청구됩니다. 표준 및 프리미엄 SKU를 사용하면 데이터 요금제 또는 무제한 데이터 요금제 중에서 선택할 수 있습니다. Global Reach 추가 기능을 사용하는 경우를 제외하고 모든 수신 데이터는 무료로 제공됩니다. 비용 및 예산 최적화를 위해 워크로드에 가장 적합한 SKU 유형 및 데이터 요금제를 이해하는 것이 중요합니다.

## <a name="estimate-costs-before-using-azure-expressroute"></a>Azure ExpressRoute를 사용하기 전에 비용 추정

Azure ExpressRoute 회로를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 비용을 추정하세요. 

1. 왼쪽에서 **네트워킹** 을 선택한 다음 **Azure ExpressRoute** 를 선택하여 시작합니다. 

1. 피어링 위치에 따라 적절한 *영역* 을 선택합니다. 드롭다운에서 적절한 *영역* 을 선택하려면 [ExpressRoute 연결 공급자](./expressroute-locations-providers.md#partners)를 참조하세요. 

1. 그런 다음 예상하려는 *SKU*, *회로 속도* 및 *데이터 요금제* 를 선택합니다. 

1. *추가 아웃바운드 데이터 전송* 에서 한 달 동안 사용할 아웃바운드 데이터의 양의 추정값(GB)을 입력합니다. 

1. 마지막으로 이 추정값에 *Global Reach 추가 기능* 을 추가할 수 있습니다.

다음 스크린샷은 계산기를 사용하여 비용 추정을 보여 줍니다.

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 계산기의 ExpressRoute 비용 추정":::

자세한 내용은 [Azure ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 참조하세요.

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute 게이트웨이 추정 비용

ExpressRoute 게이트웨이를 사용하여 가상 네트워크를 ExpressRoute 회로에 연결하는 경우 다음 단계를 사용하여 게이트웨이 사용 비용을 추정합니다.

1. 왼쪽에서 **네트워킹** 을 선택한 다음 **VPN 게이트웨이** 를 선택하여 시작합니다. 

1. 게이트웨이의 *지역* 을 선택한 다음 *유형* 을 **ExpressRoute 게이트웨이** 로 변경합니다.

1. 드롭다운에서 *게이트웨이 유형* 을 선택합니다.

1. *게이트웨이 시간* 을 입력합니다. (720시간 = 30일)

## <a name="understand-the-full-billing-model-for-expressroute"></a>ExpressRoute에 대한 전체 청구 모델 이해

Azure ExpressRoute는 새 리소스를 배포할 때 ExpressRoute에 따라 비용이 늘어나는 Azure 인프라에서 실행됩니다. 추가 인프라로 비용이 누적될 수 있다는 것을 이해하는 것이 중요합니다. 배포된 리소스를 변경할 때 이 비용을 관리해야 합니다. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>일반적으로 ExpressRoute를 사용하여 늘어나는 비용

#### <a name="expressroute"></a>ExpressRoute

ExpressRoute 회로를 만들 때 가상 네트워크를 회로에 연결하는 ExpressRoute 게이트웨이를 만들도록 선택할 수 있습니다. ExpressRoute 게이트웨이는 시간 단위 요금과 ExpressRoute 회로 비용이 청구됩니다. [ExpressRoute 가격 책정](https://azure.microsoft.com/pricing/details/expressroute)을 참조하고 ExpressRoute 게이트웨이를 선택하여 다른 게이트웨이 SKU의 요금을 확인합니다.

인바운드 데이터 전송은 세 SKU 모두에 대한 ExpressRoute 회로의 월별 비용에 포함됩니다. 아웃바운드 데이터 전송은 무제한 데이터 플랜에만 포함됩니다. 데이터 요금제의 경우 아웃바운드 데이터 전송에는 [피어링 위치](expressroute-locations-providers.md#partners)의 영역 번호에 따라 사용된 GB당 요금이 청구됩니다.

#### <a name="expressroute-direct"></a>ExpressRoute Direct

ExpressRoute Direct에는 로컬 및 표준 SKU ExpressRoute 회로에 대한 회로 요금이 포함된 월간 포트 요금이 부과됩니다. 프리미엄 SKU 회로의 경우 추가 회로 요금이 발생합니다. 아웃바운드 데이터 전송은 피어링 위치의 영역 번호에 따라 사용된 GB당 요금이 청구됩니다. 아웃바운드 데이터 요금은 표준 및 프리미엄 SKU에만 적용됩니다.
 
#### <a name="expressroute-global-reach"></a>ExpressRoute Global Reach

ExpressRoute Global Reach는 ExpressRoute 및 ExpressRoute Direct에서 ExpressRoute 회로를 함께 연결하도록 하는 데 사용할 수 있는 추가 기능입니다. 인바운드 및 아웃바운드 데이터 전송에는 피어링 위치의 영역 번호에 따라 사용된 GB당 요금이 청구됩니다.

### <a name="costs-might-accrue-after-resource-deletion"></a>비용은 리소스 삭제 후 늘어날 수 있습니다.

ExpressRoute 회로를 삭제한 후 ExpressRoute 게이트웨이가 있는 경우 삭제할 때까지 비용이 계속 청구됩니다.

### <a name="using-azure-prepayment-credit"></a>Azure 선불 크레딧 사용

Azure 선불(이전에는 요금 약정이라고 함) 크레딧을 사용하여 ExpressRoute 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용하여 Azure Marketplace에서 사용하는 타사 제품 및 서비스에 대한 요금을 지불할 수 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

ExpressRoute를 사용하여 Azure 리소스를 사용하는 경우 비용이 발생합니다. Azure 리소스 사용량 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 다릅니다. ExpressRoute 사용이 시작되는 즉시 비용이 발생하고 [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 확인할 수 있습니다.

비용 분석을 사용하는 경우 다양한 시간 간격에 대한 그래프 및 테이블에서 ExpressRoute 회로 비용을 확인할 수 있습니다. 몇 가지 예로 일, 현재 및 이전 달 및 연도에 따라 확인할 수 있습니다. 예산 및 예상 비용에 대한 비용도 조회할 수 있습니다. 시간이 지남에 따라 더 긴 보기로 전환하면 지출 추세를 파악하는 데 도움이 됩니다. 그리고 과도한 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 초과된 부분도 쉽게 확인할 수 있습니다.

비용 분석에서 ExpressRoute 비용을 보려면 다음을 수행합니다.

1. Azure Portal에 로그인합니다.

1. **구독** 으로 이동하여 목록에서 구독을 선택한 다음, 메뉴에서 **비용 분석** 을 선택합니다. **범위** 를 선택하여 비용 분석에서 다른 범위로 전환합니다. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시됩니다.

    비용 분석을 처음 열 때 실제 월별 비용이 표시됩니다. 다음은 월별 사용의 모든 비용을 보여 주는 예입니다.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="구독에 대한 누적 비용을 보여 주는 예":::
    

1.  Expressroute와 같은 단일 서비스의 비용을 줄이려면 **필터 추가** 를 선택한 다음 **서비스 이름** 을 선택합니다. 그런 다음 **ExpressRoute** 를 선택합니다.

    다음은 ExpressRoute에 대한 비용을 보여 주는 예입니다.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="ExpressRoute의 누적 비용을 보여 주는 예":::

앞의 예에서 서비스에 대한 현재 비용이 표시됩니다. 또한 Azure 지역(위치)별 비용 및 리소스 그룹별 ExpressRoute 비용이 표시됩니다. 여기에서 자신의 비용을 살펴볼 수 있습니다.

## <a name="create-budgets-and-alerts"></a>예산 및 경고 만들기

[예산](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 [경고](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. 예산 및 경고는 Azure 구독 및 리소스 그룹에 대해 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 

모니터링에 더 많은 세분성을 제공하려는 경우 Azure의 특정 리소스 또는 서비스에 대한 필터를 사용하여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 발생시키는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산을 만들 때 사용하는 필터 옵션에 대한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

스토리지 계정으로 [비용 데이터를 내보낼](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 수도 있습니다. 이는 비용에 대한 추가 데이터 분석을 직접 수행해야 하거나 다른 사용자가 수행하는 경우에 유용합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용하여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정으로 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 세트를 검색하는 데 권장되는 방법입니다.

## <a name="next-steps"></a>다음 단계

- Azure ExpressRoute를 사용한 가격 책정의 작동 방법에 대해 자세히 알아봅니다. [Azure ExpressRoute 개요 가격 책정](https://azure.microsoft.com/pricing/details/expressroute/)을 참조하세요.
- [Azure Cost Management를 통해 클라우드 투자를 최적화하는 방법](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 통한 비용 관리에 대해 알아봅니다.
- [예기치 않은 비용을 방지](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법을 알아봅니다.
- [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행합니다.