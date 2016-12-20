---
title: "Azure Data Lake Analytics 할당량 제한 | Microsoft Docs"
description: "ADLA(Azure Data Lake Analytics) 계정에서 할당량 한도를 조정하고 제한하는 방법을 알아봅니다. 이러한 한도를 알면 U-SQL 작업 동작을 이해하는 데 도움이 될 수 있습니다. 이 모든 한도는 소프트 한도이며, 언제든지 Microsoft에 문의하여 최대 한도를 늘릴 수 있습니다."
services: data-lake-analytics
keywords: "Azure 데이터 레이크 분석"
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/16/2016
ms.author: omidm
translationtype: Human Translation
ms.sourcegitcommit: ce76fb1feaa38ff20ccc873114541a015126a1ad
ms.openlocfilehash: cffafbc20294c235060b03a75f6d1bee712115d1


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics 할당량 한도
ADLA(Azure Data Lake Analytics) 계정에서 할당량 한도를 조정하고 제한하는 방법을 알아봅니다. 이러한 한도를 알면 U-SQL 작업 동작을 이해하는 데 도움이 될 수 있습니다. 이 모든 한도는 소프트 한도이며, 언제든지 Microsoft에 문의하여 최대 한도를 늘릴 수 있습니다.

**필수 구성 요소**

이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **데이터 레이크 분석 계정**. [Azure Portal을 사용하여 Azure Data Lake Analytics 시작](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/#create-adl-analytics-account)을 참조하세요.
* **데이터 레이크 분석 작업 프로세스에 대한 기본 지식**. [Azure Portal을 사용하여 Azure Data Lake Analytics 시작](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/)을 참조하세요.

<!-- ################################ -->
<!-- ################################ -->
## <a name="quota-limits"></a>할당량 한도:
아래 목록에는 시스템의 현재 할당량 한도가 나와 있습니다.

**Azure 구독 한도:** 다음 한도는 Azure 구독에 적용됩니다.
* **구독당 최대 ADLA 계정 수:** 5. 이는 구독당 만들 수 있는 ADLA 계정의 최대 개수입니다. 6번째 ADLA 계정을 만들려고 하면 "구독 이름에 따라 지역에서 허용되는 Data Lake Analytics 계정의 최대 수(5)에 도달했습니다." 오류가 발생합니다. 구독에서 사용되는 ADLA 계정을 삭제하거나 지원 티켓을 열고 Microsoft에 문의하여 이 오류를 쉽게 해결할 수 있습니다.

**ADLA 계정 한도:**
* **계정당 최대 AU(분석 단위) 수:** 250. 이는 계정에서 동시에 실행할 수 있는 AU의 최대 개수입니다. 모든 작업에서 실행 중인 총 AU 수는 이를 초과할 수 없습니다. 이 값을 초과하면 최신 작업이 자동으로 큐에 대기합니다. 예:
    * 250AU로 실행되는 작업이 하나뿐인 경우 두 번째 작업을 제출하면 첫 번째 작업이 완료될 때까지 이 작업이 작업 큐에서 대기합니다.
    * 각각 50AU로 제출된 5개의 작업을 이미 실행 중인 경우 20AU의 6번째 작업을 제출하면 작업 큐에 대기했다가 20AU를 사용할 수 있을 때 실행이 시작됩니다.
* **계정당 동시 U-SQL 작업의 최대 수:** 20. 이는 계정에서 동시에 실행할 수 있는 작업의 최대 개수입니다. 이 값을 초과하면 최신 작업이 자동으로 큐에 대기합니다.

**계정당 ADLA 할당량 한도를 조정하려면**
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 이미 만든 ADLA 계정을 선택합니다.
3. **속성**을 클릭합니다.
4. **병렬 처리** 및 **동시 작업**을 요구 사항에 맞게 조정합니다.

    ![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

**최대 할당량 한도를 늘리려면**
1. Azure Portal에서 지원 요청을 엽니다.

    ![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. 문제 유형을 **할당량**으로 선택합니다.
3. **구독**을 선택합니다("평가판" 구독이 아닌지 확인).
4. 할당량 유형을 **Data Lake Analytics**로 선택합니다.

    ![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5.  문제의 블레이드에서 요청한 증가 한도 및 이 추가 용량이 필요한 이유에 대한 **세부 정보**를 설명하세요.

    ![Azure 데이터 레이크 분석 포털 블레이드](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6.  연락처 정보를 확인하고 지원 요청을 만듭니다.

Microsoft에서는 요청을 검토한 후 가능한 한 신속하게 비즈니스 요구를 수용하기 위해 최선을 다합니다.

## <a name="see-also"></a>참고 항목
* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure Portal을 사용하여 Data Lake Analytics 시작](data-lake-analytics-get-started-portal.md)
* [Azure PowerShell을 사용하여 Azure 데이터 레이크 분석 관리](data-lake-analytics-manage-use-powershell.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Nov16_HO3-->


