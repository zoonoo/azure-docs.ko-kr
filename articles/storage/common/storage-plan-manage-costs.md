---
title: Azure Blob 저장소에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Blob 저장소에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: a30fe70943c5329b36cbe10b75d47cc0ee3827fc
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630416"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Azure Blob 저장소에 대 한 비용 계획 및 관리

이 문서는 Azure Blob 저장소에 대 한 비용을 계획 하 고 관리 하는 데 도움이 됩니다. 첫째, Azure 가격 계산기를 사용 하 여 비용을 예측 합니다. 저장소 계정을 만든 후에는 필요한 만큼만 지불 하도록 계정을 최적화 합니다. 비용 관리 기능을 사용 하 여 예산 및 모니터링 비용을 설정 합니다. 예상 비용을 검토 하 고 지출 추세를 모니터링 하 여 작업할 수 있는 영역을 식별할 수도 있습니다.

Blob 저장소에 대 한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 이 문서에서는 Blob 저장소에 대 한 비용을 예측 하 고 관리 하는 방법을 설명 하지만, 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다. Blob 저장소에 대 한 비용 관리에 익숙한 후에는 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용할 수 있습니다.

## <a name="estimate-costs"></a>비용 예측

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 Azure Storage 계정으로 데이터 전송을 만들고 시작 하기 전에 비용을 예측 합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 페이지에서 **Storage 계정** 타일을 선택 합니다.

2. 페이지 아래로 스크롤하고 예상 되는 **저장소 계정** 섹션을 찾습니다.

3. 드롭다운 목록에서 옵션을 선택 합니다. 

   이러한 드롭다운 목록의 값을 수정 하는 경우 예상 되는 비용을 변경 합니다. 예상 값은 위쪽 모퉁이와 예상의 아래쪽에 표시 됩니다.

   ![예상 값을 보여 주는 스크린샷](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **유형** 드롭다운 목록의 값을 변경 하면이 워크시트에도 표시 되는 다른 옵션도 변경 됩니다. **추가 정보** 섹션의 링크를 사용 하 여 각 옵션의 의미와 이러한 옵션이 저장소 관련 작업의 가격에 미치는 영향에 대해 자세히 알아보세요. 

4. 나머지 옵션을 수정 하 여 예상 값에 대 한 영향을 확인 합니다.

   > [!NOTE]
   > Azure 기업계약 금액 약정 크레딧을 사용 하 여 Azure Blob 저장소 요금에 대 한 비용을 지불할 수 있습니다. 그러나 현금 약정 금액을 사용 하 여 Azure Marketplace에서 사용 하는 타사 제품 및 서비스에 대 한 요금을 지불할 수 없습니다.

## <a name="optimize-costs"></a>비용 최적화

이러한 옵션을 사용 하 여 비용을 줄이는 것이 좋습니다. 

- 저장소 용량 예약

- 액세스 계층으로 데이터 구성

- 액세스 계층 간에 데이터를 자동으로 이동

이 섹션에서는 각 옵션에 대해 자세히 설명 합니다. 

#### <a name="reserve-storage-capacity"></a>저장소 용량 예약

Azure Storage reserved capacity를 사용 하 여 blob 데이터에 대 한 저장소 비용을 절감할 수 있습니다. Azure Storage reserved capacity는 1 년 또는 3 년간의 예약을 커밋할 때 블록 blob에 대 한 용량과 standard Storage 계정에 있는 Azure Data Lake Storage Gen2 데이터에 대 한 할인을 제공 합니다. 예약은 예약 기간에 고정 된 크기의 저장소 용량을 제공 합니다. 예약 된 용량 Azure Storage 블록 blob 및 Azure Data Lake Storage Gen2 데이터에 대 한 용량 비용을 크게 줄일 수 있습니다. 

자세히 알아보려면 [예약 된 용량으로 Blob storage에 대 한 비용 최적화](../blobs/storage-blob-reserved-capacity.md)를 참조 하세요.

#### <a name="organize-data-into-access-tiers"></a>액세스 계층으로 데이터 구성

Blob 데이터를 가장 비용 효율적인 액세스 계층에 배치 하 여 비용을 줄일 수 있습니다. 데이터 사용과 관련 하 여 비용을 최적화 하도록 설계 된 세 가지 계층에서 선택 합니다. 예를 들어, *핫* 계층의 저장소 비용은 더 높고 액세스 비용은 낮아집니다. 따라서 데이터에 자주 액세스할 계획인 경우 핫 계층이 가장 비용 효율적인 선택이 될 수 있습니다. 데이터에 자주 액세스 하는 경우 *콜드* 또는 *보관* 계층은 데이터에 액세스 하는 비용을 발생 시키고 데이터를 저장 하는 데 드는 비용을 줄이는 것이 가장 적합 합니다.    

자세히 알아보려면 [Azure Blob storage: 핫, 쿨 및 보관 액세스 계층](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)을 참조 하세요.

#### <a name="automatically-move-data-between-access-tiers"></a>액세스 계층 간에 데이터를 자동으로 이동

수명 주기 관리 정책을 사용 하 여 정기적으로 계층 간에 데이터를 이동 하 여 비용을 절감할 수 있습니다. 이러한 정책은 사용자가 지정 하는 규칙을 사용 하 여 데이터를로 이동할 수 있습니다. 예를 들어 blob이 90 일 이내에 수정 되지 않은 경우 blob을 보관 계층으로 이동 하는 규칙을 만들 수 있습니다. 데이터의 액세스 계층을 조정 하는 정책을 만들어 요구에 가장 저렴 한 저장소 옵션을 디자인할 수 있습니다.

자세히 알아보려면 [Azure Blob 저장소 수명 주기 관리](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) 를 참조 하세요.

## <a name="create-budgets"></a>예산 만들기

[예산](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 그러나 더 높은 수준에서 비용을 추적 하도록 디자인 되었기 때문에 Azure Storage 비용과 같은 개별 Azure 서비스 비용을 관리 하는 기능이 제한 될 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Storage에서 Azure 리소스를 사용 하는 경우 비용이 발생 합니다. 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 단위 사용 (바이트, 메가바이트 등)에 따라 달라 집니다. Azure Storage 사용이 시작 되는 즉시 비용이 발생 합니다. Azure Portal의 [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md) 창에서 비용을 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 Azure Storage 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 예산 및 예상 비용에 대 한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 보기로 전환 하면 지출 추세를 파악 하 고 낭비를 발생 시킬 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 해당 위치를 쉽게 확인할 수도 있습니다.

>[!NOTE]
> 비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

비용 분석에서 Azure Storage 비용을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음 **비용 분석** 을 선택 합니다. 그런 다음 **범위** 드롭다운에서 특정 구독에 대 한 범위를 변경할 수 있습니다.

   ![범위를 보여 주는 스크린샷](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage에 대 한 비용만 보려면 **필터 추가** 를 선택한 다음 **서비스 이름** 을 선택 합니다. 그런 다음 목록에서 **저장소** 를 선택 합니다. 

   Azure Storage에 대 한 비용을 보여 주는 예제는 다음과 같습니다.

   ![저장소로 필터링을 보여 주는 스크린샷](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

앞의 예제에서 서비스에 대 한 현재 비용이 표시 됩니다. Azure 지역 (위치) 및 리소스 그룹별 비용도 표시 됩니다. 다른 필터 (예: 특정 저장소 계정에 대 한 비용을 확인 하는 필터)도 추가할 수 있습니다.

## <a name="export-cost-data"></a>비용 데이터 내보내기

[비용 데이터](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 를 저장소 계정으로 내보낼 수도 있습니다. 이는 또는 다른 사용자가 비용에 대 한 추가 데이터 분석을 수행 해야 하는 경우에 유용 합니다. 예를 들어 재무 팀은 Excel 또는 Power BI를 사용 하 여 데이터를 분석할 수 있습니다. 매일, 매주 또는 매월 일정에 대 한 비용을 내보내고 사용자 지정 날짜 범위를 설정할 수 있습니다. 비용 데이터를 내보내는 것은 비용 데이터 집합을 검색 하는 데 권장 되는 방법입니다.

## <a name="next-steps"></a>다음 단계

- Azure Storage에서 가격 책정을 사용 하는 방법에 대해 자세히 알아보세요. [Azure Storage 개요 가격 책정](https://azure.microsoft.com/pricing/details/storage/)을 참조 하세요.
- [예약 된 용량의 Blob 저장소에 대 한 비용을 최적화](../blobs/storage-blob-reserved-capacity.md)합니다.
- [Azure Cost Management를 사용 하 여 클라우드 투자를 최적화 하는 방법을](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)알아봅니다.
- [비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)으로 비용 관리에 대해 자세히 알아보세요.
- [예기치 않은 비용을 방지](https://docs.microsoft.com/azure/cost-management-billing/manage/getting-started?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)하는 방법에 대해 알아봅니다.
- [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 단계별 학습 과정을 수행 하세요.