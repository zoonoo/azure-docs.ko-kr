---
title: Azure Storage에 대 한 비용 계획 및 관리
description: Azure Portal에서 비용 분석을 사용 하 여 Azure Storage에 대 한 비용을 계획 하 고 관리 하는 방법을 알아봅니다.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "78304525"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Azure Storage에 대 한 비용 계획 및 관리

이 문서에서는 Azure Storage에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 합니다. 먼저 리소스를 추가 하기 전에 Azure 가격 계산기를 사용 하 여 저장소 비용을 계획할 수 있습니다. Azure Storage 리소스 사용을 시작한 후에는 cost management 기능을 사용 하 여 예산을 설정 하 고 비용을 모니터링 합니다. 예상 비용을 검토 하 고 지출 추세를 모니터링 하 여 작업할 수 있는 영역을 식별할 수도 있습니다.

Azure Storage에 대 한 비용은 Azure 청구서의 월별 비용 중 일부일 뿐입니다. 이 문서에서는 Azure Storage에 대 한 비용을 계획 하 고 관리 하는 방법을 설명 하지만, 타사 서비스를 비롯 하 여 Azure 구독에 사용 되는 모든 Azure 서비스 및 리소스에 대해 요금이 청구 됩니다. Azure Storage에 대 한 비용 관리에 익숙해 졌으 면 구독에 사용 되는 모든 Azure 서비스에 대 한 비용을 관리 하는 비슷한 방법을 적용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

비용 분석은 다양한 종류의 Azure 계정 유형을 지원합니다. 지원되는 계정 유형의 전체 목록을 보려면 [Cost Management 데이터 이해](../../cost-management-billing/costs/understand-cost-mgt-data.md)를 참조하세요. 비용 데이터를 보려면 적어도 Azure 계정에 대한 읽기 권한이 필요합니다. Azure Cost Management 데이터에 액세스하는 방법에 대한 정보는 [데이터에 대한 액세스 할당](../../cost-management-billing/costs/assign-access-acm-data.md)을 참조하세요.

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Azure Storage 계정을 만들기 전에 비용 추정

[Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 를 사용 하 여 Azure Storage 계정으로 데이터 전송을 만들고 시작 하기 전에 비용을 예측 합니다.

1. [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/) 페이지에서 **Storage 계정** 타일을 선택 합니다.

2. 페이지 아래로 스크롤하고 예상 되는 **저장소 계정** 섹션을 찾습니다.

3. 드롭다운 목록에서 옵션을 선택 합니다. 

   이러한 드롭다운 목록의 값을 수정 하는 경우 예상 되는 비용을 변경 합니다. 예상 값은 위쪽 모퉁이와 예상의 아래쪽에 표시 됩니다. 
    
   ![비용 분석 창으로 비용 모니터링](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **유형** 드롭다운 목록의 값을 변경 하면이 워크시트에도 표시 되는 다른 옵션도 변경 됩니다. **추가 정보** 섹션의 링크를 사용 하 여 각 옵션의 의미와 이러한 옵션이 저장소 관련 작업의 가격에 미치는 영향에 대해 자세히 알아보세요. 

4. 나머지 옵션을 수정 하 여 예상 값에 대 한 영향을 확인 합니다.

## <a name="use-budgets-and-cost-alerts"></a>예산 및 비용 경고 사용

[예산](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)을 만들면 비용을 관리하고 관련자에게 비정상 지출 및 과다 지출 위험을 자동으로 알리는 경고를 만들 수 있습니다. 경고는 예산 및 비용 임계값에 따른 지출을 기준으로 합니다. Azure 구독 및 리소스 그룹에 대 한 예산과 경고가 생성 되므로 전체 비용 모니터링 전략의 일부로 유용 합니다. 그러나 더 높은 수준에서 비용을 추적 하도록 디자인 되었기 때문에 Azure Storage 비용과 같은 개별 Azure 서비스 비용을 관리 하는 기능이 제한 될 수 있습니다.

## <a name="monitor-costs"></a>비용 모니터링

Azure Storage에서 Azure 리소스를 사용 하는 경우 비용이 발생 합니다. 리소스 사용 단위 비용은 시간 간격 (초, 분, 시간 및 일) 또는 단위 사용 (바이트, 메가바이트 등)에 따라 달라 집니다. Azure Storage 사용이 시작 되는 즉시 비용이 발생 합니다. Azure Portal의 [비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md) 창에서 비용을 볼 수 있습니다.

비용 분석을 사용 하면 서로 다른 시간 간격에 대 한 그래프 및 테이블의 Azure Storage 비용을 볼 수 있습니다. 몇 가지 예로는 일, 현재 및 이전 달 및 연도가 있습니다. 예산 및 예상 비용에 대 한 비용을 볼 수도 있습니다. 시간이 지남에 따라 더 긴 보기로 전환 하면 지출 추세를 파악 하 고 낭비를 발생 시킬 수 있는 위치를 확인할 수 있습니다. 예산을 만든 경우 해당 위치를 쉽게 확인할 수도 있습니다.

비용 분석에서 Azure Storage 비용을 보려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Cost Management + 청구** 창을 열고 메뉴에서 **비용 관리** 를 선택한 다음 **비용 분석**을 선택 합니다. 그런 다음 **범위** 드롭다운에서 특정 구독에 대 한 범위를 변경할 수 있습니다.

   ![비용 분석 창으로 비용 모니터링](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage에 대 한 비용만 보려면 **필터 추가** 를 선택한 다음 **서비스 이름**을 선택 합니다. 그런 다음 목록에서 **저장소** 를 선택 합니다. 

   Azure Storage에 대 한 비용을 보여 주는 예제는 다음과 같습니다.

   ![비용 분석 창에서 저장소 비용 모니터링](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

앞의 예제에서 서비스에 대 한 현재 비용이 표시 됩니다. Azure 지역 (위치) 및 리소스 그룹별 비용도 표시 됩니다.  

## <a name="next-steps"></a>다음 단계

[비용 분석](../../cost-management-billing/costs/quick-acm-cost-analysis.md)으로 비용 관리에 대해 자세히 알아보세요.

Azure Storage에서 가격 책정을 사용 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Storage 개요 가격 책정](https://azure.microsoft.com/pricing/details/storage/)
- [예약된 용량으로 Blob 스토리지에 대한 비용 최적화](../blobs/storage-blob-reserved-capacity.md)
