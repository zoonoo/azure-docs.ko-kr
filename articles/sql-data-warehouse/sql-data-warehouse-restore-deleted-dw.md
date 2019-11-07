---
title: 삭제된 데이터 웨어하우스 복원
description: 삭제 된 Azure SQL Data Warehouse 복원에 대 한 지침입니다.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cb09b4808bd6d59d2f70e85d204ab8451d501cee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692600"
---
# <a name="restore-a-deleted-azure-sql-data-warehouse"></a>삭제 된 Azure SQL Data Warehouse 복원

이 문서에서는 Azure Portal 및 PowerShell을 사용 하 여 삭제 된 SQL Data Warehouse를 복원 하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 SQL Data Warehouse는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에서 호스팅됩니다.  SQL server에 복원 중인 데이터베이스에 대 한 DTU 할당량이 충분히 남아 있는지 확인 합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][Request a DTU quota change]을 참조합니다.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>PowerShell을 통해 삭제 된 데이터 웨어하우스 복원

삭제 된 SQL Data Warehouse를 복원 하려면 [AzSqlDatabase][Restore-AzSqlDatabase] cmdlet을 사용 합니다. 해당 논리 서버도 삭제 된 경우 해당 데이터 웨어하우스를 복원할 수 없습니다.

1. 시작 하기 전에 [Azure PowerShell을 설치][Install Azure PowerShell]해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 삭제 된 데이터 웨어하우스를 포함 하는 구독을 선택 합니다.
5. 특정 삭제 된 데이터 웨어하우스를 가져옵니다.
6. 삭제 된 데이터 웨어하우스 복원
    1. 삭제 된 SQL Data Warehouse을 다른 논리 서버로 복원 하려면 다른 논리 서버 이름을 지정 해야 합니다.  이 논리 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
    1. 다른 구독으로 복원 하려면 [이동][Move] 단추를 사용 하 여 논리 서버를 다른 구독으로 이동 합니다.
1. 복원 된 데이터 웨어하우스가 온라인 상태 인지 확인 합니다.
1. 복원이 완료 된 후 [복구 후 데이터베이스 구성][Configure your database after recovery]에 따라 복구 된 데이터 웨어하우스를 구성할 수 있습니다.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>" 
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Azure Portal을 사용하여 삭제된 데이터베이스 복원

1. [Azure 포털][Azure portal]에 로그인합니다.
2. 삭제 된 데이터 웨어하우스가 호스팅된 SQL server로 이동 합니다.
3. 목차에서 **삭제 된 데이터베이스** 아이콘을 선택 합니다.

    ![삭제된 데이터베이스](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 복원 하려는 삭제 된 SQL Data Warehouse를 선택 합니다.

    ![삭제된 데이터베이스 선택](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 새 **데이터베이스 이름을** 지정 하 고 **확인** 을 클릭 합니다.

    ![데이터베이스 이름 지정](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>다음 단계
- [기존 데이터 웨어하우스 복원][Restore an existing data warehouse]
- [지역 백업 데이터 웨어하우스에서 복원][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[support ticket]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket
[Move]:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources#use-the-portal
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
