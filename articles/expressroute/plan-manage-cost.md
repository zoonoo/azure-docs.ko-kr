---
title: Azure Express 경로에 대 한 비용 관리 계획
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Express 경로에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 9b74f6e82e39955554c13f6ce3490bc3c22c2b98
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600463"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Azure Express 경로에 대 한 비용 계획 및 관리

이 문서에서는 Express 경로에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure 가격 계산기를 사용 하 여 비용을 예측 하는 서비스에 대 한 리소스를 추가 하기 전에 Express 경로 비용을 계획할 수 있습니다. 그런 다음 Azure 리소스를 추가할 때 예상 비용을 검토 합니다. 

Express 경로 리소스 사용을 시작한 후 Cost Management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다. 

Express 경로 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 이 문서에서는 Express 경로에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Cost Management의 비용 분석은 대부분의 Azure 계정 유형을 지원 하지만 일부는 지원 하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 최소한 Azure 계정에 대 한 읽기 권한이 있어야 합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="local-vs-standard-vs-premium"></a>로컬과 표준 비교

Express 경로에는 [*로컬*](./expressroute-faqs.md#expressroute-local), *표준* 및 [*프리미엄*](./expressroute-faqs.md#expressroute-premium)의 3 가지 회로 SKU가 있습니다. Express 경로 사용에 대 한 요금이 청구 되는 방식은 이러한 세 가지 SKU 유형에 따라 달라 집니다. 로컬 SKU를 사용 하면 무제한 데이터 요금제를 사용 하 여 자동으로 요금이 청구 됩니다. Standard 및 Premium SKU를 사용 하 여 데이터 요금제 또는 무제한 데이터 요금제 중에서 선택할 수 있습니다. Global Reach 추가 기능을 사용 하는 경우를 제외 하 고 모든 수신 데이터는 무료로 제공 됩니다. 비용 및 예산 최적화를 위해 작업에 가장 적합 한 SKU 유형 및 데이터 요금제를 이해 하는 것이 중요 합니다.

## <a name="estimate-costs"></a>비용 예측

Express 경로 회로를 만들기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 비용을 예측 합니다. 

1. 왼쪽에서 **네트워킹** 을 선택 하 고 **Azure express** 경로를 선택 하 여 시작 합니다. 

1. 피어 링 위치에 따라 적절 한 *영역* 을 선택 합니다. 드롭 다운에서 적절 한 *영역* 을 선택 하려면 [express 경로 연결 공급자](./expressroute-locations-providers.md#partners) 를 참조 하세요. 

1. 그런 다음 계산 하려는 *SKU*, *회로 속도* 및 *데이터 요금제* 를 선택 합니다. 

1. *추가 아웃 바운드 데이터 전송* 에서 한 달 동안 사용할 수 있는 아웃 바운드 데이터의 양에 대 한 예상 값 (GB)을 입력 합니다. 

1. 마지막으로, 예측에 *Global Reach 추가 기능* 을 추가할 수 있습니다.

다음 스크린샷은 계산기를 사용 하 여 비용 예측을 보여 줍니다.

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Azure 계산기에서 Express 경로 비용 예상":::

자세한 내용은 [Azure express 경로 가격](https://azure.microsoft.com/pricing/details/expressroute/)을 참조 하세요.

### <a name="expressroute-gateway-estimated-cost"></a>Express 경로 게이트웨이 예상 비용

Express 경로 게이트웨이를 사용 하 여 가상 네트워크를 Express 경로 회로에 연결 하는 경우 다음 단계를 사용 하 여 게이트웨이 사용에 대 한 비용을 예측 합니다.

1. 왼쪽에서 **네트워킹** 을 선택 하 고 **VPN Gateway** 를 선택 하 여 시작 합니다. 

1. 게이트웨이의 *지역을* 선택 하 고 *유형* 을 **express 경로 게이트웨이** 로 변경 합니다.

1. 드롭다운에서 *게이트웨이 유형을* 선택 합니다.

1. *게이트웨이 시간* 을 입력 합니다. (720 시간 = 30 일)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Express 경로에 대 한 전체 청구 모델 이해

Express 경로는 새 리소스를 배포할 때 Express 경로와 함께 비용을 발생 시키는 Azure 인프라에서 실행 됩니다. 추가 인프라가 비용을 계산 하는 것을 이해 하는 것이 중요 합니다. 배포 된 리소스를 변경할 때이 비용을 관리 해야 합니다. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>일반적으로 Express 경로를 사용 하 여 비용을 계산 하는 비용

Express 경로 회로를 만들 때 가상 네트워크를 회로에 연결 하는 Express 경로 게이트웨이를 만들도록 선택할 수 있습니다. 게이트웨이는 시간 단위로 요금이 청구 되 고 Express 경로 회로 비용이 청구 됩니다. [Express 경로 가격](https://azure.microsoft.com/en-us/pricing/details/expressroute) 을 참조 하 고 Express 경로 게이트웨이를 선택 하 여 다른 게이트웨이 sku의 요금을 확인 하세요.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>리소스 삭제 후 비용이 발생할 수 있습니다.

Express 경로 회로를 삭제 한 후 Express 경로 게이트웨이가 있는 경우 삭제할 때까지 비용에 대 한 요금이 계속 청구 됩니다.

### <a name="using-azure-prepayment-credit"></a>Azure 선불 크레딧 사용

Azure 선불 (이전에는 금액 약정 이라고 함) 크레딧을 사용 하 여 Express 경로 요금을 지불할 수 있습니다. 그러나 Azure 선불 크레딧을 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

Express 경로를 사용 하 여 Azure 리소스를 사용 하는 경우 비용이 발생 합니다. Azure 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 단위 사용 (바이트, 메가바이트 등)에 따라 달라 집니다. Express 경로 사용이 시작 되는 즉시 비용이 발생 하며 비용 [분석](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 볼 수 있습니다.

비용 분석을 사용 하는 경우 다양 한 시간 간격에 대 한 그래프 및 테이블에서 Express 경로 회로 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 또한 예산 및 예상 비용에 대 한 비용도 볼 수 있습니다. 시간이 지남에 따라 더 긴 뷰로 전환 하면 지출 추세를 식별 하는 데 도움이 됩니다. 그리고 과도 한 지출이 발생 한 위치를 확인할 수 있습니다. 예산을 만든 경우에는 해당 위치를 쉽게 확인할 수도 있습니다.

비용 분석에서 Express 경로를 보려면 다음을 수행 합니다.

1. Azure Portal에 로그인합니다.

1. **구독** 으로 이동 하 여 목록에서 구독을 선택한 다음 메뉴에서 **비용 분석** 을 선택 합니다. 비용 분석에서 다른 범위로 전환 하려면 **범위** 를 선택 합니다. 기본적으로 서비스 비용은 첫 번째 도넛형 차트에 표시 됩니다.

    처음에는 비용 분석을 열 때 실제 월별 비용이 표시 됩니다. 모든 월간 사용 비용을 보여 주는 예제는 다음과 같습니다.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="구독에 대 한 누적 비용을 보여 주는 예제":::
    

1.  Express 경로와 같은 단일 서비스에 대 한 비용을 좁히려면 **필터 추가** 를 선택 하 고 **서비스 이름** 을 선택 합니다. 그런 다음 **express** 경로를 선택 합니다.

    다음은 Express 경로에 대 한 비용을 보여 주는 예제입니다.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Express 경로에 대 한 누적 비용을 보여 주는 예제":::

앞의 예제에서 서비스에 대 한 현재 비용이 표시 됩니다. 리소스 그룹별 Azure 지역 (위치) 및 Express 경로 비용을 기준으로 하는 비용도 표시 됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets-and-alerts"></a>예산 및 경고 만들기

[예산을](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산을 만들 때의 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="next-steps"></a>다음 단계

- Azure Express 경로를 사용 하는 가격 책정에 대해 자세히 알아보세요. [Azure express 경로 개요 가격](https://azure.microsoft.com/en-us/pricing/details/expressroute/)을 참조 하세요.
- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.
