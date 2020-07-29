---
title: 할당량 증가 요청 및 지원 받기
description: Azure Synapse Analytics에 대한 Azure Portal에서 지원 요청을 만드는 방법 할당량 증가를 요청하거나 문제 해결 지원을 가져옵니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: de45e338b0b863dc2364af399a6991f56658b0e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212278"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>할당량 증가 요청 및 Azure Synapse Analytics에 대한 지원 받기

이 문서에서는 Azure Synapse Analytics에 대한 Azure Portal에서 지원 티켓을 제출하는 방법을 설명합니다. 이 프로세스를 통해 사용자는 할당량 증가를 요청하거나 엔지니어링 지원 팀에 대한 기술 지원 요청을 제출할 수 있습니다.

## <a name="create-a-support-ticket"></a>지원 티켓 만들기

다음 단계를 사용하여 Azure Synapse Analytics에 대한 Azure Portal에서 새 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택합니다.

   ![도움말 + 지원 링크](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. **도움말 + 지원**에서 **새 지원 요청**을 선택합니다.

    ![새 지원 요청 만들기](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. [Azure 지원 플랜](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 검토합니다.

   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **고장 수리** 지원은 [개발자](https://azure.microsoft.com/support/plans/developer/), [표준](https://azure.microsoft.com/support/plans/standard/), [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 또는 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원을 통해 제공됩니다. 고장 수리 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **개발자 멘토링** 및 **자문 서비스**는 [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 및 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원 수준에서 제공됩니다.

   프리미어 지원 계획이 있는 경우 [Microsoft 프리미어 온라인 포털](https://premier.microsoft.com/)에서 Azure Synapse Analytics 문제를 보고할 수도 있습니다. 범위, 응답 시간, 가격 책정 등을 포함한 다양한 지원 계획에 대한 자세한 정보는 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 참조하세요.  Azure 지원에 대한 질문과 대답은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

1. **문제 유형**에서 적절한 문제 유형을 선택합니다. 고장 수리 문제의 경우 **기술**을 선택합니다. 할당량 증가 요청의 경우 **서비스 및 구독 제한(할당량)** 을 선택합니다.

   ![문제 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 이 문서의 나머지 부분에서는 할당량 증가 요청을 집중적으로 살펴봅니다. 하지만 여기에서 문제 해결 지원 요청에 대해 **기술**을 선택할 수도 있습니다. **기술**을 선택하는 경우 요약을 입력한 다음, **문제 유형 선택**을 선택하여 문제 유형을 식별하라는 메시지가 표시됩니다. 문제를 해결하는 데 도움이 되는 솔루션을 볼 수 있습니다. 제공된 해결 방법으로 문제가 해결되지 않으면 **다음: 세부 정보**를 선택하고 양식을 완료하여 지원 티켓을 제출합니다.

1. 할당량 증가 요청의 경우 **할당량 유형**에 대해 **Azure Synapse Analytics**를 선택합니다. 그런 다음, **Next: 솔루션 >>** 을 선택합니다.

   ![할당량 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. **세부** 정보 창에서 세부 정보 **입력** 을 선택 하 여 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>할당량 요청 유형

**세부 정보 입력** 을 선택 하면 추가 정보를 추가할 수 있는 **할당량 정보** 창이 표시 됩니다. 다음 섹션에서는 Azure Synapse Analytics에 사용할 수 있는 다양한 할당량 요청에 대해 설명합니다.

### <a name="synapse-sql-pool-data-warehouse-units-dwus-per-server"></a>서버당 Synapse SQL 풀 데이터 웨어하우스 단위 (DWUs)

서버당 DWU의 증가를 요청하려면 다음 단계를 사용합니다.

1. **SYNAPSE SQL 풀 DWUs/서버** 할당량 유형을 선택 합니다.

1. 드롭다운 목록을 사용 하 여 할당량 증가를 적용 하려는 **리소스** 를 선택 합니다.

1. 새 할당량을 **요청 할당량** 섹션에 입력 합니다.

1. **저장 및 계속**을 선택합니다.

   ![DWU 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)


### <a name="servers-per-subscription"></a>구독당 서버

구독 당 서버 수의 증가를 요청 하려면 다음 단계를 완료 해야 합니다.

1. 할당량 유형으로 **구독 당 SQL server** 를 선택 합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당입니다.

1. **요청 할당량** 필드에 해당 지역의 최대 서버 수에 대 한 요청을 입력 합니다.

   ![서버 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)



1. **저장 및 계속**을 선택합니다.

일부 제품 유형은 모든 지역에서 사용할 수 없습니다. 다음 오류가 표시될 수 있습니다.

![지역 액세스 오류](./media/sql-data-warehouse-get-started-create-support-ticket/region-access-error.png)

### <a name="enable-subscription-access-to-a-region"></a>지역에 대한 구독 액세스 사용

구독에 대 한 지역 액세스를 사용 하도록 설정 하려면 다음 단계를 완료 해야 합니다.  

1. **SYNAPSE SQL 풀 (데이터 웨어하우스) 지역 액세스** 할당량 유형을 선택 합니다.

1. 드롭다운 목록에서 **위치** 를 선택 하 여 지역을 선택 합니다.

1. **Dwu required** 섹션에서 dwu 성능 요구 사항을 표시 합니다.

1. **비즈니스 요구 사항에 대 한 설명을**입력 합니다. 

1. **저장 및 계속**을 선택합니다.

![지역 액세스](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-region.png)


### <a name="for-other-quota-requests"></a>기타 할당량 요청

다른 할당량 요청 유형에 대 한 할당량 유형 드롭다운 메뉴에서 **기타 할당량 요청** 을 선택 합니다.

![기타 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 지원 요청에 대한 나머지 세부 정보를 입력하는 것입니다. 그런 후 다음을 선택 합니다. **검토 +>>만들기 **를 선택 합니다.

![만들기 세부 정보 검토](./media/sql-data-warehouse-get-started-create-support-ticket/review-create-details.png)

요청 정보를 검토 한 후 **만들기** 를 선택 하 여 요청을 제출 합니다.

![티켓 만들기](./media/sql-data-warehouse-get-started-create-support-ticket/create-ticket.png)

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링

지원 요청을 제출하면 Azure 지원팀이 연락합니다. 요청 상태 및 세부 정보를 확인 하려면 대시보드에서 **모든 지원 요청** 을 선택 합니다.

![상태 확인](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>기타 리소스

[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) 에서 또는 [azure Synapse analytics에 대 한 질문 페이지를&](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)통해 azure Synapse analytics 커뮤니티에 연결할 수도 있습니다.

