---
title: Azure 코스모스 DB의 비용 계획 및 관리
description: Azure 포털에서 비용 분석을 사용하여 Azure Cosmos DB의 비용을 계획하고 관리하는 방법을 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152587"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure 코스모스 DB의 비용 계획 및 관리

이 문서에서는 Azure Cosmos DB에 대한 비용을 계획하고 관리하는 방법에 대해 설명합니다. 먼저 Azure Cosmos DB 용량 계산기를 사용하여 리소스를 추가하기 전에 비용을 계획할 수 있습니다. 다음으로 Azure 리소스를 추가할 때 예상 비용을 검토할 수 있습니다. Azure Cosmos DB 리소스를 사용하기 시작한 후에는 비용 관리 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 또한 예상 비용을 검토하고 지출 추세를 식별하여 조치를 수행할 수 있는 영역을 식별할 수 있습니다.

Azure Cosmos DB의 비용은 Azure 청구서의 월별 비용의 일부일 뿐입니다. 다른 Azure 서비스를 사용하는 경우 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. 이 문서에서는 Azure Cosmos DB의 비용을 계획하고 관리하는 방법을 설명합니다. Azure Cosmos DB의 비용 관리에 익숙한 후에는 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리하기 위해 유사한 방법을 적용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

## <a name="review-estimated-costs-with-capacity-calculator"></a>용량 계산기로 예상 비용 검토

Azure [Cosmos DB 용량 계산기를](https://cosmos.azure.com/capacitycalculator/) 사용하여 Azure Cosmos 계정에서 리소스를 만들기 전에 비용을 예측합니다. 용량 계산기는 워크로드의 필요한 처리량과 비용을 추정하는 데 사용됩니다. Azure Cosmos 데이터베이스 및 컨테이너를 적절한 양의 프로비저닝된 처리량 또는 [요청 단위(RU/s)로](request-units.md)구성하는 것은 비용과 성능을 최적화하는 데 필수적입니다. API 유형, 지역 수, 항목 크기, 초당 읽기/쓰기 요청, 비용 견적을 얻기 위해 저장된 총 데이터와 같은 세부 정보를 입력해야 합니다. 용량 계산기에 대한 자세한 내용은 [예상](estimate-ru-with-capacity-planner.md) 문서를 참조하세요.

다음 스크린샷은 용량 계산기를 사용하여 처리량 및 비용 추정을 보여 주며,

![Azure 코스모스 DB 용량 계산기의 비용 추정](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Azure 포털에서 예상 비용 검토

Azure 포털에서 Azure Cosmos DB 리소스를 만들 때 예상 비용을 확인할 수 있습니다. 다음 단계를 사용하여 비용 견적을 검토합니다.

1. Azure 포털에 로그인하고 Azure Cosmos 계정으로 이동합니다.
1. **데이터 탐색기로**이동합니다.
1. 그래프 컨테이너와 같은 새 컨테이너를 만듭니다.
1. 400 RU/s와 같은 워크로드에 필요한 처리량을 입력합니다. 처리량 값을 입력한 후 다음 스크린샷과 같이 가격 예상치를 볼 수 있습니다.

   ![Azure 포털의 비용 추정](./media/plan-manage-costs/cost-estimate-portal.png)

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액에 대한 지출을 방지합니다. Azure 리소스를 만들고 사용하면 크레딧이 사용됩니다. 크레딧 한도에 도달하면 배포한 리소스가 해당 청구 기간의 나머지 기간 동안 비활성화됩니다. 신용 한도는 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대한 자세한 내용은 [Azure 지출 한도를](../billing/billing-spending-limit.md)참조하십시오.

## <a name="use-budgets-and-cost-alerts"></a>예산 및 비용 경고 사용

[예산](../cost-management/tutorial-acm-create-budgets.md)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대해 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 그러나 Azure Cosmos DB의 비용과 같은 개별 Azure 서비스 비용을 관리하는 기능이 제한되어 있을 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Cosmos DB에서 리소스를 사용하면 비용이 발생합니다. 리소스 사용 단위 비용은 시간 간격(초, 분, 시간 및 일)에 따라 또는 요청 단위 사용량에 따라 다릅니다. Azure Cosmos DB 사용이 시작되자마자 비용이 발생하며 Azure 포털의 [비용 분석](../cost-management/quick-acm-cost-analysis.md) 창에서 볼 수 있습니다.

비용 분석을 사용하는 경우 서로 다른 시간 간격에 대한 그래프 및 테이블에서 Azure Cosmos DB 비용을 볼 수 있습니다. 일, 현재, 이전 월 및 연도별로 몇 가지 예가 있습니다. 예산 및 예상 비용에 대한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 조회수로 전환하면 지출 추세를 파악하고 초과 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 예산을 초과한 위치도 쉽게 확인할 수 있습니다. 비용 분석에서 Azure Cosmos DB 비용을 보려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

1. 비용 **관리 + 청구** 창을 열고 메뉴에서 **비용 관리를** 선택한 다음 **비용 분석을**선택합니다. 그런 다음 **범위** 드롭다운에서 특정 구독의 범위를 변경할 수 있습니다.

1. 기본적으로 모든 서비스에 대한 비용은 첫 번째 도넛 차트에 표시됩니다. "Azure 코스모스 DB"라고 표시된 차트에서 영역을 선택합니다.

1. Azure Cosmos DB와 같은 단일 서비스에 대한 비용을 좁히려면 **필터 추가를** 선택한 다음 **서비스 이름을**선택합니다. 그런 다음 목록에서 **Azure 코스모스 DB를 선택합니다.** 다음은 Azure 코스모스 DB의 비용을 보여주는 예제입니다.
 
   ![비용 분석 창으로 비용 모니터링](./media/plan-manage-costs/cost-analysis-pane.png)

앞의 예제에서는 2월 의 Azure Cosmos DB의 현재 비용이 표시됩니다. 차트에는 위치 및 리소스 그룹별 Azure Cosmos DB 비용도 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 가격 책정이 작동하는 방식에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](optimize-cost-queries.md)
* [Azure Cosmos DB의 스토리지 비용 최적화](optimize-cost-storage.md)