---
title: 지역 백업에서 Azure SQL Data Warehouse 복원 | Microsoft Docs
description: Azure SQL Data Warehouse 지역 복원에 대 한 방법 가이드입니다.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68426656"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>지역 복원 Azure SQL Data Warehouse

이 문서에서는 Azure Portal 및 PowerShell을 통해 지리적 백업에서 데이터 웨어하우스를 복원 하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 SQL Data Warehouse는 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에서 호스팅됩니다. SQL server에 복원 중인 데이터베이스에 대 한 DTU 할당량이 충분히 남아 있는지 확인 합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청][Request a DTU quota change]을 참조합니다.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell을 통해 Azure 지역에서 복원

지역 백업에서 복원 하려면 [AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] 및 [AzSqlDatabase][Restore-AzSqlDatabase] cmdlet을 사용 합니다.

> [!NOTE]
> 지역 복원을 Gen2로 수행할 수 있습니다! 이렇게 하려면 Gen2 ServiceObjectiveName(예: DW1000**c**)을 선택적 매개 변수로 지정하세요.
>

1. 시작 하기 전에 [Azure PowerShell을 설치][Install Azure PowerShell]해야 합니다.
2. PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터 웨어하우스를 포함 하는 구독을 선택 합니다.
4. 복구 하려는 데이터 웨어하우스를 가져옵니다.
5. 데이터 웨어하우스에 대 한 복구 요청을 만듭니다.
6. 지역 복원 데이터 웨어하우스의 상태를 확인 합니다.
7. 복원이 완료된 후에 데이터 웨어하우스를 구성하려면 [복구 후 데이터베이스 구성][Configure your database after recovery]을 참조하세요.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure Portal를 통해 Azure 지역에서 복원

지역 백업에서 Azure SQL Data Warehouse를 복원 하려면 아래에 설명 된 단계를 따르세요.

1. [Azure Portal][Azure portal] 계정에 로그인 합니다.
1. **+ 리소스 만들기** 를 클릭 하 고 SQL Data Warehouse를 검색 한 후 **만들기**를 클릭 합니다.

    ![새 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. **기본 사항** 탭에서 요청 된 정보를 입력 하 고 **다음을 클릭 합니다. 추가 설정**입니다.

    ![기본 사항](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. **기존 데이터** 매개 변수 사용에 대해 **백업** 을 선택 하 고 아래로 스크롤 옵션에서 적절 한 백업을 선택 합니다. **검토 + 만들기**를 클릭 합니다.
 
   ![백업(backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. 데이터 웨어하우스가 복원 되 면 **상태가** 온라인 인지 확인 합니다.

## <a name="next-steps"></a>다음 단계
- [기존 데이터 웨어하우스 복원][Restore an existing data warehouse]
- [삭제 된 데이터 웨어하우스 복원][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
