---
title: "이전 시점으로 Azure SQL Database 복원(PowerShell) | Microsoft Docs"
description: "이전 시점으로 Azure SQL 데이터베이스를 복원합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 3d1ecd68-5e78-4e8d-9347-b37b5fa15f36
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b70d51cbd4b245605173ee596fc0ea587668b92


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>PowerShell을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원
> [!div class="op_single_selector"]
> * [개요](sql-database-recovery-using-backups.md)
> * [지정 시간 복원: Azure 포털](sql-database-point-in-time-restore-portal.md)
> 
> 

이 문서에서는 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)에서 이전 시점으로 데이터베이스를 복원하는 방법을 보여 줍니다. 이 작업은 PowerShell을 사용하여 수행할 수 있습니다.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>데이터베이스를 지정 시간의 독립 실행형 데이터베이스로 복원합니다.
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>데이터베이스를 지정 시간의 탄력적 데이터베이스 풀에 복원합니다.
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다.
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) cmdlet을 사용하여 데이터베이스를 지정 시간으로 복원합니다.
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>다음 단계
* 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)
* Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)
* 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)
* 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)  
* 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


