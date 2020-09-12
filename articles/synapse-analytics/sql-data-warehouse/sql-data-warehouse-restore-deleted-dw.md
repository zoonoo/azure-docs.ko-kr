---
title: 삭제 된 SQL 풀 복원
description: 삭제 된 SQL 풀을 복원 하는 방법에 대 한 지침입니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 879844efdc5c2b40f69ee5f79305d4dfa596fd27
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460731"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Azure Synapse Analytics를 사용 하 여 삭제 된 SQL 풀 복원

이 문서에서는 Azure Portal 또는 PowerShell을 사용 하 여 SQL을 복원 하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 SQL 풀은 기본 DTU 할당량이 있는 [논리 SQL server](../../azure-sql/database/logical-servers.md) (예: myserver.database.windows.net)에 의해 호스팅됩니다.  서버에 복원 중인 데이터베이스에 대 한 DTU 할당량이 충분히 남아 있는지 확인 합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>PowerShell을 통해 삭제 된 데이터 웨어하우스 복원

삭제 된 SQL 풀을 복원 하려면 [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet을 사용 합니다. 해당 서버도 삭제 된 경우 해당 데이터 웨어하우스를 복원할 수 없습니다.

1. 시작 하기 전에 [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원할 삭제 된 SQL 풀을 포함 하는 구독을 선택 합니다.
5. 특정 삭제 된 데이터 웨어하우스를 가져옵니다.
6. 삭제 된 SQL 풀 복원
    1. 삭제 된 SQL 풀을 다른 서버로 복원 하려면 다른 서버 이름을 지정 해야 합니다.  이 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
    1. 다른 구독으로 복원 하려면 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) 단추를 사용 하 여 서버를 다른 구독으로 이동 합니다.
7. 복원 된 데이터 웨어하우스가 온라인 상태 인지 확인 합니다.
8. 복원이 완료 된 후 [복구 후 데이터베이스 구성](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)에 따라 복구 된 데이터 웨어하우스를 구성할 수 있습니다.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
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

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Azure Portal을 사용하여 삭제된 데이터베이스 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 삭제 된 데이터 웨어하우스가 호스팅된 서버로 이동 합니다.
3. 목차에서 **삭제 된 데이터베이스** 아이콘을 선택 합니다.

    ![삭제된 데이터베이스](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 복원 하려는 삭제 된 Azure Synapse Analytics를 선택 합니다.

    ![삭제된 데이터베이스 선택](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 새 **데이터베이스 이름을** 지정 하 고 **확인** 을 클릭 합니다.

    ![데이터베이스 이름 지정](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>다음 단계

- [기존 SQL 풀 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [지역 백업 SQL 풀에서 복원](sql-data-warehouse-restore-from-geo-backup.md)
