---
title: 할당량 증가 요청 및 지원 받기
description: Azure 시냅스 분석을 위한 Azure 포털에서 지원 요청을 만드는 방법 할당량 증가를 요청하거나 문제 해결 지원을 받습니다.
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: eaa6ceded9893b278b28d517cbddb303f8469e10
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350889"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>할당량 증가를 요청하고 Azure 시냅스 분석에 대한 지원을 받으십시오.

이 문서에서는 Azure Synapse 분석을 위한 Azure 포털에서 지원 티켓을 제출하는 방법에 대해 설명합니다. 이 프로세스를 사용하면 할당량 증가를 요청하거나 엔지니어링 지원 팀에 대한 기술 지원 요청을 제출할 수 있습니다.

## <a name="create-a-support-ticket"></a>지원 티켓 만들기

다음 단계를 사용하여 Azure 시냅스 분석을 위한 Azure 포털에서 새 지원 요청을 만듭니다.

1. Azure [포털](https://portal.azure.com) 메뉴에서 **도움말 + 지원을**선택합니다.

   ![도움말 + 지원 링크](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. **도움말 + 지원에서** **새 지원 요청을**선택합니다.

    ![새 지원 요청 만들기](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. Azure [지원 계획을](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)검토합니다.

   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **브레이크 픽스** 지원은 [개발자,](https://azure.microsoft.com/support/plans/developer/) [표준,](https://azure.microsoft.com/support/plans/standard/) [전문 직접,](https://azure.microsoft.com/support/plans/prodirect/)또는 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원을 통해 제공됩니다. 고장 수리 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **개발자 멘토링** 및 **자문 서비스**는 [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 및 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원 수준에서 제공됩니다.

   프리미어 지원 계획이 있는 경우 [Microsoft Premier 온라인 포털에서](https://premier.microsoft.com/)Azure Synapse 분석 문제를 보고할 수도 있습니다. [범위,](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) 응답 시간, 가격 책정 등을 비롯한 다양한 지원 계획에 대해 자세히 알아보려면 Azure 지원 계획을 참조하십시오.  Azure 지원에 대해 자주 묻는 질문은 [Azure 지원 FAQ를](https://azure.microsoft.com/support/faq/)참조하십시오.

1. **문제 유형의**경우 적절한 문제 유형을 선택합니다. 고장 수리 문제의 경우 **기술**. 할당량 증가 요청의 경우 **서비스 및 구독 한도(할당량)를 선택합니다.**

   ![문제 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 이 문서의 나머지 부분에서는 할당량 증가 요청에 중점을 둡니다. 그러나 문제 해결 지원 요청에 대한 **기술(기술)을** 선택할 수도 있습니다. **기술**- 요약을 제공한 다음 문제 유형 선택을 선택하여 문제 유형을 **식별하라는**메시지가 표시됩니다. 문제를 해결하는 데 도움이 되는 해결 방법을 확인할 수 있습니다. 제시된 해결 방안이 해결되지 않으면 **다음:세부 정보를** 선택하고 양식을 작성하여 지원 티켓을 제출합니다.

1. 할당량 증가 요청의 경우 **할당량 유형에**대한 **Azure Synapse 분석을** 선택합니다. 그런 다음 ** >>을 선택합니다. **

   ![할당량 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. 세부 **정보** 창에서 **세부 정보 제공을** 선택하여 추가 정보를 입력합니다.

   !["세부 정보 제공" 링크](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>할당량 요청 유형

**세부정보 제공을** 클릭하면 추가 정보를 추가할 수 있는 **할당량 세부 정보** 창이 표시됩니다. 다음 섹션에서는 Azure Synapse Analytics에서 사용할 수 있는 다양한 할당량 요청을 설명합니다.

### <a name="data-warehouse-units-dwus-per-server"></a>서버당 데이터 웨어하우스 단위(DW).

다음 단계를 사용하여 서버당 DWUs 증가를 요청합니다.

1. 서버 할당량 **유형당 데이터 웨어하우스 단위(DT)를** 선택합니다.

1. **리소스** 목록에서 대상리소스를 선택합니다.

1. 요청 **할당량** 필드에 요청하는 새 DWU 제한을 입력합니다.

   ![DWU 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>구독당 서버

다음 단계를 사용하여 구독당 서버 수 증가를 요청합니다.

1. 구독 할당량 **유형당 서버를** 선택합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택합니다. 할당량은 각 지역의 구독당 할당량입니다.

1. 새 **할당량** 필드에 해당 지역의 최대 서버 수에 대한 요청을 입력합니다.

   ![서버 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>리전에 대한 구독 액세스 활성화

일부 혜택 유형은 일부 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시될 수 있습니다.

`This location is not available for subscription`

구독이 특정 리전에서 액세스해야 하는 경우 **기타 할당량 요청** 옵션을 사용하여 액세스를 요청하십시오. 요청에서 지역에 대해 사용할 제품 및 SKU 세부 정보를 지정합니다. 오퍼링 및 SKU 옵션을 탐색하려면 [Azure Synapse 분석 가격 책정을](https://azure.microsoft.com/pricing/details/synapse-analytics/)참조하십시오.

![기타 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 지원 요청의 나머지 세부 정보를 채우는 것입니다. 그런 다음: **검토 + 만들기>>** 선택하고 요청 세부 정보를 검토한 후 **만들기를** 클릭하여 요청을 제출합니다.

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링

지원 요청을 제출한 후 Azure 지원 팀에서 연락을 드립니다. 요청 상태 및 세부 정보를 확인하려면 대시보드에서 **모든 지원 요청**을 클릭합니다.

![상태 확인](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>기타 리소스

[스택 오버플로또는](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) [Azure SQL 데이터 웨어하우스 MSDN 포럼을](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)통해 Azure Synapse 분석 커뮤니티에 연결할 수도 있습니다.

