---
title: Azure Cosmos DB에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: a73d155d20d09573ad5e1b2597678291aea4c92b
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980578"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 비용 계획 및 관리
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure Cosmos DB 용량 계산기를 사용 하 여 리소스를 만들기 전에 워크 로드 비용을 예측 합니다. 나중에 예상 비용을 검토 하 고 리소스 만들기를 시작할 수 있습니다.

Azure Cosmos DB 리소스 사용을 시작한 후에는 Cost Management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure Cosmos DB 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure Cosmos DB에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만, 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="provisioned-throughput-or-serverless"></a>프로 비전 된 처리량 또는 서버 리스

Azure Cosmos DB는 [프로 비전 된 처리량](set-throughput.md) 및 [서버](serverless.md)를 사용 하지 않는 두 가지 유형의 용량 모드를 지원 합니다. Azure Cosmos DB 사용에 대 한 요금이 청구 되는 방식은 이러한 두 모드 사이에서 매우 다양 하므로 워크 로드에 가장 적합 한 것을 선택 하는 것이 중요 합니다. 이 선택을 수행 하는 방법에 대 한 지침과 권장 사항은 [프로 비전 된 처리량과 서버 리스 서버 간 선택 방법](throughput-serverless.md) 문서를 참조 하세요.

### <a name="cost-analysis"></a>비용 분석

Cost Management의 비용 분석은 대부분의 Azure 계정 유형을 지원 하지만 일부는 지원 하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 최소한 Azure 계정에 대 한 읽기 권한이 있어야 합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Azure Cosmos DB를 사용 하기 전에 프로 비전 된 처리량 비용 예측

프로 비전 된 처리량 모드에서 Azure Cosmos DB를 사용 하려는 경우 Azure Cosmos 계정에 리소스를 만들기 전에 [Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/) 를 사용 하 여 비용을 예측 합니다. 용량 계산기는 워크 로드에 필요한 처리량 및 비용의 추정치를 얻는 데 사용 됩니다. 적절 한 크기의 프로 비전 된 처리량을 사용 하 여 Azure Cosmos 데이터베이스 및 컨테이너를 구성 하거나 작업에 대 한 [요청 단위 (r u/초)](request-units.md)를 구성 하는 것은 비용과 성능을 최적화 하는 데 필요 합니다. API 형식, 지역 수, 항목 크기, 초당 읽기/쓰기 요청, 예상 비용을 얻기 위해 저장 된 총 데이터 등의 세부 정보를 입력 해야 합니다. 용량 계산기에 대해 자세히 알아보려면 [예상](estimate-ru-with-capacity-planner.md) 문서를 참조 하세요.

다음 스크린샷은 용량 계산기를 사용 하 여 처리량 및 비용 예측을 보여 줍니다.

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Azure Cosmos DB 용량 계산기의 예상 비용":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Azure Cosmos DB를 사용 하기 전에 서버 리스 비용 예측

서버를 사용 하지 않는 모드로 Azure Cosmos DB를 사용 하려면 월 기준으로 사용할 수 있는 [요청 단위](request-units.md) 및 GB의 저장소 수를 예상 해야 합니다. 한 달에 발생 하는 데이터베이스 작업의 수를 평가 하 여 필요한 요청 단위 양을 예측 하 고 해당 하는 시간을 해당 하는 작업 비용에 곱할 수 있습니다. 다음 표에서는 일반적인 데이터베이스 작업에 대 한 예상 비용을 보여 줍니다.

| 작업(Operation) | 예상 비용 | 참고 |
| --- | --- | --- |
| 항목 만들기 | 5RU | 5 개 미만의 속성을 인덱싱하는 1kb 항목에 대 한 평균 비용 |
| 항목 업데이트 | 10RU | 5 개 미만의 속성을 인덱싱하는 1kb 항목에 대 한 평균 비용 |
| 해당 ID 및 파티션 키로 개별 항목 읽기 (지점 읽기) | 1RU | 1kb 항목에 대 한 평균 비용 |
| 항목 삭제 | 5RU | |
| 쿼리 실행 | 10RU | [인덱싱을](index-overview.md) 최대한 활용 하 고 100 결과를 반환 하는 쿼리의 평균 비용 |

> [!IMPORTANT] 
> 위의 표에서 메모를 확인 합니다. 작업의 실제 비용을 보다 정확 하 게 예측 하기 위해 [Azure Cosmos 에뮬레이터](local-emulator.md) 를 사용 하 여 [작업의 정확한 작업 비용을 측정할](find-request-unit-charge.md)수 있습니다. Azure Cosmos Emulator는 서버 리스 서버를 지원 하지 않지만 데이터베이스 작업에 대 한 표준 작업 요금을 보고 하 고이 예측에 사용할 수 있습니다.

한 달 동안 소비할 가능성이 높은 요청 단위 및 GB 저장소의 총 수를 계산한 후에는 **([요청 단위 수]/100만 * $0.25) + ([GB의 저장소] * $0.25)** 와 같은 수식이 예상 비용을 반환 합니다.

> [!NOTE]
> 앞의 예제에 표시 된 비용은 데모용 으로만 사용 됩니다. 최신 가격 정보는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/) 를 참조 하세요.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure Portal에서 Azure Cosmos DB 리소스를 사용 하기 시작 하면 예상 비용을 볼 수 있습니다. 다음 단계를 사용 하 여 예상 비용을 검토 합니다.

1. Azure Portal에 로그인 하 여 Azure Cosmos 계정으로 이동 합니다.
1. **개요** 섹션으로 이동 합니다.
1. 아래쪽의 **비용** 차트를 확인 합니다. 이 차트는 구성 가능한 기간 동안의 현재 비용을 예상 하 여 보여 줍니다.
1. 그래프 컨테이너와 같은 새 컨테이너를 만듭니다.
1. 400 r u/초와 같은 워크 로드에 필요한 처리량을 입력 합니다. 처리량 값을 입력 한 후에는 다음 스크린샷에 표시 된 가격 책정을 확인할 수 있습니다.

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="예상 비용 Azure Portal":::

Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과 하 여 지출 하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용 됩니다. 신용 한도에 도달 하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용 하지 않도록 설정 됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대 한 자세한 내용은 [Azure 지출 한도](https://docs.microsoft.com/azure/billing/billing-spending-limit)를 참조 하세요.

Azure 기업계약 금액 약정 크레딧을 사용 하 여 Azure Cosmos DB 요금을 지불할 수 있습니다. 그러나 현금 약정 금액을 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Cosmos DB에서 리소스를 사용 하는 경우 비용이 발생 합니다. 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 요청 단위 사용에 따라 달라 집니다. Azure Cosmos DB를 시작 하는 즉시 비용이 발생 하며 Azure Portal의 [비용 분석](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 창에서이를 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 Azure Cosmos DB 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재, 이전 달 및 연도가 있습니다. 예산 및 예상 비용에 대 한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 보기로 전환 하면 지출 추세를 파악 하 고 낭비를 발생 시킬 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 해당 위치를 쉽게 확인할 수도 있습니다. 

비용 분석에서 Azure Cosmos DB 비용을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Portal에서 범위를 열고 메뉴에서 **비용 분석** 을 선택 합니다. 예를 들어 **구독** 으로 이동 하 여 목록에서 구독을 선택한 다음 메뉴에서  **비용 분석** 을 선택 합니다. 비용 분석에서 다른 범위로 전환 하려면 **범위** 를 선택 합니다.

1. 기본적으로 모든 서비스에 대 한 비용은 첫 번째 도넛형 차트에 표시 됩니다. "Azure Cosmos DB" 이라는 레이블이 지정 된 차트에서 영역을 선택 합니다.

1. Azure Cosmos DB와 같은 단일 서비스에 대 한 비용을 좁히려면 **필터 추가** 를 선택 하 고 **서비스 이름** 을 선택 합니다. 그런 다음 목록에서 **Azure Cosmos DB** 을 선택 합니다. Azure Cosmos DB에 대 한 비용을 보여 주는 예제는 다음과 같습니다.
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="비용 분석 창으로 비용 모니터링":::

위의 예제에서는 2 월의 Azure Cosmos DB에 대 한 현재 비용을 볼 수 있습니다. 차트에는 위치 및 리소스 그룹별 Azure Cosmos DB 비용도 포함 됩니다.

## <a name="create-budgets"></a>예산 만들기

[예산을](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산 생성 시의 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 가격 책정의 작동 방식에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure Cosmos DB의 가격 책정 모델](how-pricing-works.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](./optimize-cost-reads-writes.md)
* [Azure Cosmos DB의 스토리지 비용 최적화](optimize-cost-storage.md)
* [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
* [비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
* [예기치 않은 비용을 방지](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
* [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.
