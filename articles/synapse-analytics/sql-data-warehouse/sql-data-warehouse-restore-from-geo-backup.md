---
title: 지리적 백업에서 데이터 웨어하우스 복원
description: SQL 풀을 지역 복원하는 방법 가이드입니다.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4390ed39c86e041d3fbd776415f0ffbe71f605bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350160"
---
# <a name="geo-restore-for-sql-pool"></a>SQL 풀에 대한 지리적 복원

이 문서에서는 Azure 포털 및 PowerShell을 통해 지리적 백업에서 SQL 풀을 복원하는 방법을 배웁니다.

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**DTU 용량을 확인합니다.** 각 SQL 풀은 기본 DTU 할당량이 있는 SQL 서버(예: myserver.database.windows.net)에 의해 호스팅됩니다. 복원중인 데이터베이스에 대해 SQL 서버에 충분한 남은 DTU 할당량이 있는지 확인합니다. 필요한 DTU를 계산하거나 더 많은 DTU를 요청하는 방법을 알아보려면 [DTU 할당량 변경 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 참조합니다.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>PowerShell을 통해 Azure 지리적 지역에서 복원

지리적 백업에서 복원하려면 [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) 및 [복원-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) cmdlet을 사용합니다.

> [!NOTE]
> 지역 복원을 Gen2로 수행할 수 있습니다! 이렇게 하려면 Gen2 ServiceObjectiveName(예: DW1000**c**)을 선택적 매개 변수로 지정하세요.
>

1. 시작하기 전에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)을 설치해야 합니다.
2. PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터 웨어하우스가 포함된 구독을 선택합니다.
4. 복구할 데이터 웨어하우스를 가져옵니다.
5. 데이터 웨어하우스에 대한 복구 요청을 만듭니다.
6. 지역 복원된 데이터 웨어하우스의 상태를 확인합니다.
7. 복원이 완료된 후에 데이터 웨어하우스를 구성하려면 [복구 후 데이터베이스 구성]( ../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)을 참조하세요.

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

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Azure 포털을 통해 Azure 지리적 지역에서 복원

아래 설명된 단계에 따라 지리적 백업에서 SQL 풀을 복원합니다.

1. [Azure 포털](https://portal.azure.com/) 계정에 로그인합니다.
1. **+ 리소스 만들기**를 클릭합니다. 

![새로운 DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. **데이터베이스를 클릭한** 다음 **Azure 시냅스 분석(이전 SQL DW)**.

![새로운 DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. 기본 탭에서 요청한 정보를 입력하고 **다음: 추가 설정을** **클릭합니다.**

![기본 사항](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. 기존 데이터 매개 **변수를 사용하여** **백업을** 선택하고 스크롤 아래로 스크롤 옵션에서 적절한 백업을 선택합니다. **검토 + 만들기를**클릭합니다.
 
![백업(backup)](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. 데이터 웨어하우스가 복원되면 **상태가** 온라인 상태인지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [기존 SQL 풀 복원](sql-data-warehouse-restore-active-paused-dw.md)
- [삭제된 SQL 풀 복원](sql-data-warehouse-restore-deleted-dw.md)