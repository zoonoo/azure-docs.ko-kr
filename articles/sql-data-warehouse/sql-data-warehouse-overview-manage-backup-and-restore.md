<properties
   pageTitle="Azure SQL 데이터 웨어하우스에서 데이터베이스 백업 및 복원(개요) | Microsoft Azure"
   description="Azure SQL 데이터 웨어하우스의 데이터베이스 복구를 위한 백업 및 복원 옵션 개요입니다."
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


# Azure SQL 데이터 웨어하우스에서 데이터베이스 백업 및 복원(개요)

> [AZURE.SELECTOR]
- [개요](sql-data-warehouse-overview-manage-backup-and-restore.md)
- [포털](sql-data-warehouse-manage-backup-and-restore-tasks-portal.md)
- [PowerShell](sql-data-warehouse-manage-backup-and-restore-tasks-powershell.md)
- [REST (영문)](sql-data-warehouse-manage-backup-and-restore-tasks-rest-api.md)

이 문서는 SQL 데이터 웨어하우스를 사용한 비즈니스 연속성 및 재해 복구 계획의 기본 사항을 안내합니다. 데이터 웨어하우스는 기업에 대한 정보의 중앙 저장소이며 조직의 모든 수준에서의 비즈니스 인텔리전스 및 분석을 위한 일상적인 작업에 있어 중요한 역할을 담당합니다. 따라서 필수적으로 데이터 웨어하우스가 안정적이며 복구 기능 및 연속 작업을 수행할 수 있어야 합니다. 특히, 이 문서에서는 SQL 데이터 웨어하우스에서 데이터베이스 복원 및 지역 복원을 사용하여 사용자 오류 및 재해에서 복구할 수 있는 방법을 보여 줍니다.

## 복구 계획 

비즈니스 연속성 및 재해 복구 계획은 다음을 최적화하기 위해 필요합니다.

**복구 시간 목표(RTO):** 실패 중 복구 가용성 손실과 같은 중단 이벤트 후 데이터베이스가 완벽하게 복구되기 전의 최대 허용 가능한 시간입니다.

**복구 지점 목표(RTO):** 실패 중 최대 데이터 손실과 같은 중단 이벤트에서 데이터베이스가 복구될 때 손실된 업데이트의 최대 허용 가능한 시간입니다.

**예상 복구 시간(ERT):** 복원 요청 이후 데이터베이스가 완전하게 사용 가능하게 될 때까지의 예상 기간입니다.

자세한 내용은 [SQL 데이터베이스 무중단 업무 방식 개요][]를 참조하세요.

## 복구 시나리오

**인프라 오류로부터 복구:** 디스크 오류 등의 인프라 문제를 복구하는 데 이 시나리오를 참조합니다. 고객은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 확인하고자 합니다.

**사용자 오류에서 복구:** 이 시나리오에서는 의도 하지 않거나 부수적 데이터 손상 또는 삭제로부터의 복구를 가리킵니다. 사용자가 실수로 또는 우연하게 데이터를 수정하거나 삭제한 경우, 고객은 이전 시점으로 데이터베이스를 복원하여 비즈니스 연속성을 보장하고자 합니다.

**재해에서 복구(DR):** 이 시나리오는 주요 재해로부터의 복구를 가리킵니다. 자연 재해 또는 지역 가동 중단과 같은 중단 이벤트로 데이터베이스를 사용할 수 없는 시나리오에서는 고객이 비즈니스 작업을 계속하기 위해 다른 지역에서 데이터베이스를 복구하려고 합니다.


## 백업 및 복원 기능

SQL 데이터 웨어하우스가 데이터베이스의 안정성을 개선하고 앞에서 언급한 시나리오에서의 복구 기능 및 연속 작업을 허용하는 방법을 살펴보겠습니다.


### 데이터 중복

SQL 데이터 웨어하우스는 계산과 저장소를 분리합니다. 모든 데이터는 지역 중복 Azure 저장소(RA-GRS)에 직접 기록됩니다. 지역 중복 저장소는 기본 지역에서 수백 마일 떨어져 있는 보조 영역에 데이터를 복제합니다. 기본 및 보조 지역에서 데이터는 별도 오류 도메인 및 업그레이드 도메인에 걸쳐 각각 세 번씩 복제됩니다. 이렇게 하면 영역 중 하나를 사용할 수 없게 하는 완전한 지역 가동 중단 또는 재해 경우더라도 데이터를 지속적으로 사용할 수 있습니다. 읽기 액세스 지역 중복 저장소에 대한 자세한 정보는 [Azure 저장소 중복 옵션][]을 참조하세요.

### 데이터베이스 복원

데이터베이스 복원은 데이터베이스를 이전 시점으로 복원하도록 설계되었습니다. Azure SQL 데이터 웨어하우스 서비스는 적어도 8시간마다 자동 저장소 스냅숏을 사용하여 모든 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 이러한 백업은 RA-GRS Azure 저장소에 저장되므로 기본적으로 지리적으로 중복됩니다. 자동 백업 및 복원 기능은 별도의 관리 방법 없이 실수로 손상 또는 삭제되지 않도록 데이터베이스를 보호합니다. 데이터베이스를 복원하는 방법에 대한 자세한 내용은 [작업 백업 및 복원][]을 참조하세요.

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
[작업 백업 및 복원]: sql-data-warehouse-backup-and-restore-tasks-portal.md
[Finalize a recovered database]: ../sql-database/sql-database-recovered-finalize.md
[관리 개요]: sql-data-warehouse-overview-management.md
[SQL 데이터베이스 무중단 업무 방식 개요]: ../sql-database/sql-database-business-continuity.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->