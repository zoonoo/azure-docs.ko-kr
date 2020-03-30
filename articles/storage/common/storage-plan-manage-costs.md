---
title: Azure 저장소에 대한 비용 계획 및 관리
description: Azure 포털에서 비용 분석을 사용하여 Azure 저장소에 대한 비용을 계획하고 관리하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304525"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Azure 저장소에 대한 비용 계획 및 관리

이 문서에서는 Azure 저장소에 대한 비용을 계획하고 관리하는 방법에 대해 설명합니다. 먼저 Azure 가격 계산기를 사용하여 리소스를 추가하기 전에 저장소 비용을 계획할 수 있습니다. Azure Storage 리소스사용을 시작한 후 비용 관리 기능을 사용하여 예산을 설정하고 비용을 모니터링합니다. 또한 예상 비용을 검토하고 지출 추세를 모니터링하여 조치를 원하는 영역을 식별할 수 있습니다.

Azure 저장소에 대한 비용은 Azure 청구서의 월별 비용의 일부일 뿐입니다. 이 문서에서는 Azure Storage에 대한 비용을 계획하고 관리하는 방법을 설명하지만 타사 서비스를 포함하여 Azure 구독에 사용되는 모든 Azure 서비스 및 리소스에 대한 요금이 청구됩니다. Azure Storage의 비용 관리에 익숙한 후에는 구독에 사용되는 모든 Azure 서비스에 대한 비용을 관리하기 위해 유사한 방법을 적용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Azure Storage 계정을 만들기 전에 비용 예측

Azure [가격 계산기를](https://azure.microsoft.com/pricing/calculator/) 사용하여 Azure Storage 계정으로 데이터를 만들고 전송하기 전에 비용을 예측합니다.

1. Azure [가격 계산기](https://azure.microsoft.com/pricing/calculator/) 페이지에서 **저장소 계정** 타일을 선택합니다.

2. 페이지를 아래로 스크롤하여 예상 의 **저장소 계정** 섹션을 찾습니다.

3. 드롭다운 목록에서 옵션을 선택합니다. 

   이러한 드롭다운 목록의 값을 수정하면 비용 예상치가 변경됩니다. 이 추정치는 추정의 맨 아래뿐만 아니라 위쪽 모서리에 나타납니다. 
    
   ![비용 분석 창으로 비용 모니터링](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **[입력]** 드롭다운 목록의 값을 변경하면 이 워크시트에 나타나는 다른 옵션도 변경됩니다. **추가 정보** 섹션의 링크를 사용하여 각 옵션의 의미와 이러한 옵션이 저장소 관련 작업의 가격에 미치는 영향에 대해 자세히 알아봅니다. 

4. 나머지 옵션을 수정하여 예상 옵션의 영향을 확인합니다.

## <a name="use-budgets-and-cost-alerts"></a>예산 및 비용 경고 사용

[예산](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대해 예산 및 경고가 만들어지므로 전체 비용 모니터링 전략의 일부로 유용합니다. 그러나 Azure Storage 의 비용과 같은 개별 Azure 서비스 비용을 관리하는 기능이 제한되어 있을 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure 저장소와 함께 Azure 리소스를 사용하면 비용이 발생합니다. 리소스 사용 단위 비용은 시간 간격(초, 분, 시간 및 일) 또는 단위 사용량(바이트, 메가바이트 등)에 따라 다릅니다. Azure 저장소 사용이 시작되는 즉시 비용이 발생합니다. Azure 포털의 비용 [분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md) 창에서 비용을 확인할 수 있습니다.

비용 분석을 사용하는 경우 서로 다른 시간 간격에 대한 그래프 및 테이블에서 Azure Storage 비용을 볼 수 있습니다. 일별, 현재 및 이전 월 및 연도가 있습니다. 예산 및 예상 비용에 대한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 조회수로 전환하면 지출 추세를 파악하고 초과 지출이 발생한 위치를 확인할 수 있습니다. 예산을 만든 경우 예산을 초과한 위치도 쉽게 확인할 수 있습니다.

비용 분석에서 Azure 저장소 비용을 보려면 다음을 수행하십시오.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. 비용 **관리 + 청구** 창을 열고 메뉴에서 **비용 관리를** 선택한 다음 **비용 분석을**선택합니다. 그런 다음 **범위** 드롭다운에서 특정 구독의 범위를 변경할 수 있습니다.

   ![비용 분석 창으로 비용 모니터링](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure 저장소에 대한 비용만 보려면 **필터 추가를** 선택한 다음 **서비스 이름을**선택합니다. 그런 다음 목록에서 **저장소를** 선택합니다. 

   다음은 Azure 저장소에 대한 비용을 보여주는 예제입니다.

   ![비용 분석 창으로 스토리지 비용 모니터링](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

앞의 예제에서는 서비스에 대한 현재 비용이 표시됩니다. Azure 지역(위치) 및 리소스 그룹별 비용도 표시됩니다.  

## <a name="next-steps"></a>다음 단계

[비용 분석을](../../cost-management-billing/costs/quick-acm-cost-analysis.md)통해 비용 관리에 대해 자세히 알아보십시오.

Azure Storage의 가격 책정 작동 방식에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure 저장소 개요 가격](https://azure.microsoft.com/pricing/details/storage/)
- [예약된 용량으로 Blob 스토리지에 대한 비용 최적화](../blobs/storage-blob-reserved-capacity.md)
