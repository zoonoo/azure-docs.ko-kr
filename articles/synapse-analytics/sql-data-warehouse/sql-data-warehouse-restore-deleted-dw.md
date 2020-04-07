---
title: 삭제된 SQL 풀 복원
description: 삭제 된 SQL 풀을 복원하는 방법을 안내합니다.
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
ms.openlocfilehash: d2e2fdb181b553d330368b043b75159e211dd0d2
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745135"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Azure 시냅스 분석을 사용하여 삭제된 SQL 풀 복원

이 문서에서는 Azure 포털 또는 PowerShell을 사용하여 SQL을 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 SQL 풀은 기본 DTU 할당량이 있는 SQL 서버(예: myserver.database.windows.net)에 의해 호스팅됩니다.  복원중인 데이터베이스에 대해 SQL 서버에 충분한 남은 DTU 할당량이 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>PowerShell을 통해 삭제된 데이터 웨어하우스 복원

삭제된 SQL 풀을 복원하려면 [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet을 사용합니다. 해당 논리 서버도 삭제된 경우 해당 데이터 웨어하우스를 복원할 수 없습니다.

1. 시작하기 전에 [Azure PowerShell](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)을 설치해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 삭제된 데이터 웨어하우스가 포함된 구독을 선택합니다.
5. 삭제된 특정 데이터 웨어하우스를 가져옵니다.
6. 삭제된 데이터 웨어하우스 복원
    1. 삭제된 SQL 데이터 웨어하우스를 다른 논리 서버로 복원하려면 다른 논리 서버 이름을 지정해야 합니다.  이 논리 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
    1. 다른 구독으로 복원하려면 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) 단추를 사용하여 논리 서버를 다른 구독으로 이동합니다.
7. 복원된 데이터 웨어하우스가 온라인 상태입니다.
8. 복원이 완료되면 [복구 후 데이터베이스를 구성하여](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)복구된 데이터 웨어하우스를 구성할 수 있습니다.

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

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 삭제된 데이터 웨어하우스가 호스팅된 SQL 서버로 이동합니다.
3. 목회의에서 **삭제된 데이터베이스** 아이콘을 선택합니다.

    ![삭제된 데이터베이스](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 복원할 삭제된 SQL 데이터 웨어하우스를 선택합니다.

    ![삭제된 데이터베이스 선택](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 새 **데이터베이스 이름** 지정 및 **확인을** 클릭합니다.

    ![데이터베이스 이름 지정](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>다음 단계

- [기존 SQL 풀 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [지리적 백업 SQL 풀에서 복원](sql-data-warehouse-restore-from-geo-backup.md)
