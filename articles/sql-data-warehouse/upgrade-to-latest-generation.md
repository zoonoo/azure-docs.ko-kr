---
title: 최신 세대 Azure SQL Data Warehouse로 업그레이드 | Microsoft Docs
description: Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: 522f1f2f24e8c8c3f68a42569d4057a7694754d1
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651076"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse를 업그레이드하여 성능 최적화

Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유

이제 [지원되는 지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 Azure Portal에서 SQL Data Warehouse 계산에 최적화된 Gen2 계층으로 원활하게 업그레이드할 수 있습니다. 지역이 자체 업그레이드를 지원하지 않는 경우 지원되는 지역으로 업그레이드하거나, 지역에서 자체 업그레이드를 사용할 수 있게 될 때까지 기다릴 수 있습니다. 지금 바로 업그레이드하여 최신 세대 Azure 하드웨어와 더 빠른 성능, 더 높은 확장성, 무제한 열 형식 스토리지를 비롯한 향상된 스토리지 아키텍처를 활용하세요. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>적용 대상

이 업그레이드는 [지원되는 지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)의 계산에 최적화된 Gen1 계층 데이터 웨어하우스에 적용됩니다.

## <a name="before-you-begin"></a>시작하기 전에

1. 사용자의 [지역](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)에서 GEN1-GEN2 마이그레이션이 지원되는지 확인합니다. 자동 마이그레이션 날짜를 기록해 둡니다. 자동 프로세스 충돌을 방지하려면 자동 프로세스 시작 날짜 전에 수동 마이그레이션을 진행하도록 계획합니다.
2. 아직 지원되지 않는 지역에 있는 경우 지역이 추가되는지 여부를 계속 확인하거나 [복원을 사용하여 지원되는 지역으로 업그레이드](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal)합니다.
3. 사용자의 지역이 지원되는 [Azure Portal을 통해 업그레이드](#upgrade-in-a-supported-region-using-the-azure-portal)합니다.
4. 데이터 웨어하우스에 대한 **제안된 성능 수준 선택**은 아래에 있는 매핑을 사용하여 계산에 최적화된 Gen1 계층의 현재 성능 수준을 기반으로 합니다.

   | 계산에 최적화된 Gen1 계층 | 계산에 최적화된 Gen2 계층 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> 제안된 성능 수준은 직접 변환이 아닙니다. 예를 들어, DW600에서 DW500c로 변환하는 것이 좋습니다.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Azure Portal을 사용하여 지원되는 지역에서 업그레이드

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Azure portal 통해 Gen2로 Gen1에서 마이그레이션은 영구적입니다. Gen1를 반환 하기 위해 프로세스가 아닙니다.  

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 업그레이드 대상인 계산에 최적화된 Gen1 계층 데이터 웨어하우스가 일시 중지된 경우 [데이터 웨어하우스를 다시 시작](pause-and-resume-compute-portal.md)합니다.

   > [!NOTE]
   > Gen2로 마이그레이션하려면 Azure SQL Data Warehouse를 실행해야 합니다.

2. 몇 분 정도의 가동 중지 시간에 대비합니다. 

3. Compute에 최적화된 Gen1 성능 수준에 대한 코드 참조를 식별하고, 동등한 Compute에 최적화된 Gen2 성능 레벨로 수정합니다. 아래에는 업그레이드 전에 코드 참조를 업데이트해야 는 두 가지 예제입니다.

   원래 Gen1 PowerShell 명령:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   다음으로 수정됨:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName “DW300”이 - RequestedServiceObjectiveName “DW300**c**”로 변경되었습니다.
   >

   원래 Gen1 T-SQL 명령:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   다음으로 수정됨:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = ‘DW300’이 SERVICE_OBJECTIVE = ‘DW300**c**’로 변경되었습니다.

## <a name="start-the-upgrade"></a>업그레이드 시작

1. Azure Portal에서 계산에 최적화된 Gen1 계층 데이터 웨어하우스로 이동합니다. 업그레이드 대상인 계산에 최적화된 Gen1 계층 데이터 웨어하우스가 일시 중지된 경우 [데이터 웨어하우스를 다시 시작](pause-and-resume-compute-portal.md)합니다. 
2. 작업 탭에서 **Gen2로 업그레이드** 카드를 선택합니다.  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > [작업] 탭 아래 **Gen2로 업그레이드** 카드가 표시되지 않으면 구독 유형이 현재 지역으로 제한됩니다.
    > [지원 티켓을 제출](sql-data-warehouse-get-started-create-support-ticket.md)하여 구독을 허용 목록으로 가져옵니다.

3. 업그레이드 전에 워크로드가 실행되고 정지되었는지 확인합니다. 데이터 웨어하우스가 계산에 최적화된 Gen2 계층 데이터 웨어하우스로 다시 온라인 상태가 되기 전에 몇 분 동안 가동 중지 시간이 발생합니다. **업그레이드를 선택합니다**.

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal에서 상태를 확인하여 **업그레이드를 모니터링**합니다.

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   비율 크기 조정 작업(“업그레이드 - 오프라인”)을 통해 업그레이드 프로세스의 첫 단계가 진행됩니다. 여기서는 모든 세션이 종료되며 연결이 삭제됩니다. 

   업그레이드 프로세스의 두 번째 단계는 데이터 마이그레이션(“업그레이드 - 온라인”)입니다. 데이터 마이그레이션은 지속적인 온라인 백그라운드 프로세스입니다. 이 프로세스는 로컬 SSD 캐시를 활용하여 열 형식의 데이터가 이전 스토리지 아키텍처에서 새로운 스토리지 아키텍처로 느리게 이동합니다. 이 시간 동안 데이터 웨어하우스는 쿼리 및 로딩을 위해 온라인 상태가 됩니다. 데이터는 마이그레이션 여부에 관계 없이 쿼리에 사용할 수 있습니다. 데이터 마이그레이션은 데이터 크기, 성능 수준 및 columnstore 세그먼트의 수에 따라 다양한 속도로 발생합니다. 

5. **선택적 권장 사항:** 크기 조정 작업이 완료되면 데이터 마이그레이션 백그라운드 프로세스의 속도를 높일 수 있습니다. 더 큰 SLO 및 리소스 클래스에서 쿼리하는 모든 기본 columnstore 테이블에서 [Alter Index rebuild](sql-data-warehouse-tables-index.md)를 실행하여 데이터를 강제로 이동할 수 있습니다. 이 작업은 지속적인 백그라운드 프로세스와 비교할 때 **오프라인** 방식이므로, 테이블의 크기와 수에 따라 완료하는 데 몇 시간이 걸릴 수 있습니다. 그러나 완료되면 고품질의 행 그룹으로 인해 스토리지 아키텍처가 새롭게 개선되므로 데이터 마이그레이션이 훨씬 더 빨라집니다.
 
> [!NOTE]
> Alter Index rebuild는 오프라인 작업이며, 다시 빌드가 완료될 때까지 테이블을 사용할 수 없습니다.

다음 쿼리는 데이터 마이그레이션을 더 신속히 처리하기 위해 필요한 Alter Index Rebuild 명령을 생성합니다.

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON [' 
       + Schema_name(tbl.schema_id) + '].[' 
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE 
                                                         WHEN ( 
                                                     (SELECT Count(*) 
                                                      FROM   sys.partitions 
                                                             part2 
                                                      WHERE  part2.index_id 
                                                             = idx.index_id 
                                                             AND 
                                                     idx.object_id = 
                                                     part2.object_id) 
                                                     > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              ELSE '' 
                                                       END ) + '; SELECT ''[' + 
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' + 
              Object_name(idx.object_id) + '] ' + ( 
              CASE 
                WHEN ( (SELECT Count(*) 
                        FROM   sys.partitions 
                               part2 
                        WHERE 
                     part2.index_id = 
                     idx.index_id 
                     AND idx.object_id 
                         = part2.object_id) > 1 ) THEN 
              ' PARTITION = ' 
              + Cast(part.partition_number AS NVARCHAR(256)) 
              + ' completed'';' 
              ELSE ' completed'';' 
                                                    END ) 
FROM   sys.indexes idx 
       INNER JOIN sys.tables tbl 
               ON idx.object_id = tbl.object_id 
       LEFT OUTER JOIN sys.partitions part 
                    ON idx.index_id = part.index_id 
                       AND idx.object_id = part.object_id 
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE'; 
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Azure Portal 통한 복원으로 Azure 지역에서 업그레이드

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Azure Portal을 사용하여 사용자 정의 복원 지점 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 복원 지점을 만들려는 SQL 데이터 웨어하우스로 이동합니다.

3. 개요 섹션 위쪽에서 **+새 복원 지점**을 선택합니다.

    ![새 복원 지점](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. 복원 지점에 대한 이름을 지정합니다.

    ![복원 지점에 대한 이름](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Azure Portal을 사용하여 활성 또는 일시 중지된 데이터베이스 복원

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 복원하려는 SQL 데이터 웨어하우스로 이동합니다.
3. 개요 섹션 위쪽에서 **복원**을 선택합니다.

    ![ 복원 개요](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. **자동 복원 지점** 또는 **사용자 정의 복원 지점** 중 하나를 선택합니다.

    ![자동 복원 지점](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. 사용자 정의 복원 지점의 경우 **복원 지점을 선택**하거나 **새 사용자 정의 복원 지점을 만듭니다**. Gen2 지원 지리적 지역에서 서버를 선택합니다. 

    ![사용자 정의 복원 지점](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>PowerShell을 사용하여 Azure 지역에서 복원

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

데이터베이스를 복구하려면 [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) cmdlet을 사용합니다.

> [!NOTE]
> 지역 복원을 Gen2로 수행할 수 있습니다! 이렇게 하려면 Gen2 ServiceObjectiveName(예: DW1000**c**)을 선택적 매개 변수로 지정하세요.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 복구하려는 데이터베이스를 가져옵니다.
5. Gen2 ServiceObjectiveName을 지정하여 데이터베이스에 대한 복구 요청을 만듭니다.
6. 지역에서 복원된 데이터베이스의 상태를 확인합니다.

```Powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> 복원이 완료된 후에 데이터베이스를 구성하려면 [복구 후 데이터베이스 구성](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)을 참조하세요.

원본 데이터베이스가 TDE를 사용할 수 있는 경우 복구된 데이터베이스도 TDE를 사용할 수 있습니다.


데이터 웨어하우스에 문제가 발생하는 경우 [지원 요청](sql-data-warehouse-get-started-create-support-ticket.md)을 만들고 가능한 원인으로 "Gen2 업그레이드"를 참조합니다.

## <a name="next-steps"></a>다음 단계

업그레이드한 데이터 웨어하우스가 온라인 상태입니다. 향상된 아키텍처를 이용하려면 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
