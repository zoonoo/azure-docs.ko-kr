---
title: "이전 시점으로 Azure SQL Database 복원 | Microsoft Docs"
description: "이전 시점으로 Azure SQL 데이터베이스를 복원합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>이전 시점으로 Azure SQL 데이터베이스를 복원합니다. 

이 방법 문서에서는 [SQL Database 자동화된 백업](sql-database-automated-backups.md)에서 이전 시점으로 데이터베이스를 복원하는 방법을 보여 줍니다. 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Azure Portal을 사용하여 이전 시점으로 복원

> [!TIP]
> 자습서는 [데이터 보호 및 복구를 위한 백업 및 복원 시작](sql-database-get-started-backup-recovery.md)을 참조하세요.
>

Azure Portal에서 복원할 데이터베이스를 선택합니다.

1. [Azure 포털](https://portal.azure.com)을 엽니다.
2. 화면 왼쪽에서 **추가 서비스** > **SQL Database**를 선택합니다.
3. 복원할 데이터베이스를 클릭합니다.
4. 데이터베이스 페이지 위쪽에서 **복원**을 선택합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간(UTC 시간)을 선택하고 **확인**을 클릭합니다.
   
   ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 이전 단계에서 **확인**을 클릭한 후에는 페이지 오른쪽 위에 있는 알림 아이콘을 클릭하고 **SQL Database 복원** 알림을 클릭하여 세부 정보를 확인합니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. 복원 상태에 대한 정보가 포함된 SQL Database 복원 페이지가 열립니다. 행 항목을 클릭하여 자세한 내용을 확인할 수 있습니다.
   
    ![Azure SQL 데이터베이스 복원](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>PowerShell을 사용하여 이전 시점으로 복원

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>데이터베이스를 지정 시간의 단일 데이터베이스로 복원
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>데이터베이스를 지정 시간의 탄력적 풀에 복원
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>다음 단계
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 데이터베이스의 서비스에서 생성된 백업에서 가장 오래된 복원 지점을 보려면 [가장 오래된 복원 지점 보기](sql-database-view-oldest-restore-point.md)를 참조하세요.
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


