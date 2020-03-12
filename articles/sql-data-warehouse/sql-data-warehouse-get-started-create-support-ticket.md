---
title: 요청 할당량이 늘어나고 지원 받기
description: Azure Synapse Analytics에 대 한 Azure Portal에서 지원 요청을 만드는 방법 요청 할당량을 늘리거나 문제 해결을 지원 합니다.
services: sql-data-warehouse
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/10/2020
author: kevinvngo
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88cc737fddfdd896575227e036df087c13a4d0f6
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79130135"
---
# <a name="request-quota-increases-and-get-support-for-azure-synapse-analytics"></a>요청 할당량이 늘어나고 Azure Synapse Analytics에 대 한 지원을 받습니다.

이 문서에서는 Azure Synapse Analytics에 대 한 Azure Portal 지원 티켓을 제출 하는 방법을 설명 합니다. 이 프로세스를 통해 사용자는 할당량 증가를 요청 하거나 엔지니어링 지원 팀에 대 한 기술 지원 요청을 제출할 수 있습니다.

## <a name="create-a-support-ticket"></a>지원 티켓 만들기

다음 단계를 사용 하 여 Azure Synapse Analytics에 대 한 Azure Portal에서 새 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택 합니다.

   ![도움말 + 지원 링크](./media/sql-data-warehouse-get-started-create-support-ticket/help-plus-support.png)


1. **도움말 + 지원**에서 **새 지원 요청**을 선택 합니다.

    ![새 지원 요청 만들기](./media/sql-data-warehouse-get-started-create-support-ticket/new-support-request.png)

1. [Azure 지원 계획](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 검토 합니다.

   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **중단-수정** 지원은 [개발자](https://azure.microsoft.com/support/plans/developer/), [표준](https://azure.microsoft.com/support/plans/standard/), [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/)또는 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원을 통해 제공 됩니다. 고장 수리 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **개발자 멘토링** 및 **자문 서비스** 는 [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 및 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원 수준에서 제공 됩니다.

   프리미어 지원 계획이 있는 경우 [Microsoft 프리미어 온라인 포털](https://premier.microsoft.com/)에서 Azure Synapse 분석 문제를 보고할 수도 있습니다. 범위, 응답 시간, 가격 책정 등을 포함 한 다양 한 지원 계획에 대해 자세히 알아보려면 [Azure 지원 계획](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) 을 참조 하세요.  Azure 지원에 대 한 질문과 대답은 [azure 지원 faq](https://azure.microsoft.com/support/faq/)를 참조 하세요.

1. **문제 유형**에서 적절 한 문제 유형을 선택 합니다. 중단-수정 문제에 대해 **기술**을 선택 합니다. 할당량 증가 요청의 경우 **서비스 및 구독 제한 (할당량)** 을 선택 합니다.

   ![문제 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-issue-type.png)  

   > [!NOTE]
   > 이 문서의 나머지 부분에서는 할당량 증가 요청을 집중적으로 살펴봅니다. 그러나 문제 해결 지원 요청에 대해 여기에서 **기술** 을 선택할 수도 있습니다. **기술**을 선택 하는 경우 요약을 입력 하 라는 메시지가 표시 되 면 **문제 유형 선택**을 선택 하 여 문제 유형을 식별 합니다. 문제를 해결 하는 데 도움이 되는 솔루션을 볼 수 있습니다. 제공 된 해결 방법으로 문제가 해결 되지 않으면 **다음: 세부 정보** 를 선택 하 고 양식을 완료 하 여 지원 티켓을 제출 합니다.

1. 할당량 증가 요청의 경우 **할당량 유형에**대해 **Azure Synapse Analytics** 를 선택 합니다. 그런 후 **다음: 솔루션 > >** 를 선택 합니다.

   ![할당량 유형 선택](./media/sql-data-warehouse-get-started-create-support-ticket/select-quota-type.png)

1. **세부** 정보 창에서 세부 정보 **제공** 을 선택 하 여 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/sql-data-warehouse-get-started-create-support-ticket/provide-details-link.png)

## <a name="quota-request-types"></a>할당량 요청 유형

**세부 정보 제공** 을 클릭 하면 추가 정보를 추가할 수 있는 **할당량 정보** 창이 표시 됩니다. 다음 섹션에서는 Azure Synapse Analytics에 사용할 수 있는 다양 한 할당량 요청에 대해 설명 합니다.

### <a name="data-warehouse-units-dwus-per-server"></a>서버당 DWUs (데이터 웨어하우스 단위)

서버 당 DWUs의 증가를 요청 하려면 다음 단계를 사용 합니다.

1. 서버 할당량 유형 **당 dtu (데이터 웨어하우스 단위)** 를 선택 합니다.

1. **리소스** 목록에서 대상으로 할 리소스를 선택 합니다.

1. **요청 할당량** 필드에 요청 하는 새 dwu 제한을 입력 합니다.

   ![DWU 할당량 세부 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-dwus.png)

### <a name="servers-per-subscription"></a>구독 당 서버 수

다음 단계를 사용 하 여 구독 당 서버 수의 증가를 요청 합니다.

1. **구독 당 서버** 할당량 유형을 선택 합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택 합니다. 할당량은 각 지역의 구독 당입니다.

1. **새 할당량** 필드에 해당 지역의 최대 서버 수에 대 한 요청을 입력 합니다.

   ![서버 할당량 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-servers.png)

### <a name="enable-subscription-access-to-a-region"></a>지역에 대 한 구독 액세스 사용

일부 제품 유형은 모든 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시 될 수 있습니다.

`This location is not available for subscription`

구독에 특정 지역에 대 한 액세스 권한이 필요한 경우 **다른 할당량 요청** 옵션을 사용 하 여 액세스를 요청 하세요. 요청에서 지역에 대해 사용 하도록 설정할 제품 및 SKU 세부 정보를 지정 합니다. 제품 및 SKU 옵션을 탐색 하려면 [Azure Synapse Analytics 가격 책정](https://azure.microsoft.com/pricing/details/synapse-analytics/)을 참조 하세요.

![기타 할당량 정보](./media/sql-data-warehouse-get-started-create-support-ticket/quota-details-whitelisting.png)

## <a name="submit-your-request"></a>요청 제출

최종 단계는 SQL Database 지원 요청의 나머지 세부 정보를 입력 하는 것입니다. 그런 후 다음을 선택 합니다 **. 검토 + 만들기 > >** 를 선택 하 고 요청 세부 정보를 검토 한 후 **만들기** 를 클릭 하 여 요청을 제출 합니다.

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링

지원 요청을 제출 하면 Azure 지원 팀이 연락을 드립니다. 요청 상태 및 세부 정보를 확인하려면 대시보드에서 **모든 지원 요청**을 클릭합니다.

![상태 확인](./media/sql-data-warehouse-get-started-create-support-ticket/monitor-ticket.png)

## <a name="other-resources"></a>기타 리소스

[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse+or+azure-sql-data-warehouse) 또는 [Azure SQL Data Warehouse MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)을 통해 Azure Synapse Analytics 커뮤니티에 연결할 수도 있습니다.

