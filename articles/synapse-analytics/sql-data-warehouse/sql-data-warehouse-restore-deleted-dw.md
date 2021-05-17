---
title: 삭제된 전용 SQL 풀(이전의 SQL DW) 복원
description: Azure Synapse Analytics에서 삭제된 전용 SQL 풀을 복원하는 방법에 대한 가이드입니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b85e16d546989200adbfc37c2ef656022ad87cef
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108163466"
---
# <a name="restore-a-deleted-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 삭제된 전용 SQL 풀(이전의 SQL DW) 복원

이 문서에서는 Azure Portal 또는 PowerShell을 사용하여 전용 SQL 풀(이전의 SQL DW)을 복원하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 전용 SQL 풀(이전의 SQL DW)은 기본 DTU 할당량이 있는 [논리 SQL Server](../../azure-sql/database/logical-servers.md)(예: myserver.database.windows.net)에서 호스트합니다.  서버에 데이터베이스를 복원하기에 충분한 DTU 할당량이 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>PowerShell을 통해 삭제된 데이터 웨어하우스 복원

삭제된 전용 SQL 풀(이전의 SQL DW)을 복원하려면 [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) cmdlet을 사용합니다. 해당 서버도 삭제된 경우에는 해당 데이터 웨어하우스를 복원할 수 없습니다.

1. 시작하기 전에 [Azure PowerShell을 설치](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)해야 합니다.
2. PowerShell을 엽니다.
3. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
4. 복원하려는 삭제된 전용 SQL 풀(이전의 SQL DW)이 포함된 구독을 선택합니다.
5. 삭제된 특정 데이터 웨어하우스를 가져옵니다.
6. 삭제된 전용 SQL 풀(이전의 SQL DW) 복원
    1. 삭제된 전용 SQL 풀(이전의 SQL DW)을 다른 서버로 복원하려면 다른 서버 이름을 지정해야 합니다.  이 서버는 다른 리소스 그룹 및 지역에 있을 수도 있습니다.
    1. 다른 구독으로 복원하려면 [이동](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) 단추를 사용하여 서버를 다른 구독으로 이동합니다.
7. 복원된 데이터 웨어하우스가 온라인 상태인지 확인합니다.
8. 복원이 완료된 후 [복구 후 데이터베이스 구성](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)에 따라 복구된 데이터 웨어하우스를 구성할 수 있습니다.

```powershell
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
2. 삭제된 데이터 웨어하우스가 호스트된 서버로 이동합니다.
3. 목차에서 **삭제된 데이터베이스** 아이콘을 선택합니다.

    ![삭제된 데이터베이스](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. 복원하려는 삭제된 Azure Synapse Analytics를 선택합니다.

    ![삭제된 데이터베이스 선택](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. 새 **데이터베이스 이름** 을 지정하고 **확인** 을 클릭합니다.

    ![데이터베이스 이름 지정](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>다음 단계

- [기존 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [지역 백업 전용 SQL 풀(이전의 SQL DW) 복원](sql-data-warehouse-restore-from-geo-backup.md)
