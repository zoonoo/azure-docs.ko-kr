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
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# 개요: 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [개요](sql-database-restore-deleted-database.md)
- [Azure 포털](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)에 대한 보존 기간 동안 삭제된 데이터베이스를 복원할 수 있습니다. [Azure 포털](sql-database-restore-deleted-database-portal.md), [PowerShell](sql-database-restore-deleted-database-powershell.md) 또는 [REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx)를 사용할 수 있습니다.

데이터베이스를 삭제하면 삭제된 시점으로 데이터베이스를 복원할 수 있으므로 기본 보존 기간에 대한 최종 백업이 보존됩니다.

## 최근에 삭제된 데이터베이스 복원

삭제된 데이터베이스의 경우 복원 지점은 데이터베이스의 삭제 시점에 고정됩니다. 삭제된 데이터베이스를 복원할 경우에만 원래 데이터베이스를 포함하는 서버에 복원할 수 있습니다. 일단 삭제되면 해당 서버에 이전에 있던 데이터베이스를 복원할 수 없으므로 서버를 삭제할 때 주의를 기울여야 합니다.

> [AZURE.IMPORTANT] Azure SQL 데이터베이스 서버 인스턴스를 삭제하면 모든 해당 데이터베이스도 삭제되고 복구할 수 없습니다.

## 요약

자동 백업은 데이터베이스를 실수로 삭제되지 않도록 보호합니다. 이 비용 및 관리가 없는 솔루션은 모든 SQL 데이터베이스에서 사용할 수 있습니다.

## 다음 단계

- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [Azure 포털을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-portal.md)
- [PowerShell을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md)
- [REST API를 사용하여 삭제된 데이터베이스 복원](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL 데이터베이스 자동화된 백업](sql-database-automated-backups.md)

## 추가 리소스

- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->