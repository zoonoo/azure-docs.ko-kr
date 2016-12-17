---
title: "이전 시점으로 Azure SQL Database 복원(Azure Portal) | Microsoft Docs"
description: "이전 시점으로 Azure SQL Database를 복원합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Azure Portal을 사용하여 이전 시점으로 Azure SQL Database 복원
> [!div class="op_single_selector"]
> * [개요](sql-database-recovery-using-backups.md)
> * [지정 시간 복원: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

이 문서에서는 Azure Portal을 사용하여 [SQL Database 자동화된 백업](sql-database-automated-backups.md) 에서 이전 시점으로 데이터베이스를 복원하는 방법을 보여 줍니다.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>이전 시점으로 SQL 데이터베이스 복원
Azure Portal에서 복원할 데이터베이스를 선택합니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 화면 왼쪽에서 **추가 서비스** > **SQL Database**를 선택합니다.
3. 복원할 데이터베이스를 클릭합니다.
4. 데이터베이스 페이지 위쪽에서 **복원**을 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간(UTC 시간)을 선택하고 **확인**을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>복원 작업 모니터링
1. 이전 단계에서 **확인**을 클릭한 후에는 페이지 오른쪽 위에 있는 알림 아이콘을 클릭하고 **SQL Database 복원** 알림을 클릭하여 세부 정보를 확인합니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. 복원 상태에 대한 정보가 포함된 SQL Database 복원 페이지가 열립니다. 행 항목을 클릭하여 자세한 내용을 확인할 수 있습니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


