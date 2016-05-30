<properties
   pageTitle="Azure SQL 데이터 웨어하우스에서 데이터베이스 복원(개요) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 데이터베이스를 복구하기 위한 데이터베이스 복원 옵션 개요입니다."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/05/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Azure SQL 데이터 웨어하우스에서 데이터베이스 복원(개요)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-manage-database-restore.md)
- [포털](sql-data-warehouse-manage-database-restore-portal.md)
- [PowerShell](sql-data-warehouse-manage-database-restore-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-database-restore-rest-api.md)

Azure SQL 데이터 웨어하우스에서 데이터베이스를 복원하기 위한 옵션을 설명합니다. 여기에는 라이브 데이터베이스, 삭제된 데이터베이스 및 액세스할 수 없는 데이터베이스의 복원이 포함됩니다. 라이브 및 삭제된 데이터베이스는 동일한 데이터 센터의 스냅숏을 사용하여 복원됩니다. 액세스할 수 없는 데이터베이스는 가동 중단으로 인해 오프라인 상태가 될 수 있습니다. 이 경우 다른 지리적 위치에 있는 데이터 센터에서 복원합니다.


## 복구 시나리오

**인프라 오류로부터 복구:** 디스크 오류 등의 인프라 문제를 복구하는 데 이 시나리오를 참조합니다. 고객은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 확인하고자 합니다.

**사용자 오류에서 복구:** 이 시나리오에서는 의도 하지 않거나 부수적 데이터 손상 또는 삭제로부터의 복구를 가리킵니다. 사용자가 실수로 또는 우연하게 데이터를 수정하거나 삭제한 경우, 고객은 이전 시점으로 데이터베이스를 복원하여 비즈니스 연속성을 보장하고자 합니다.

**재해에서 복구(DR):** 이 시나리오는 주요 재해로부터의 복구를 가리킵니다. 자연 재해 또는 지역 가동 중단과 같은 중단 이벤트로 데이터베이스를 사용할 수 없는 시나리오에서는 고객이 비즈니스 작업을 계속하기 위해 다른 지역에서 데이터베이스를 복구하려고 합니다.

## 스냅숏 정책

[AZURE.INCLUDE [SQL 데이터 웨어하우스 백업 보존 정책](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## 데이터베이스 복원 기능

SQL 데이터 웨어하우스가 데이터베이스의 안정성을 개선하고 앞에서 언급한 시나리오에서의 복구 기능 및 연속 작업을 허용하는 방법을 살펴보겠습니다.


### 데이터 중복

SQL 데이터 웨어하우스는 지역 중복 Azure 저장소(RA-GRS)에 모든 데이터를 저장합니다. 지역 중복 저장소는 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다. 기본 및 보조 지역에서 데이터는 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 각각 세 번씩 복제됩니다. 이렇게 하면 영역 중 하나를 사용할 수 없게 하는 완전한 지역 가동 중단 또는 재해 경우더라도 데이터를 지속적으로 사용할 수 있습니다. 읽기 액세스 지역 중복 저장소에 대한 자세한 정보는 [Azure 저장소 중복 옵션][]을 참조하세요.

### 데이터베이스 복원

데이터베이스 복원은 데이터베이스를 이전 시점으로 복원하도록 설계되었습니다. Azure SQL 데이터 웨어하우스 서비스는 적어도 8시간마다 자동 저장소 스냅숏을 사용하여 모든 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 이러한 스냅숏은 RA-GRS Azure 저장소에 저장되므로 기본적으로 지역 중복됩니다. 자동 스냅숏 및 복원 기능은 별도의 관리 방법 없이 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호합니다. 데이터베이스를 복원하는 방법에 대한 자세한 내용은 [데이터베이스 복원 작업][]을 참조하세요.

### 지역 복원

지역 복원은 중단 이벤트로 인해 사용할 수 없는 경우 데이터베이스를 복구하도록 설계되었습니다. 백업이 지리적으로 중복되기 때문에 가동 중단으로 인해 데이터베이스에 액세스할 수 없는 경우더라도 데이터베이스를 복구하는 데 사용할 수 있습니다. Azure 지역의 모든 서버에서 복원된 데이터베이스를 만들 수 있습니다. 가동 중단에서 복구하는 것 외에도 다른 서버나 지역에 데이터베이스 복사 또는 마이그레이션과 같은 다른 시나리오에 대해서도 지역 복원을 사용할 수 있습니다.

**복구를 시작해야 하는 시기** 복구 작업을 수행하려면 복구 시 SQL 연결 문자열을 변경해야 하며 변경 시 데이터가 영구적으로 손상될 수도 있습니다. 따라서 중단이 응용 프로그램의 RTO보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 다음 데이터 요소를 사용하여 복구가 보장되는지 확인합니다.

- 데이터베이스에 대한 영구 연결 실패
- Azure 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.


## 다음 단계
기타 중요한 관리 작업은 [관리 개요][]를 참조하세요.

<!--Image references-->

<!--Article references-->
[Azure 저장소 중복 옵션]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[관리 개요]: sql-data-warehouse-overview-management.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->