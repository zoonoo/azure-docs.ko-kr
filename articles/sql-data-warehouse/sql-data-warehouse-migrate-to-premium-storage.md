<properties
   pageTitle="기존 Azure SQL 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션 | Microsoft Azure"
   description="기존 SQL 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션하기 위한 지침"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/05/2016"
   ms.author="nicw;barbkess;sonyama"/>

# 프리미엄 저장소 세부 정보로 마이그레이션
SQL 데이터 웨어하우스는 최근에 도입된 [큰 성능 예측 가능성을 위한 프리미엄 저장소][]입니다. 이제 현재 표준 저장소에 있는 기존 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션할 준비가 되었습니다. 자동 마이그레이션이 발생하는 방법 및 시기, 가동 중지가 발생하는 시간을 제어하려는 경우 자체 마이그레이션하는 방법에 대한 자세한 내용은 계속 읽어 보세요.

둘 이상의 데이터 웨어하우스가 있는 경우 아래 [자동 마이그레이션 일정][]을 사용하여 마이그레이션되는 시점을 확인하세요.

## 저장소 유형 결정
아래 날짜 전에 DW를 만든 경우 현재 표준 저장소를 사용하고 있습니다. 이 저장소 외에, 마이그레이션되는 표준 저장소의 각 데이터 웨어하우스의 경우 [Azure 포털][]의 데이터 웨어하우스 블레이드에 "*프리미엄 저장소로의 예정된 업그레이드를 위해 가동 중단이 필요합니다. 자세한 정보 ->*"라는 알림이 표시됩니다.

| **지역** | **이 날짜 이전에 만든 DW** |
| :------------------ | :-------------------------------- |
| 오스트레일리아 동부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 오스트레일리아 남동부 | 2016년 8월 5일 |
| 브라질 남부 | 2016년 8월 5일 |
| 캐나다 중부 | 2016년 5월 25일 |
| 캐나다 동부 | 2016년 5월 26일 |
| 미국 중부 | 2016년 5월 26일 |
| 중국 동부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 중국 북부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 동아시아 | 2016년 5월 25일 |
| 미국 동부 | 2016년 5월 26일 |
| 미국 동부2 | 2016년 5월 27일 |
| 인도 중부 | 2016년 5월 27일 |
| 인도 남부 | 2016년 5월 26일 |
| 인도 서부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 일본 동부 | 2016년 8월 5일 |
| 일본 서부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 미국 중북부 | 프리미엄 저장소를 아직 사용할 수 없음 |
| 북유럽 | 2016년 8월 5일 |
| 미국 중남부 | 2016년 5월 27일 |
| 동남아시아 | 2016년 5월 24일 |
| 서유럽 | 2016년 5월 25일 |
| 미국 서부 | 2016년 5월 26일 |

## 자동 마이그레이션 세부 정보
기본적으로 아래 [자동 마이그레이션 일정][] 동안 어느 시점에서 지역 현지 시간으로 오후 6시~오전 6시 동안 데이터를 마이그레이션합니다. 마이그레이션하는 동안 기존 데이터 웨어하우스를 사용할 수 없습니다. 마이그레이션은 데이터 웨어하우스당 저장소의 TB당 약 1시간이 소요될 것으로 예상합니다. 또한 마이그레이션의 어느 부분에서도 청구되지 않도록 합니다.

> [AZURE.NOTE] 마이그레이션하는 동안 기존 데이터 웨어하우스를 사용할 수 없습니다. 마이그레이션이 완료되면 데이터 웨어하우스는 다시 온라인 상태가 됩니다.

아래 자세한 내용은 마이그레이션을 완료하도록 Microsoft에서 사용자 대신 수행하는 단계이며 사용자의 개입은 필요하지 않습니다. 이 예의 목적에 맞게 표준 저장소의 기존 DW가 현재 "MyDW"라는 이름이라고 가정합니다.

1.	Microsoft는 "MyDW"의 이름을 "MyDW\_DO\_NOT\_USE\_[타임스탬프]"로 바꿉니다.
2.	Microsoft는 "MyDW\_DO\_NOT\_USE\_[타임스탬프]"를 일시 중지합니다. 이 시간 동안 Microsoft는 백업이 수행됩니다. 이 과정에서 문제가 발생한 경우 여러 일시 중지/다시 시작이 표시될 수 있습니다.
3.	Microsoft는 위의 2단계에서 수행된 백업에서 프리미엄 저장소에 "MyDW"라는 새 DW를 만듭니다. 복원이 완료될 때까지 "MyDW"는 나타나지 않습니다.
4.	복원이 완료되면 "MyDW"는 동일한 수준의 DWU 및 마이그레이션 전의 일시 중지 또는 활성 상태를 반환합니다.
5.	마이그레이션이 완료되면 Microsoft는 "MyDW\_DO\_NOT\_USE\_[타임스탬프]"를 삭제합니다.
	
> [AZURE.NOTE] 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

### 자동 마이그레이션 일정
자동 마이그레이션은 아래에 나열된 중단 일정 동안 어느 시점에서 오후 6시-오전 6시(해당 지역에 대한 현지 시간)에 발생합니다.

| **지역** | **예상된 시작 날짜** | **예상된 종료 날짜** |
| :------------------ | :--------------------------- | :--------------------------- |
| 오스트레일리아 동부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 오스트레일리아 남동부 | 2016년 8월 10일 | 2016년 8월 24일 |
| 브라질 남부 | 2016년 8월 10일 | 2016년 8월 24일 |
| 캐나다 중부 | 2016년 6월 23일 | 2016년 7월 1일 |
| 캐나다 동부 | 2016년 6월 23일 | 2016년 7월 1일 |
| 미국 중부 | 2016년 6월 23일 | 2016년 7월 4일 |
| 중국 동부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 중국 북부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 동아시아 | 2016년 6월 23일 | 2016년 7월 1일 |
| 미국 동부 | 2016년 6월 23일 | 2016년 7월 11일 |
| 미국 동부2 | 2016년 6월 23일 | 2016년 7월 8일 |
| 인도 중부 | 2016년 6월 23일 | 2016년 7월 1일 |
| 인도 남부 | 2016년 6월 23일 | 2016년 7월 1일 |
| 인도 서부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 일본 동부 | 2016년 8월 10일 | 2016년 8월 24일 |
| 일본 서부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 미국 중북부 | 아직 결정되지 않음 | 아직 결정되지 않음 |
| 북유럽 | 2016년 8월 10일 | 2016년 8월 24일 |
| 미국 중남부 | 2016년 6월 23일 | 2016년 7월 2일 |
| 동남아시아 | 2016년 6월 23일 | 2016년 7월 1일 |
| 서유럽 | 2016년 6월 23일 | 2016년 7월 8일 |
| 미국 서부 | 2016년 6월 23일 | 2016년 7월 7일 |

## 프리미엄 저장소로 자체 마이그레이션
가동 중지가 발생하는 시간을 제어하려는 경우 아래 단계를 사용하여 표준 저장소의 기존 데이터 웨어하우스를 프리미엄 저장소로 마이그레이션할 수 있습니다. 자체 마이그레이션하도록 선택하는 경우 충돌을 야기하는 자동 마이그레이션의 모든 위험을 방지하도록 해당 지역에서 자동 마이그레이션이 시작하기 전에 자체 마이그레이션을 완료해야 합니다([자동 마이그레이션 일정][] 참조).

### 자체 마이그레이션 지침
작동 중단 시간을 제어하려는 경우 백업/복원을 사용하여 데이터 웨어하우스를 자체 마이그레이션할 수 있습니다. 마이그레이션의 복원 부분은 DW당 저장소의 TB당 약 1시간 정도가 걸릴 예정입니다. 마이그레이션이 완료된 후 동일한 이름을 유지하려는 경우 [마이그레이션 중에 이름을 바꾸기 위한 단계][]는 아래 단계를 따르세요.

1.	자동 백업을 수행하는 DW [일시 중지][]
2.	가장 최근의 스냅숏에서 [복원][]
3.	표준 저장소의 기존 DW를 삭제합니다. **이 단계의 수행에 실패하면 두 DW에 대해 청구됩니다.**

> [AZURE.NOTE] 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

#### 선택 사항: 마이그레이션하는 동안 이름을 바꾸는 단계 
동일한 논리 서버의 두 개의 데이터베이스는 동일한 이름을 가질 수 없습니다. 이제 SQL 데이터 웨어하우스는 DW 이름 바꾸기 기능을 지원합니다.

이 예의 목적에 맞게 표준 저장소의 기존 DW가 현재 "MyDW"라는 이름이라고 가정합니다.

1.	"MyDW\_BeforeMigration" 등과 같은 항목 다음에 ALTER DATABASE 명령을 사용하여 "MyDW" 이름을 바꿉니다. 이 작업은 기존 트랜잭션을 모두 삭제하며, master 데이터베이스에서 수행해야 성공적으로 진행될 수 있습니다.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	자동 백업을 수행하는 "MyDW\_BeforeMigration" [일시 중지][]
3.	가장 최근의 스냅숏에서 이전 이름(예: "MyDW")을 갖는 새 데이터베이스로 [복원][]
4.	"MyDW\_BeforeMigration"을 삭제합니다. **이 단계의 수행에 실패하면 두 DW에 대해 청구됩니다.**

> [AZURE.NOTE] 이러한 설정은 마이그레이션의 일환으로 수행하지 않습니다.
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

## 다음 단계
프리미엄 저장소로 변경하여 데이터 웨어하우스의 기반 아키텍처에서 데이터베이스 blob 파일 수도 증가시켰습니다. 성능 문제가 발생하는 경우 아래 스크립트를 사용하여 클러스터형 Columnstore를 다시 빌드하는 것이 좋습니다. 그러면 일부 기존 데이터가 추가 blob에 적용됩니다. 아무 작업도 하지 않으면 자연스럽게 시간이 지나면서 데이터 웨어하우스 테이블에 더 많은 데이터를 로드함에 따라 데이터가 재배포됩니다.

**필수 조건:**

1.	데이터 웨어하우스를 1,000 DWU 이상으로 실행해야 함([계산 능력 크기 조정][])
2.	스크립트를 실행하는 사용자가 [mediumrc 역할][] 이상이어야 함
	1.	이 역할에 사용자를 추가하려면 다음을 실행합니다.
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- 1단계: 인덱스 다시 작성을 제어하기 위한 테이블 만들기
-- Mediumrc 이상의 사용자로 실행
--------------------------------------------------------------------------------
create table sql\_statements WITH (distribution = round\_robin) as select 'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement, row\_number() over (order by s.name, t.name) as sequence from sys.schemas s inner join sys.tables t on s.schema\_id = t.schema\_id where is\_external = 0 ; go
 
--------------------------------------------------------------------------------
-- 2단계: 인덱스 다시 작성을 실행합니다. 스크립트가 실패하는 경우 아래 명령을 다시 실행하여 마지막 중단했던 지점에서 다시 시작할 수 있음
-- Mediumrc 이상의 사용자로 실행
--------------------------------------------------------------------------------

declare @nbr\_statements int = (select count(*) from sql\_statements) declare @i int = 1 while(@i <= @nbr\_statements) begin declare @statement nvarchar(1000)= (select statement from sql\_statements where sequence = @i) print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement exec (@statement) delete from sql\_statements where sequence = @i set @i += 1 end;
go
-------------------------------------------------------------------------------
-3단계: 1단계에서 만든 테이블 정리
--------------------------------------------------------------------------------
drop table sql\_statements; go ````

데이터 웨어하우스에 문제가 발생하는 경우 [지원 티켓을 만들고][] 가능한 원인으로 "프리미엄 저장소로 마이그레이션"을 참조하세요.

<!--Image references-->

<!--Article references-->
[자동 마이그레이션 일정]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[지원 티켓을 만들고]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: ./best-practices-availability-paired-regions.md
[main documentation site]: ./services/sql-data-warehouse.md
[일시 중지]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[복원]: ./sql-data-warehouse-manage-database-restore-portal.md
[마이그레이션 중에 이름을 바꾸기 위한 단계]: #optional-steps-to-rename-during-migration
[계산 능력 크기 조정]: ./sql-data-warehouse-manage-compute-portal/#scale-compute-power
[mediumrc 역할]: ./sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[큰 성능 예측 가능성을 위한 프리미엄 저장소]: https://azure.microsoft.com/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure 포털]: https://portal.azure.com

<!---HONumber=AcomDC_0810_2016-->