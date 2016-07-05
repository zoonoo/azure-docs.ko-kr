<properties
   pageTitle="클라우드 비즈니스 연속성 - 삭제된 데이터베이스 복원 - SQL 데이터베이스 | Microsoft Azure"
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
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 개요: 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)


[서비스 계층](sql-database-service-tiers.md)의 [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)에 대한 보존 기간 동안 삭제된 데이터베이스를 복원할 수 있습니다. [Azure 포털](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)를 사용할 수 있습니다.

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 최근에 삭제된 데이터베이스 복원

동일하거나 다른 데이터베이스 이름을 사용하여 삭제된 데이터베이스를 원래 데이터베이스를 포함하는 논리 서버로 복원할 수 있습니다. 삭제된 데이터베이스의 경우 복원 지점은 데이터베이스의 삭제 시점에 고정됩니다.

> [AZURE.IMPORTANT] Azure SQL 데이터베이스 서버 인스턴스를 삭제하면 모든 해당 데이터베이스도 삭제되고 복구할 수 없습니다.

## 복원 시간

데이터베이스를 복원하는 데 걸린 시간은 선택한 지점의 상태를 다시 생성하기 위해 재생하는 데 필요한 데이터베이스의 크기, 트랜잭션 로그의 수, 선택된 시점 및 작업량을 비롯한 많은 요인에 따라 달라집니다. 매우 큰 및/또는 활성 데이터베이스의 경우 복원에는 몇 시간이 걸릴 수 있습니다. 데이터베이스를 복원하면 항상 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만들기 때문에 복원된 데이터베이스에 새 이름을 지정해야 합니다. 대부분의 데이터베이스는 12시간 내에 완전히 복원됩니다.

## 요약

자동 백업은 데이터베이스를 실수로 삭제되지 않도록 보호합니다. 이 비용 및 관리가 없는 솔루션은 모든 SQL 데이터베이스에서 사용할 수 있습니다.

## 다음 단계

- Azure 포털을 사용하여 삭제된 데이터베이스를 복원하는 방법에 대한 자세한 단계는 [Azure 포털을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-portal.md)을 참조하세요.
- PowerShell을 사용하여 삭제된 데이터베이스를 복원하는 방법에 대한 자세한 단계는 [PowerShell을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md)을 참조하세요.
- 삭제된 데이터베이스를 복원하는 방법에 대한 자세한 내용은 [REST API를 사용하여 삭제된 데이터베이스 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)을 참조하세요.
- Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.

## 추가 리소스

- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->