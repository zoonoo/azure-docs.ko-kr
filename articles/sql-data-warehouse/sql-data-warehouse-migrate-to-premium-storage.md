---
title: "기존 Azure SQL Data Warehouse를 Premium Storage로 마이그레이션 | Microsoft Docs"
description: "기존 SQL 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션하기 위한 지침"
services: sql-data-warehouse
documentationcenter: NA
author: happynicolle
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/29/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: b676630e44c49c2ab4006f04408b01cc90c4dc28
ms.openlocfilehash: ef20cf90b54c9b28c70341d7545bb55474feb39f


---
# <a name="migration-to-premium-storage-details"></a>프리미엄 저장소 세부 정보로 마이그레이션
SQL Data Warehouse는 최근에 도입된 [큰 성능 예측 가능성을 위한 Premium Storage][Premium Storage for greater performance predictability]입니다.  이제 현재 표준 저장소에 있는 기존 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션할 준비가 되었습니다.  자동 마이그레이션이 발생하는 방법 및 시기, 가동 중지가 발생하는 시간을 제어하려는 경우 자체 마이그레이션하는 방법에 대한 자세한 내용은 계속 읽어 보세요.

둘 이상의 데이터 웨어하우스가 있는 경우 아래 [자동 마이그레이션 일정][automatic migration schedule]을 사용하여 마이그레이션되는 시점을 확인하세요.

## <a name="determine-storage-type"></a>저장소 유형 결정
아래 날짜 전에 DW를 만든 경우 현재 표준 저장소를 사용하고 있습니다.

| **지역** | **이 날짜 이전에 만든 DW** |
|:--- |:--- |
| 오스트레일리아 동부 |프리미엄 저장소를 아직 사용할 수 없음 |
| 중국 동부 |2016년 11월 1일 |
| 중국 북부 |2016년 11월 1일 |
| 독일 중부 |2016년 11월 1일 |
| 독일 북동부 |2016년 11월 1일 |
| 인도 서부 |프리미엄 저장소를 아직 사용할 수 없음 |
| 일본 서부 |프리미엄 저장소를 아직 사용할 수 없음 |
| 미국 중북부 |2016년 11월 10일 |

## <a name="automatic-migration-details"></a>자동 마이그레이션 세부 정보
기본적으로 아래 [자동 마이그레이션 일정][automatic migration schedule] 동안 지역 현지 시간으로 오후 6시~오전 6시 동안 데이터를 마이그레이션합니다.  마이그레이션하는 동안 기존 데이터 웨어하우스를 사용할 수 없습니다.  마이그레이션은 데이터 웨어하우스당 저장소의 TB당 약 1시간이 소요될 것으로 예상합니다.  또한 자동 마이그레이션의 어느 부분에서도 청구되지 않도록 합니다.

> [!NOTE]
> 마이그레이션하는 동안 기존 데이터 웨어하우스를 사용할 수 없습니다.  마이그레이션이 완료되면 데이터 웨어하우스는 다시 온라인 상태가 됩니다.
>
>

아래 자세한 내용은 마이그레이션을 완료하도록 Microsoft에서 사용자 대신 수행하는 단계이며 사용자의 개입은 필요하지 않습니다.  이 예제에서는 표준 저장소의 기존 DW가 현재 "MyDW"라는 이름이라고 가정합니다.

1. Microsoft는 "MyDW"의 이름을 "MyDW_DO_NOT_USE_[타임스탬프]"로 바꿉니다.
2. Microsoft는 "MyDW_DO_NOT_USE_[타임스탬프]"를 일시 중지합니다.  이 시간 동안 백업이 수행됩니다.  이 과정에서 문제가 발생한 경우 여러 일시 중지/다시 시작이 표시될 수 있습니다.
3. Microsoft는 2단계에서 수행된 백업에서 프리미엄 저장소에 "MyDW"라는 새 DW를 만듭니다.  복원이 완료될 때까지 "MyDW"는 나타나지 않습니다.
4. 복원이 완료되면 "MyDW"는 동일한 DWU 및 마이그레이션 전의 일시 중지 또는 활성 상태를 반환합니다.
5. 마이그레이션이 완료되면 Microsoft는 "MyDW_DO_NOT_USE_[타임스탬프]"를 삭제합니다.

> [!NOTE]
> 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
>
> * 데이터베이스 수준에서 감사를 다시 사용하도록 설정해야 합니다.
> * **데이터베이스** 수준에서 방화벽 규칙을 다시 추가해야 합니다.  **서버** 수준에서 방화벽 규칙은 영향을 받지 않습니다.
>
>

### <a name="automatic-migration-schedule"></a>자동 마이그레이션 일정
자동 마이그레이션은 다음 중단 일정 동안 오후 6시 - 오전 6시(지역별 현지 시간)에 발생합니다.

| **지역** | **예상된 시작 날짜** | **예상된 종료 날짜** |
|:--- |:--- |:--- |
| 오스트레일리아 동부 |아직 결정되지 않음 |아직 결정되지 않음 |
| 중국 동부 |2017년 1월 9일 |2017년 1월 13일 |
| 중국 북부 |2017년 1월 9일 |2017년 1월 13일 |
| 독일 중부 |2017년 1월 9일 |2017년 1월 13일 |
| 독일 북동부 |2017년 1월 9일 |2017년 1월 13일 |
| 인도 서부 |아직 결정되지 않음 |아직 결정되지 않음 |
| 일본 서부 |아직 결정되지 않음 |아직 결정되지 않음 |
| 미국 중북부 |2017년 1월 9일 |2017년 1월 13일 |

## <a name="self-migration-to-premium-storage"></a>프리미엄 저장소로 자체 마이그레이션
가동 중지가 발생하는 시간을 제어하려는 경우 아래 단계를 사용하여 표준 저장소의 기존 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션할 수 있습니다.  자체 마이그레이션하도록 선택하는 경우 충돌을 야기하는 자동 마이그레이션의 모든 위험을 방지하도록 해당 지역에서 자동 마이그레이션이 시작되기 전에 자체 마이그레이션을 완료해야 합니다([자동 마이그레이션 일정][automatic migration schedule] 참조).

### <a name="self-migration-instructions"></a>자체 마이그레이션 지침
작동 중단 시간을 제어하려는 경우 백업/복원을 사용하여 데이터 웨어하우스를 자체 마이그레이션할 수 있습니다.  마이그레이션의 복원 부분은 DW당 저장소의 TB당 약 1시간 정도가 걸릴 예정입니다.  마이그레이션이 완료된 후 동일한 이름을 유지하려는 경우 [마이그레이션 중에 이름을 바꾸기 위한 단계][steps to rename during migration]를 따르세요.

1. 자동 백업을 수행하는 DW [일시 중지][Pause]
2. 가장 최근의 스냅숏에서 [복원][Restore]
3. 표준 저장소의 기존 DW를 삭제합니다. **이 단계의 수행에 실패하면 두 DW에 대해 청구됩니다.**

> [!NOTE]
> 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
>
> * 데이터베이스 수준에서 감사를 다시 사용하도록 설정해야 합니다.
> * **데이터베이스** 수준에서 방화벽 규칙을 다시 추가해야 합니다.  **서버** 수준에서 방화벽 규칙은 영향을 받지 않습니다.
>
>

#### <a name="optional-steps-to-rename-during-migration"></a>선택 사항: 마이그레이션하는 동안 이름을 바꾸는 단계
동일한 논리 서버의 두 개의 데이터베이스는 동일한 이름을 가질 수 없습니다. 이제 SQL 데이터 웨어하우스는 DW 이름 바꾸기 기능을 지원합니다.

이 예제에서는 표준 저장소의 기존 DW가 현재 "MyDW"라는 이름이라고 가정합니다.

1. "MyDW_BeforeMigration" 등과 같은 항목 다음에 ALTER DATABASE 명령을 사용하여 "MyDW" 이름을 바꿉니다.  이 명령은 기존 트랜잭션을 모두 삭제하며, master 데이터베이스에서 수행해야 성공적으로 진행될 수 있습니다.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. 자동 백업을 수행하는 "MyDW_BeforeMigration" [일시 중지][Pause]
3. 가장 최근의 스냅숏에서 이전 이름(예: "MyDW")을 갖는 새 데이터베이스로 [복원][Restore]
4. "MyDW_BeforeMigration"을 삭제합니다.  **이 단계의 수행에 실패하면 두 DW에 대해 청구됩니다.**

> [!NOTE]
> 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
>
> * 데이터베이스 수준에서 감사를 다시 사용하도록 설정해야 합니다.
> * **데이터베이스** 수준에서 방화벽 규칙을 다시 추가해야 합니다.  **서버** 수준에서 방화벽 규칙은 영향을 받지 않습니다.
>
>

## <a name="next-steps"></a>다음 단계
프리미엄 저장소로 변경하여 데이터 웨어하우스의 기반 아키텍처에서 데이터베이스 blob 파일 수도 증가시켰습니다.  이 변경을 통해 성능을 최대한 개선하려면 다음 스크립트를 사용하여 클러스터형 columnstore 인덱스를 다시 작성하는 것이 좋습니다.  아래 스크립트는 일부 기존 데이터를 추가 Blob에 강제로 적용하여 작동합니다.  아무 작업도 하지 않으면 자연스럽게 시간이 지나면서 데이터 웨어하우스 테이블에 더 많은 데이터를 로드함에 따라 데이터가 재배포됩니다.

**필수 조건:**

1. 데이터 웨어하우스를 1,000 DWU 이상으로 실행해야 합니다([계산 능력 크기 조정][scale compute power] 참조).
2. 스크립트를 실행하는 사용자가 [mediumrc 역할][mediumrc role] 이상이어야 합니다.
   1. 이 역할에 사용자를 추가하려면 다음을 실행합니다.
      1. ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create Table to control Index Rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute Index Rebuilds.  If script fails, the below can be rerun to restart where last left off
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Cleanup Table Created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

데이터 웨어하우스에 문제가 발생하는 경우 [지원 티켓을 만들고][create a support ticket] 가능한 원인으로 "Premium Storage로 마이그레이션"을 참조하세요.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO5-->


