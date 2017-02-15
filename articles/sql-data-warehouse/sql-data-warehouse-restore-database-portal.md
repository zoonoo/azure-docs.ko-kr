---
title: "Azure SQL Data Warehouse 복원(포털) | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스 복원을 위한 Azure 포털 작업."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: barbkess
editor: 
ms.assetid: b0aef539-7657-4b0e-9899-74098f5c21bc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 09/21/2016
ms.author: lakshmir;barbkess;sonyama
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2cb8cb2b58df5cc209b1f966c792ca0f4082e652


---
# <a name="restore-an-azure-sql-data-warehouse-portal"></a>Azure SQL 데이터 웨어하우스 복원(포털)
> [!div class="op_single_selector"]
> * [개요][개요]
> * [포털][포털]
> * [PowerShell][PowerShell]
> * [REST (영문)][REST (영문)]
> 
> 

이 문서에서는 Azure 포털을 사용하여 Azure SQL 데이터 웨어하우스를 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에
**DTU 용량을 확인합니다.**  각 SQL 데이터 웨어하우스는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에 의해 호스팅됩니다.  SQL 데이터 웨어하우스를 복원하기 전에 SQL 서버에 복원 중인 데이터베이스에 대해 충분한 DTU 할당량이 남아 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][DTU 할당량 변경 요청]을 참조하세요.

## <a name="restore-an-active-or-paused-database"></a>활성 또는 일시 중지된 데이터베이스 복원
데이터베이스를 복원하려면

1. [Azure 포털][Azure 포털]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버** 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 서버로 이동한 후 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-server.png)
4. 복원하려는 SQL 데이터 웨어하우스를 찾아서 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-active-dw.png)
5. 데이터 웨어하우스 블레이드의 위쪽에서 **복원**
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-select-restore-from-active.png)
6. 새 **데이터베이스 이름**
7. 최신 **복원 지점**
   
   1. 최신 복원 지점을 선택했는지 확인합니다.  복원 지점은 UTC로 표시되므로 표시된 기본 옵션이 최신 복원 지점이 아닐 때도 있습니다.
      
      ![](./media/sql-data-warehouse-restore-database-portal/01-restore-blade-from-active.png)
8.  **확인**
9. 데이터베이스 복원 프로세스가 시작되며 **알림**

> [!NOTE]
> 복원이 완료된 후 [복구 후 데이터베이스 구성][복구 후 데이터베이스 구성]에 따라 복구된 데이터베이스를 구성할 수 있습니다.
> 
> 

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원
삭제된 데이터베이스를 복원하려면:

1. [Azure 포털][Azure 포털]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버** 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/01-browse-for-sql-server.png)
3. 서버로 이동한 후 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-server.png)
4. 서버 블레이드의 작업 섹션까지 아래로 스크롤
5. **삭제된 데이터베이스** 타일 클릭
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dws.png)
6. 복원할 삭제된 데이터베이스 선택
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-select-deleted-dw.png)
7. 새 **데이터베이스 이름**
   
    ![](./media/sql-data-warehouse-restore-database-portal/02-restore-blade-from-deleted.png)
8.  **확인**
9. 데이터베이스 복원 프로세스가 시작되며 **알림**

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성][복구 후 데이터베이스 구성]을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
Azure SQL Database 버전의 무중단 업무 방식 기능에 대해 알아보려면 [Azure SQL 데이터베이스 무중단 업무 방식 개요][Azure SQL 데이터베이스 무중단 업무 방식 개요]를 읽으세요.

<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 무중단 업무 방식 개요]: ../sql-database/sql-database-business-continuity.md
[개요]: ./sql-data-warehouse-restore-database-overview.md
[포털]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST (영문)]: ./sql-data-warehouse-restore-database-rest-api.md
[복구 후 데이터베이스 구성]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[DTU 할당량 변경 요청]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change

<!--MSDN references-->

<!--Blog references-->

<!--Other Web references-->
[Azure 포털]: https://portal.azure.com/



<!--HONumber=Nov16_HO3-->


