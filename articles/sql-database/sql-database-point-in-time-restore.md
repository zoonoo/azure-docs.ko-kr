<properties
   pageTitle="클라우드 비즈니스 연속성 - 특정 시점 복원 - SQL 데이터베이스 | Microsoft Azure"
   description="Azure SQL 데이터베이스를 이전 시점(최대 35일)에 롤백할 수 있는 특정 시점 복원에 대해 알아봅니다."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-bcdr"
   ms.date="06/17/2016"
   ms.author="sstein"/>

# 개요: SQL 데이터베이스 지정 시간 복원

> [AZURE.SELECTOR]
- [비즈니스 연속성 개요](sql-datbase-business-continuity.md)
- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)

지정 시간 복원을 사용하면 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)을 사용하여 기존 데이터베이스를 새 데이터베이스로서 동일한 논리 서버의 이전 시점으로 복원할 수 있습니다. 기존 데이터베이스를 덮어쓸 수는 없습니다. [Azure 포털](sql-database-point-in-time-restore-portal.md), [PowerShell](sql-database-point-in-time-restore-powershell.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)를 사용하여 이전 시점으로 복원할 수 있습니다.

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

성능 수준 또는 탄력적 풀에 데이터베이스를 복원할 수 있습니다 복원이 새 데이터베이스를 만들고 복원된 데이터베이스의 서비스 계층 및 성능 수준이 라이브 데이터베이스의 현재 상태와 다를 수 있다는 점에 주의하며 서버 또는 풀의 DTU 할당량이 충분한지 확인해야 합니다. 일단 완료되면 복원된 데이터베이스는 서비스 계층 및 성능 수준에 따라 정상 요금이 청구되는 완벽하게 액세스할 수 있는 일반적인 온라인 데이터베이스가 됩니다.

사용 가능한 복원 지점 중 가장 오래된 지점을 알아보려면 [Get Database](https://msdn.microsoft.com/library/dn505708.aspx)(*RecoveryPeriodStartDate*)를 사용하여 가장 오래된 복원 지점(지역에서 복제된 복원 지점 아님)을 찾을 수 있습니다.

복구를 위해 데이터베이스를 복원하는 경우 원본 데이터베이스에 대한 대체로 복원된 데이터베이스를 처리하거나 데이터를 검색하고 원래 데이터베이스를 업데이트하는 데 사용할 수 있습니다. 복원된 데이터베이스를 원래 데이터베이스에 대한 대체로 여기는 경우 성능 수준을 확인하고/하거나 필요한 경우 서비스 계층 적절하고 데이터베이스의 크기를 조정해야 합니다. 원본 데이터베이스의 이름을 바꿀 수 있으며 T-SQL에서 ALTER DATABASE 명령을 사용하여 복원된 데이터베이스에 원래 이름을 제공할 수 있습니다.

복원된 데이터베이스에서 데이터를 검색하려는 경우 필요한 모든 데이터 복구 스크립트를 별도로 작성하고 실행해야 합니다. 복원 작업을 완료하는 데 긴 시간이 걸리지만 데이터베이스가 데이터베이스 목록 전체에 표시됩니다. 복원하는 동안 데이터베이스를 삭제하는 경우 작업을 취소하고 청구되지 않습니다.

## 지정 시간 복원에 대한 복구 시간

데이터베이스를 복원하는 데 걸린 시간은 선택한 지점의 상태를 다시 생성하기 위해 재생하는 데 필요한 데이터베이스의 크기, 트랜잭션 로그의 수, 선택된 시점 및 작업량을 비롯한 많은 요인에 따라 달라집니다. 매우 큰 및/또는 활성 데이터베이스의 경우 복원에는 몇 시간이 걸릴 수 있습니다. 대부분의 데이터베이스는 12시간 내에 완전히 복원됩니다.

## 백업/복원 및 복사/내보내기/가져오기

지정 시간 복원은 실수로 인한 데이터 손실이나 손상으로부터 Basic, Standard 및 Premium 데이터베이스를 복구하기 위해 권장되는 방법입니다. 백업 및 복원은 비용이 절감되고(과도하지 않는 한 백업에 대한 요금이 부과되지 않음. 반면 트랜잭션 일관성 내보내기를 확인하는 데 필요한 데이터베이스 복사본 및 BACPAC 파일을 저장하기 위해 요금이 부과됨), 관리 없이(백업은 자동임. 반면 사용자가 직접 관리하거나 내보내기를 예약해야 함) 더 나은 RPO(복사/내보내기/가져오기를 사용하여 실질적이기 보다 세밀한(1분) 세분성으로 특정 시점에 복원할 수 있음) 및 짧은 복구 시간(백업에서 복원은 일반적으로 가져오기보다 빠르며 스키마 작성, 인덱스 사용 안 함, 데이터 로드 및 각 테이블에 인덱스 사용과 같은 단계를 포함)을 제공합니다. 복사 및 내보내기는 보존 기간이 넘는 장기 보관에 대한 솔루션으로 계속 권장됩니다.


## 요약

자동 백업 및 지정 시간 복원은 실수로 인한 데이터 손상 또는 데이터 삭제로부터 데이터베이스를 보호합니다. 이 비용 및 관리가 없는 솔루션은 모든 SQL 데이터베이스에서 사용할 수 있습니다. 백업 및 복원은 단기 복구 요구에 대한 대체 복사/내보내기/가져오기 솔루션을 통해 훨씬 향상된 기능을 제공합니다. 비즈니스 연속성 전략의 일부로 지정 시간 복원을 사용하고 장기 보관 또는 데이터 마이그레이션을 위해 필요에 따라 내보내기를 사용하는 것이 좋습니다.


## 다음 단계

- Azure 포털을 사용하여 지정 시간으로 복구하는 자세한 단계는 [Azure 포털을 사용하는 지정 시간 복원](sql-database-point-in-time-restore-portal.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복구하는 자세한 단계는 [PowerShell을 사용하는 지정 시간 복원](sql-database-point-in-time-restore-powershell.md)을 참조하세요.
- REST API를 사용하여 지정 시간으로 복구하는 방법에 대한 자세한 내용은 [REST API를 사용하는 지정 시간 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)을 참조하세요.
- 사용자 또는 응용 프로그램 오류로부터 복구하는 방법에 대한 자세한 내용은 [사용자 오류 복구](sql-database-user-error-recovery.md)를 참조하세요.

## 추가 리소스

- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->