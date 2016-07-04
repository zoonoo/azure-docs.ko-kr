<properties 
   pageTitle="SQL 데이터베이스 사용자 오류 복구 | Microsoft Azure" 
   description="Azure SQL 데이터베이스의 PITR(지정 시간 복원) 기능을 사용하여 사용자 오류, 실수로 인한 데이터 손상 또는 삭제된 데이터베이스를 복구하는 방법을 알아봅니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# 사용자 오류에서 Azure SQL 데이터베이스 복구

Azure SQL 데이터베이스는 사용자 오류나 의도하지 않은 데이터 수정에서 복구할 수 있는 두 가지 핵심 기능을 제공합니다.

- [지정 시간 복원](sql-database-point-in-time-restore.md) 
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)

Azure SQL 데이터베이스는 항상 새로운 데이터베이스로 복원됩니다. 이러한 복원 기능은 모든 기본, 표준 및 프리미엄 데이터베이스에 대해 제공됩니다.

##지정 시간 복원

사용자 오류나 의도하지 않은 데이터 수정 시, 지정 시간 복원 기능을 사용하여 데이터베이스 보존 기간 내의 지정된 임의의 시간으로 데이터베이스를 복원할 수 있습니다.

기본 데이터베이스는 7일 동안 보존되고 표준 데이터베이스는 14일 동안, 프리미엄 데이터베이스는 35일 동안 보존됩니다. 데이터베이스 백업 보존에 대한 자세한 내용은 [자동화된 백업](sql-database-automated-backups.md)을 참조하세요.

지정 시간 복원을 수행하려면 다음을 참조하세요.

- [Azure 포털을 사용하여 특정 시점 복원](sql-database-point-in-time-restore-portal.md)
- [PowerShell을 사용하여 특정 시점 복원](sql-database-point-in-time-restore-powershell.md)
- [REST API를 사용하여 특정 시점 복원(createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 


## 삭제된 데이터베이스 복원

데이터베이스가 삭제된 경우, Azure SQL 데이터베이스를 사용하면 삭제된 데이터베이스를 지정된 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터베이스는 데이터베이스 보존 기간 동안 삭제된 데이터베이스의 백업을 저장합니다.

삭제된 데이터베이스의 보존 기간은 해당 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스의 존재 일 수 중 더 작은 일 수에 의해 결정됩니다. 데이터베이스 보존에 대해 자세히 알아보려면 [자동화된 백업](sql-database-automated-backups.md)을 참조하세요.

삭제된 데이터베이스를 복원하려면:

- [Azure 포털을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-portal.md)
- [PowerShell을 사용하여 삭제된 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md)
- [REST API를 사용하여 삭제된 데이터베이스 복원(createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## 다음 단계

- 재해 복구를 위해 활성 지역 복제를 사용 및 구성하는 방법에 대한 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 재해 복구를 위해 지리적 복원을 사용하는 방법에 대한 자세한 내용은 [지리적 복원](sql-database-geo-restore.md)을 참조하세요.

## 추가 리소스

- [SQL 데이터베이스 비즈니스 연속성 및 재해 복구](sql-database-business-continuity.md)
- [특정 시점 복원](sql-database-point-in-time-restore.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)
- [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)
- [SQL 데이터베이스 BCDR FAQ](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->