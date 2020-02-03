---
title: 지원 티켓을 만드는 방법
description: Azure SQL Data Warehouse에서 지원 티켓을 만드는 방법
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 04fd6267782ab89e12288dadb64d28d1ef1746b5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717826"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse에 대한 지원 티켓을 만드는 방법
SQL Data Warehouse에 문제가 있는 경우 엔지니어링 지원 팀에서 도움을 받을 수 있도록 지원 티켓을 만드세요.

## <a name="create-a-support-ticket"></a>지원 티켓 만들기
1. [Azure Portal](https://portal.azure.com/)을 엽니다.
2. 홈 화면에서 **도움말 + 지원** 탭을 클릭합니다.
   
    ![도움말 + 지원](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. 도움말 + 지원 블레이드에서 **새 지원 요청**을 클릭하고 **기본** 블레이드에 정보를 입력합니다.

   [Azure 지원 계획](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 선택합니다.
   
   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **중단-수정** 지원은 [개발자](https://azure.microsoft.com/support/plans/developer/), [표준](https://azure.microsoft.com/support/plans/standard/), [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/)또는 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원을 통해 제공 됩니다. 고장 수리 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **개발자 멘토링** 및 **자문 서비스** 는 [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 및 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원 수준에서 제공 됩니다. 
     
     프리미어 지원 계획이 있는 경우 [Microsoft 프리미어 온라인 포털](https://premier.microsoft.com/)에서 SQL Data Warehouse 관련 문제를 보고할 수도 있습니다. 범위, 응답 시간, 가격 책정 등을 포함 한 다양 한 지원 계획에 대해 자세히 알아보려면 [Azure 지원 계획](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/) 을 참조 하세요.  Azure 지원에 대 한 질문과 대답은 [azure 지원 faq](https://azure.microsoft.com/support/faq/)를 참조 하세요.  
        
     ![기본 블레이드](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![기본 블레이드1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. **문제** 블레이드에 정보를 입력합니다.

    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > 기본적으로 각 SQL 서버(예: myserver.database.windows.net)의 **DTU 할당량**은 45,000입니다. 이 할당량은 안전을 위한 제한일 뿐입니다. 지원 티켓을 만들고 *할당량* 을 요청 형식으로 선택하여 할당량을 늘릴 수 있습니다. DTU 요구를 계산 하려면 7.5를 필요한 총 [Dwu](sql-data-warehouse-overview-what-is.md) 로 곱합니다. 예를 들어 하나의 SQL Server에서 두 개의 DW6000을 호스트하려면 DTU 할당량인 90,000을 요청해야 합니다.  포털의 SQL Server 블레이드에서 현재 DTU 사용량을 볼 수 있습니다. 일시 중지되거나 일시 중지되지 않은 데이터베이스는 모두 DTU 할당량에 포함됩니다. 
   > 
   > 
   
5. **연락처 정보**를 입력합니다.

   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. **만들기** 를 클릭하여 지원 요청을 제출합니다.

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링
지원 요청을 제출 하면 Azure 지원 팀이 연락을 드립니다. 요청 상태 및 세부 정보를 확인하려면 대시보드에서 **모든 지원 요청**을 클릭합니다.

![상태 확인](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>기타 리소스
[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/) 또는 [Azure SQL Data Warehouse MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/)을 통해 SQL Data Warehouse 커뮤니티에 연결할 수도 있습니다.

 
