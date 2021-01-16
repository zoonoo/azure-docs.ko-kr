---
title: Azure SQL Database에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure SQL Database에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 19531b9d0019fb000efe5aeb2d1b9fed34ce23f1
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253423"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Azure SQL Database에 대 한 비용 계획 및 관리

이 문서에서는 Azure SQL Database에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 Azure 가격 책정 계산기를 사용 하 여 Azure 리소스를 추가 하 고 예상 비용을 검토 합니다. Azure SQL Database 리소스 사용을 시작한 후에는 Cost Management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 파악 하 여 작업할 수 있는 영역을 식별할 수도 있습니다. Azure SQL Database 비용은 Azure 청구서의 월별 비용 중 일부에 불과합니다. 이 문서에서는 Azure SQL Database에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만 모든 타사 서비스를 포함 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다.


## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 대부분의 Azure 계정 유형을 지원 하지만 일부는 지원 하지 않습니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)를 참조하세요. 비용 데이터를 보려면 최소한 Azure 계정에 대 한 읽기 권한이 있어야 합니다. 

Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조하세요.


## <a name="sql-database-initial-cost-considerations"></a>SQL Database 초기 비용 고려 사항

Azure SQL Database 작업할 때 다음과 같은 몇 가지 비용 절감 기능을 고려해 야 합니다.


### <a name="vcore-or-dtu-purchasing-models"></a>vCore 또는 DTU 구매 모델 

Azure SQL Database은 vCore 및 DTU의 두 구매 모델을 지원 합니다. 요금을 청구 하는 방법은 구매 모델에 따라 달라 지므로 비용을 계획 하 고 고려할 때 작업에 가장 적합 한 모델을 이해 하는 것이 중요 합니다. VCore 및 DTU 구매 모델에 대 한 자세한 내용은 [vCore 및 dtu 구매 모델 중에서 선택](purchasing-models.md)을 참조 하세요.


### <a name="provisioned-or-serverless"></a>프로 비전 되었거나 서버 리스

VCore 구매 모델에서 Azure SQL Database는 프로 비전 된 처리량 및 서버를 사용 하지 않는 두 가지 종류의 계산 계층도 지원 합니다. 각 계산 계층에 대해 부과 되는 방법은 다양 하므로 비용을 계획 하 고 고려할 때 작업에 가장 적합 한 작업을 이해 하는 것이 중요 합니다. 자세한 내용은 [Vcore 모델 개요-compute 계층](service-tiers-vcore.md#compute-tiers)을 참조 하세요.

VCore 기반 구매 모델의 프로 비전 된 계산 계층에서 할인 된 요금으로 기존 라이선스를 교환할 수 있습니다. 자세한 내용은 [Azure 하이브리드 혜택 (AHB)](../azure-hybrid-benefit.md)를 참조 하세요.

### <a name="elastic-pools"></a>탄력적 풀

다양 하 고 예측할 수 없는 사용 요구가 있는 여러 데이터베이스를 사용 하는 환경에서는 탄력적 풀이 동일한 양의 단일 데이터베이스를 프로 비전 하는 것과 비교 하 여 비용을 절감할 수 있습니다. 자세한 내용은 [탄력적 풀](elastic-pool-overview.md)을 참조 하세요.

## <a name="estimate-azure-sql-database-costs"></a>예상 Azure SQL Database 비용

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 다양 한 Azure SQL Database 구성에 대 한 비용을 계산할 수 있습니다. 다음 이미지의 정보 및 가격은 예를 들어 다음 용도로만 사용 됩니다.

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL Database 가격 계산기 예":::

또한 각 보존 정책 옵션이 비용에 미치는 영향을 예측할 수 있습니다. 다음 이미지의 정보 및 가격은 예를 들어 다음 용도로만 사용 됩니다.

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="저장소에 대 한 Azure SQL Database 가격 계산기 예제":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Azure SQL Database에 대 한 전체 청구 모델 이해

Azure SQL Database는 새 리소스를 배포할 때 Azure SQL Database와 함께 비용이 발생 하는 Azure 인프라에서 실행 됩니다. 추가 인프라가 비용을 계산 하는 것을 이해 하는 것이 중요 합니다. 배포 된 리소스를 변경할 때이 비용을 관리 해야 합니다. 


Azure SQL Database (서버 리스 서버 제외)는 예측 가능한 시간당 요금으로 청구 됩니다. SQL 데이터베이스가 1 시간 미만 동안 활성 상태인 경우 사용량이 나 데이터베이스가 한 시간 미만 동안 활성화 되었는지 여부에 관계 없이 선택한 가장 높은 서비스 계층을 사용 하 여 데이터베이스가 존재 하는 시간 마다 요금이 청구 됩니다. 프로 비전 된 저장소 및 해당 시간 동안 적용 된 IO입니다.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Azure SQL Database에서 금액 크레딧 사용

EA 금액 약정 크레딧을 사용 하 여 Azure SQL Database 요금을 지불할 수 있습니다. 그러나 EA 현금 약정 크레딧을 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Azure Portal에서 예상 비용 검토

Azure SQL Database를 만드는 과정을 진행 하면서 계산 계층을 구성 하는 동안 예상 비용을 확인할 수 있습니다. 

이 화면에 액세스 하려면 **SQL Database 만들기** 페이지의 **기본 사항** 탭에서 **데이터베이스 구성** 을 선택 합니다. 다음 이미지의 정보 및 가격은 예를 들어 다음 용도로만 사용 됩니다.

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Azure Portal의 예상 비용을 보여 주는 예":::



Azure 구독에 지출 한도가 있는 경우 Azure는 크레딧 금액을 초과 하 여 지출 하지 못하도록 합니다. Azure 리소스를 만들고 사용할 때 크레딧이 사용 됩니다. 신용 한도에 도달 하면 배포한 리소스는 해당 청구 기간의 나머지 기간 동안 사용 하지 않도록 설정 됩니다. 신용 한도를 변경할 수 없지만 제거할 수는 있습니다. 지출 한도에 대 한 자세한 내용은 [Azure 지출 한도](https://docs.microsoft.com/azure/billing/billing-spending-limit)를 참조 하세요.

## <a name="monitor-costs"></a>비용 모니터링

Azure SQL Database 사용을 시작 하면 포털에서 예상 비용을 볼 수 있습니다. 다음 단계를 사용 하 여 예상 비용을 검토 합니다.

1. Azure Portal에 로그인 하 여 Azure SQL database의 리소스 그룹으로 이동 합니다. 데이터베이스를 탐색 하 고 **개요** 섹션에서 **리소스 그룹** 을 선택 하 여 리소스 그룹을 찾을 수 있습니다.
1. 메뉴에서 **비용 분석** 을 선택 합니다.
1. **누적 비용** 을 확인 하 고 아래쪽의 차트를 **서비스 이름** 으로 설정 합니다. 이 차트에서는 현재 SQL Database 비용의 예상 값을 보여 줍니다. 전체 페이지의 Azure SQL Database에 대 한 비용을 좁히려면 **필터 추가** 를 선택 하 고 **Azure SQL Database** 를 선택 합니다. 다음 이미지의 정보 및 가격은 예를 들어 다음 용도로만 사용 됩니다.

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Azure Portal 누적 비용을 보여 주는 예":::

여기에서 비용을 직접 살펴볼 수 있습니다. 다른 비용 분석 설정에 대 한 자세한 내용과 정보는 [비용 분석 시작](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="create-budgets"></a>예산 만들기

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

[예산을](../../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 만들어 비용을 관리 하 고 관련자에 게 잘못 된 지출 및 과도 한 지출 위험을 자동으로 알리는 [경고](../../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 

모니터링에 세분성이 더 필요한 경우 Azure의 특정 리소스 또는 서비스에 대 한 필터를 사용 하 여 예산을 만들 수 있습니다. 필터를 통해 추가 비용을 절감할 수 있는 새 리소스를 실수로 만들지 않도록 할 수 있습니다. 예산 생성 시의 필터 옵션에 대 한 자세한 내용은 [그룹 및 필터 옵션](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)을 참조 하세요.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Azure SQL Database에 대 한 비용을 관리 하 고 절감 하는 기타 방법

또한 Azure SQL Database를 사용 하면 응용 프로그램 요구에 따라 비용을 제어 하기 위해 리소스를 확장 하거나 축소할 수 있습니다. 자세한 내용은 [데이터베이스 리소스를 동적으로 확장](scale-resources.md)을 참조 하세요.

1 ~ 3 년간 계산 리소스에 대 한 예약을 커밋하여 비용을 절감할 수 있습니다. 자세한 내용은 [예약 된 용량으로 리소스에 대 한 비용](reserved-capacity-overview.md)절감을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](../../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.
