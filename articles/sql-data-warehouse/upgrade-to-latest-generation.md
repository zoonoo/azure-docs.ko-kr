---
title: 최신 세대 Azure SQL Data Warehouse로 업그레이드 | Microsoft Docs
description: Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 11/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1e2f1a3c46c9d343c305292a217fff5750f442fa
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682557"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>SQL Data Warehouse를 업그레이드하여 성능 최적화
Azure SQL Data Warehouse를 최신 세대 Azure 하드웨어와 저장소 아키텍처로 업그레이드합니다.

## <a name="why-upgrade"></a>업그레이드가 필요한 이유
이제 Azure Portal에서 SQL Data Warehouse 계산에 최적화된 Gen2 계층으로 원활하게 업그레이드할 수 있습니다. 계산에 최적화된 Gen1 계층 데이터 웨어하우스가 있는 경우 업그레이드하는 것이 좋습니다. 업그레이드하여 최신 버전의 Azure 하드웨어 및 향상된 저장소 아키텍처를 사용할 수 있습니다. 더 빠른 성능, 더 높은 확장성 및 제한 없는 열 형식의 저장소를 활용할 수 있습니다. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>적용 대상
이 업그레이드는 계산에 최적화된 Gen1 계층 데이터 웨어하우스에 적용됩니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="before-you-begin"></a>시작하기 전에
> [!NOTE]
> 기존의 계산에 최적화된 Gen1 계층 데이터 웨어하우스가 계산 최적화된 Gen2 계층이 제공되는 지역에 없는 경우 PowerShell을 통해 지원되는 지역으로 [지역 복원](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)을 수행할 수 있습니다.
>
> 

1. 업그레이드 대상인 계산에 최적화된 Gen1 계층 데이터 웨어하우스가 일시 중지된 경우 [데이터 웨어하우스를 다시 시작](pause-and-resume-compute-portal.md)합니다.

2. 몇 분 정도의 가동 중지 시간에 대비합니다. 

3. Compute에 최적화된 Gen1 성능 수준에 대한 코드 참조를 식별하고, 동등한 Compute에 최적화된 Gen2 성능 레벨로 수정합니다. 아래에는 업그레이드 전에 코드 참조를 업데이트해야 는 두 가지 예제입니다.

   원래 Gen1 PowerShell 명령:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   다음으로 수정됨:

   ```powershell
   Set-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
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

1. Azure Portal에서 Compute에 최적화된 Gen1 계층 데이터 웨어하우스로 이동하여 작업 탭에 있는 **Gen2로 업그레이드** 카드를 클릭합니다.  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > [작업] 탭 아래 **Gen2로 업그레이드** 카드가 표시되지 않으면 구독 유형이 현재 지역으로 제한됩니다.
    > [지원 티켓을 제출](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket)하여 구독을 허용 목록으로 가져옵니다.

2. 기본적으로 데이터 웨어하우스에 대한 **제안된 성능 수준 선택**은 아래에 있는 매핑을 사용하여 계산에 최적화된 Gen1 계층의 현재 성능 수준을 기반으로 합니다.

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

3. 업그레이드 전에 워크로드가 실행되고 정지되었는지 확인합니다. 데이터 웨어하우스가 계산에 최적화된 Gen2 계층 데이터 웨어하우스로 다시 온라인 상태가 되기 전에 몇 분 동안 가동 중지 시간이 발생합니다. **업그레이드를 클릭합니다**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. Azure Portal에서 상태를 확인하여 **업그레이드를 모니터링**합니다.

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   비율 크기 조정 작업(“업그레이드 - 오프라인”)을 통해 업그레이드 프로세스의 첫 단계가 진행됩니다. 여기서는 모든 세션이 종료되며 연결이 삭제됩니다. 

   업그레이드 프로세스의 두 번째 단계는 데이터 마이그레이션(“업그레이드 - 온라인”)입니다. 데이터 마이그레이션은 지속적인 온라인 백그라운드 프로세스로, 로컬 SSD 캐시를 활용하여 열 형식의 데이터가 이전 저장소 아키텍처에서 새로운 저장소 아키텍처로 느리게 이동합니다. 이 시간 동안 데이터 웨어하우스는 쿼리 및 로딩을 위해 온라인 상태가 됩니다. 모든 데이터는 마이그레이션 여부에 관계 없이 쿼리에 사용할 수 있습니다. 데이터 마이그레이션은 데이터 크기, 성능 수준 및 columnstore 세그먼트의 수에 따라 다양한 속도로 발생합니다. 

5. **선택적 권장 사항:** 데이터 마이그레이션 백그라운드 프로세스를 더 신속히 처리하기 위해 더 큰 SLO 및 리소스 클래스에서 쿼리하는 모든 기본 columnstore 테이블에서 [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index)를 실행하여 데이터 이동을 즉시 실행할 수 있습니다. 이 작업은 테이블 수와 크기에 따라 완료하는 데 몇 시간이 소요될 수 있는 지속적인 백그라운드 프로세스와 달리 **오프라인** 상태이지만 데이터 마이그레이션은 훨씬 더 빨라집니다. 고품질 행 그룹으로 완료되면 새롭게 향상된 저장소 아키텍처의 장점을 충분히 활용할 수 있습니다. 

다음 쿼리는 데이터 마이그레이션 프로세스를 더 신속히 처리하기 위해 필요한 Alter Index Rebuild 명령을 생성합니다.

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



## <a name="next-steps"></a>다음 단계
업그레이드한 데이터 웨어하우스가 온라인 상태입니다. 향상된 아키텍처를 이용하려면 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요.
