---
title: Azure API Management에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure API Management에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: ab634e697e9d46d9e3b8c1b9597fc7d0544ba991
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97633027"
---
# <a name="plan-and-manage-costs-for-api-management"></a>API Management에 대 한 비용 계획 및 관리

이 문서에서는 Azure API Management에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure 가격 계산기를 사용 하 여 비용을 예측 하는 서비스에 대 한 리소스를 추가 하기 전에 API Management 비용을 계획할 수 있습니다. API Management 리소스 사용을 시작한 후에는 Cost Management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다. 

API Management 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 API Management에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만, 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

Cost Management의 비용 분석은 대부분의 Azure 계정 유형을 지원 하지만 일부는 지원 하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 최소한 Azure 계정에 대 한 읽기 권한이 있어야 합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimate-costs-before-using-api-management"></a>API Management를 사용 하기 전에 비용 추정

API Management를 추가 하기 전에 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 비용을 예측 합니다. 

1. *API Management* 를 검색 하거나 **통합**  >  **API Management** 를 선택 합니다.
1. API Management 서비스 인스턴스에 대 한 기본 비용 추정치를 추가 하려면 **보기** 를 선택 합니다.

> [!NOTE]
> 이 예제에 표시 된 비용은 데모용 으로만 사용 됩니다. 최신 가격 정보는 [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/) 을 참조 하세요.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="개발자 계층의 비용 예측":::

* 기본 예상 비용은 **개발자** [서비스 계층](api-management-features.md) 에서 [용량 단위가](api-management-capacity.md)1 인 API Management 서비스 인스턴스를 기반으로 합니다. 개발자 계층은 비프로덕션 사용 사례 및 평가를 위한 것입니다. 서비스 수준 계약에서 지원 되지 않습니다.

* 추가 용량 단위 또는 다른 서비스 계층에 대 한 비용을 계산 하려면 **단위** 및 **계층** 드롭다운에서 기타 옵션을 선택 합니다.

* 기능 가용성 및 서비스 계층에 따라 [자체 호스팅 게이트웨이의](self-hosted-gateway-overview.md)사용에 대 한 추가 요금이 적용 될 수 있습니다.

추가 가격 및 기능에 대 한 자세한 내용은 다음을 참조 하세요.

* [API Management 가격 책정](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API Management 계층의 기능 기반 비교](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>API Management에서 금액 크레딧 사용

EA 금액 약정 크레딧을 사용 하 여 API Management 요금을 지불할 수 있습니다. 그러나 EA 현금 약정 크레딧을 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

API Management에서 Azure 리소스를 사용 하는 경우 비용이 발생 합니다. Azure 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 단위 사용 (바이트, 메가바이트 등)에 따라 달라 집니다. API Management를 시작 하는 즉시 비용이 발생 하며 비용 [분석](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)에서 비용을 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 API Management 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 또한 예산 및 예상 비용에 대 한 비용도 볼 수 있습니다. 시간이 지남에 따라 더 긴 뷰로 전환 하면 지출 추세를 식별 하는 데 도움이 됩니다. 그리고 과도 한 지출이 발생 한 위치를 확인할 수 있습니다. 예산을 만든 경우에는 해당 위치를 쉽게 확인할 수도 있습니다.

> [!NOTE]
> 이 예제에 표시 된 비용은 데모용 으로만 사용 됩니다. 비용은 리소스 사용량과 현재 가격에 따라 달라 집니다.

비용 분석에서 API Management 비용을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://azure.microsoft.com)에 로그인합니다.
1. **Cost Management + 청구** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음 **청구 범위** 를 선택 합니다. 예를 들어 목록에서 구독을 선택 합니다.
1. 메뉴에서 **Cost Management** 를 선택한 다음 **비용 분석** 을 선택 합니다.
1. 기본적으로 모든 서비스에 대 한 월별 비용은 첫 번째 도넛형 차트에 표시 됩니다. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="구독에 대 한 월별 비용":::

1. API Management와 같은 단일 서비스에 대 한 비용을 좁히려면 **필터 추가** 를 선택한 다음 **서비스 이름** 을 선택 합니다. 그런 다음 **API Management** 를 선택 합니다.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="API Management에 대 한 누적 비용을 보여 주는 예제":::

앞의 예제에서 서비스에 대 한 현재 비용이 표시 됩니다. Azure 지역 (위치) 및 리소스 그룹별 비용 API Management 비용도 표시 됩니다. 여기에서 비용을 직접 살펴볼 수 있습니다.

## <a name="create-budgets"></a>예산 만들기

[예산을](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산 생성 시의 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>API Management에 대 한 비용을 관리 하 고 절감 하는 기타 방법

### <a name="choose-tier"></a>계층 선택

시나리오에 적합 한 서비스 계층을 결정 하는 데 도움이 되도록 [Azure API Management 계층의 기능 기반 비교](api-management-features.md) 를 검토 합니다. 다양 한 서비스 계층에서는 다양 한 사용 사례에 맞게 설계 된 다양 한 기능 및 비용의 조합을 지원 합니다. 언제 든 지 다른 서비스 계층으로 [업그레이드](upgrade-and-scale.md) 합니다.

* **소비** 서비스 계층은 고정 비용이 발생 하지 않는 간단 하 고 서버를 사용 하지 않는 옵션을 제공 합니다. 특정 임계값을 초과 하는 서비스에 대 한 API 호출 수를 기준으로 요금이 청구 됩니다. 용량 또한 서비스의 부하에 따라 자동으로 크기가 조정 됩니다.
* 다른 API Management 계층은 월별 비용을 발생 하 고 평가 및 프로덕션 워크 로드에 대 한 더 많은 처리량과 더 풍부한 기능 집합을 제공 합니다.

### <a name="scale-using-capacity-units"></a>용량 단위를 사용 하 여 크기 조정

소비 서비스 계층을 제외 하 고 API Management [*용량 단위*](api-management-capacity.md)를 추가 하거나 제거 하 여 크기 조정을 지원 합니다. API Management 인스턴스에서 부하가 증가 하는 경우 더 높은 서비스 계층으로 업그레이드 하는 것 보다 용량 단위를 추가 하는 것이 더 경제적 일 수 있습니다. 최대 단위 수는 서비스 계층에 따라 달라 집니다.

각 용량 단위에는 서비스 계층에 따라 달라 지는 특정 요청 처리 기능이 있습니다. 예를 들어 기본 계층의 단위는 초당 약 1000 요청에 대 한 예상 최대 처리량을 갖습니다. 

단위를 추가 하거나 제거할 때 용량 및 비용을 비례적으로 조정 합니다. 예를 들어 표준 계층의 두 단위는 초당 약 2000 요청에 대 한 예상 처리량을 제공 합니다. 실제 처리량은 요청 또는 응답의 크기, 연결 패턴, 요청 하는 클라이언트 수 및 기타 요인으로 인해 달라질 수 있습니다.

API Management 인스턴스의 용량 메트릭을 [모니터링](api-management-howto-use-azure-monitor.md) 하 여 더 많은 부하를 수용할 수 있도록 API Management 인스턴스를 확장 하거나 업그레이드할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.
- API Management [용량](api-management-capacity.md)에 대해 알아봅니다.
- [Azure Portal](upgrade-and-scale.md)를 사용 하 여 API Management 크기를 조정 하 고 업그레이드 하는 단계 및 자동 크기 조정 [에 대해 알아봅니다.](api-management-howto-autoscale.md)