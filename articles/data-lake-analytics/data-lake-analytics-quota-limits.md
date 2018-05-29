---
title: Azure Data Lake Analytics 할당량 한도
description: ADLA(Azure Data Lake Analytics) 계정에서 할당량 한도를 조정하고 늘리는 방법을 알아봅니다.
services: data-lake-analytics
keywords: Azure 데이터 레이크 분석
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: c6c39fb0810a7ea8b6facec1ca80da25d2253329
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311134"
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 할당량 한도

ADLA(Azure Data Lake Analytics) 계정에서 할당량 한도를 조정하고 늘리는 방법을 알아봅니다. 이러한 한도를 알면 U-SQL 작업 동작을 이해하는 데 도움이 될 수 있습니다. 모든 할당량 한도는 소프트 한도이며, Azure Support에 문의하면 최대 한도를 늘릴 수 있습니다.

## <a name="azure-subscriptions-limits"></a>Azure 구독 한도

**지역당 구독당 최대 ADLA 계정 수:** 5

6번째 ADLA 계정을 만들려고 하면 "구독 이름에 따라 지역에서 허용되는 Data Lake Analytics 계정의 최대 수(5)에 도달했습니다." 오류가 발생합니다. 

이 한도를 초과하려면 이러한 옵션을 사용해 볼 수 있습니다.
* 적합한 경우 다른 영역 선택
* 할당량 증대를 요청하는 [지원 티켓을 열어](#increase-maximum-quota-limits) Azure 고객 지원팀에 문의

## <a name="adla-account-limits"></a>ADLA 계정 한도

**계정당 최대 AU(분석 단위) 수:** 250.

이는 계정에서 동시에 실행할 수 있는 AU의 최대 개수입니다. 모든 작업에서 실행 중인 총 AU 수가 이 한도를 초과하면 최신 작업이 자동으로 큐에 대기됩니다. 예: 

* 250AU로 실행되는 작업이 하나뿐인 경우 두 번째 작업을 제출하면 첫 번째 작업이 완료될 때까지 이 작업이 작업 큐에서 대기합니다.
* 이미 5개의 작업이 실행 중이고 각각 50AU를 사용하는 경우 20AU가 필요한 6번째 작업을 제출하면 20AU가 사용 가능 상태가 될 때까지 작업 큐에서 대기합니다.

**계정당 동시 U-SQL 작업의 최대 수:** 20.

이는 계정에서 동시에 실행할 수 있는 작업의 최대 개수입니다. 이 값을 초과하면 최신 작업이 자동으로 큐에 대기합니다.

## <a name="adjust-adla-quota-limits-per-account"></a>계정당 ADLA 할당량 한도 조정

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 기존 ADLA 계정을 선택합니다.
3. **속성**을 클릭합니다.
4. **병렬 처리** 및 **동시 작업**을 요구 사항에 맞게 조정합니다.

    ![Azure Data Lake Analytics 포털 페이지](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>최대 할당량 한도 늘리기

1. Azure Portal에서 지원 요청을 엽니다.

    ![Azure Data Lake Analytics 포털 페이지](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics 포털 페이지](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 문제 유형을 **할당량**으로 선택합니다.
3. **구독**을 선택합니다("평가판" 구독이 아닌지 확인).
4. 할당량 유형을 **Data Lake Analytics**로 선택합니다.

    ![Azure Data Lake Analytics 포털 페이지](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. 문제 페이지에 요청한 증가 한도 및 이 추가 용량이 필요한 이유에 대한 **세부 정보**를 설명하세요.

    ![Azure Data Lake Analytics 포털 페이지](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 연락처 정보를 확인하고 지원 요청을 만듭니다.

Microsoft에서 요청을 검토하고 최대한 빨리 비즈니스 요구를 수용하려고 합니다.

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure PowerShell을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-powershell.md)
* [Azure Portal을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
