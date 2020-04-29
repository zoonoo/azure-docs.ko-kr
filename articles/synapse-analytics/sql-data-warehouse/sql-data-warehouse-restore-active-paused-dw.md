---
title: 기존 데이터 웨어하우스 복원
description: 기존 SQL 풀 복원에 대 한 방법 가이드입니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 6fa8bd42eb067124ab6ea1db77e2f3d6fba79638
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745220"
---
# <a name="restore-an-existing-sql-pool"></a>기존 SQL 풀 복원

이 문서에서는 Azure Portal 및 PowerShell을 사용 하 여 Azure Synapse Analytics에서 기존 SQL 풀을 복원 하는 방법에 대해 알아봅니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 풀은 기본 DTU 할당량이 있는 SQL server (예: myserver.database.windows.net)에서 호스팅됩니다. SQL server에 복원 중인 데이터베이스에 대 한 DTU 할당량이 충분히 남아 있는지 확인 합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="before-you-begin"></a>시작하기 전에

1. [Azure PowerShell을 설치](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. 복원 하려는 기존 복원 지점이 있어야 합니다. 새 복원을 만들려면 자습서를 참조 [하 여 새 사용자 정의 복원 지점을 만듭니다](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>PowerShell을 통해 기존 SQL 풀 복원

복원 지점에서 기존 SQL 풀을 복원 하려면 [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용 합니다.

1. PowerShell을 엽니다.

2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.

3. 복원할 데이터베이스가 포함된 구독을 선택합니다.

4. SQL 풀의 복원 지점이 나열 됩니다.

5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.

6. [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell cmdlet을 사용 하 여 SQL 풀을 원하는 복원 지점으로 복원 합니다.
        1. 다른 논리 서버에 SQL 풀을 복원 하려면 다른 논리 서버 이름을 지정 해야 합니다.  이 논리 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
        2. 다른 구독으로 복원 하려면 ' 이동 ' 단추를 사용 하 여 논리 서버를 다른 구독으로 이동 합니다.

7. 복원 된 SQL 풀이 온라인 상태 인지 확인 합니다.

8. 복원이 완료 된 후 [복구 후 데이터베이스 구성](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)에 따라 복구 된 SQL 풀을 구성할 수 있습니다.

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

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Azure Portal를 통해 기존 SQL 풀 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 복원 하려는 SQL 풀로 이동 합니다.
3. [개요] 블레이드의 위쪽에서 **복원**을 선택합니다.

    ![ 복원 개요](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다. SQL 풀에 자동 복원 지점이 없는 경우 몇 시간을 기다리거나 복원 하기 전에 사용자 정의 복원 지점을 만듭니다. 사용자 정의 복원 지점의 경우 기존 항목을 선택 하거나 새로 만듭니다. **서버의**경우 다른 리소스 그룹 및 지역에서 논리 서버를 선택 하거나 새 리소스 그룹을 만들 수 있습니다. 모든 매개 변수를 제공한 후 **검토 + 복원**을 클릭 합니다.

    ![자동 복원 지점](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>다음 단계

- [삭제 된 SQL 풀 복원](sql-data-warehouse-restore-deleted-dw.md)
- [지역 백업 SQL 풀에서 복원](sql-data-warehouse-restore-from-geo-backup.md)
