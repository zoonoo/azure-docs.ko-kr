<properties
   pageTitle="클라우드 비즈니스 연속성 - 지역 복원 | Microsoft Azure"
   description="Azure SQL 데이터베이스에서 클라우드 무중단 업무 방식 및 데이터베이스 복구를 지원하고 중요 업무용 클라우드 응용 프로그램을 계속해서 실행할 수 있도록 하는 방법을 알아봅니다."
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

# 개요: 지역 중복 백업에서 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [지정 시간 복원](sql-database-point-in-time-restore.md)
- [삭제된 데이터베이스 복원](sql-database-restore-deleted-database.md)
- [지역 복원](sql-database-geo-restore.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)


지리적 복원 기능을 사용하면 가장 최근의 [자동 매일 백업](sql-database-automated-backups.md)에서 Azure 지역에 있는 모든 서버의 SQL 데이터베이스를 복원합니다. 지리적 복원은 지역 중복 백업을 해당 원본으로 사용하고 가동 중단으로 인해 데이터베이스 또는 데이터 센터에 액세스할 수 없는 경우에도 데이터베이스를 복구하는 데 사용될 수 있습니다. [Azure 포털](sql-database-geo-restore-portal.md), [PowerShell](sql-database-geo-restore-powershell.md) 또는 [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)를 사용할 수 있습니다.

> [AZURE.SELECTOR]
- [개요](sql-database-geo-restore.md)
- [Azure 포털](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

지리적 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션입니다. Azure 지역의 모든 서버에서 데이터베이스를 만들 수 있습니다. 지리적 복원은 지역 중복 Azure 저장소에서 [자동화된 데이터베이스 백업](sql-database-automated-backups.md)에 의존하고 지역에서 복제된 백업 복사본에서 복원되므로 주 지역의 저장소 중단에 유연합니다.

## 지리적 복원 세부 사항

지리적 복원은 지정 시간 복원과 동일한 기술을 사용하지만 한 가지 중요한 차이점이 있습니다. 지역에서 복제된 Blob 저장소(RA-GRS)의 최신 매일 백업의 복사본에서 데이터베이스를 복원합니다. 각 활성 데이터베이스의 경우 서비스에는 매주 전체 백업, 매일 여러 개의 차등 백업 및 5분 마다 저장되는 트랜잭션 로그를 포함한 백업 체인을 유지 관리합니다. 이러한 Blob는 주 지역에 대규모 실패 후에도 매일 백업을 사용할 수 있도록 지리적으로 복제됩니다. 다음은 저장소 컨테이너에 복사된 주간 및 매일 백업의 지역에서 복제를 보여 줍니다.

![geo-restore](./media/sql-database-geo-restore/geo-restore-1.png)


지역에서 대규모 인시던트로 인해 데이터베이스 응용 프로그램을 사용할 수 없게 되면 지리적 복원 기능을 사용하여 가장 최근의 백업에서 다른 지역에 있는 서버로 데이터베이스를 복원할 수 있습니다. 모든 백업은 지역에서 복제되고 백업이 수행된 시점과 다른 지역의 Azure Blob에 지역 복제되는 시점 사이에 지연이 있을 수 있습니다. 재해가 발생한 경우 최대 1시간 동안의 데이터가 손실(즉, 최대 1시간의 RPO)되므로 이 지연은 최대 한 시간일 수 있습니다. 다음은 마지막 매일 백업에서 데이터베이스의 복원을 보여 줍니다.


![geo-restore](./media/sql-database-geo-restore/geo-restore-2.png)

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx)(*LastAvailableBackupDate*)를 사용하여 가장 최근의 지역에서 복제된 복원 지점을 가져옵니다.

## 지역 복원에 대한 복구 시간

복구 시간은 데이터베이스의 크기와 데이터베이스의 성능 수준 및 대상 지역에서 처리 중인 동시 복원 요청 수 등 여러 가지 요인의 영향을 받습니다. 지역에서 장시간 가동 중단된 경우 다른 지역에서 많은 수의 지리적 복원 요청이 처리 중일 수 있습니다. 많은 수의 요청이 있는 경우 해당 지역에 데이터베이스의 복구 시간이 늘어날 수 있습니다. 데이터베이스를 복원하는 데 걸리는 기간은 데이터베이스의 크기, 트랜잭션 로그의 수, 네트워크 대역폭과 같은 여러 요인에 따라 달라집니다. 대부분의 데이터베이스는 12시간 내에 완전히 복원됩니다.

## 요약

지리적 복원 기능은 모든 서비스 계층에서 사용할 수 있는 반면 RPO와 ERT(예상 복구 시간)가 가장 긴 SQL 데이터베이스에서 사용할 수 있는 재해 복구 솔루션 중 가장 기본적인 기능입니다. 최대 크기 2GB인 기본 데이터베이스의 경우 지리적 복원은 ERT가 12시간인 적절한 DR 솔루션을 제공합니다. 대규모 표준 또는 프리미엄 데이터베이스의 경우 크게 복구 시간이 단축되지 않으면 데이터 손실 가능성을 줄이기 위해 활성 지역 복제를 사용하도록 고려해야 합니다. 활성 지역 복제는 지속적으로 복제된 보조 데이터베이스에 대한 장애 조치를 시작하도록 하여 훨씬 더 낮은 RPO와 ERT를 제공합니다. 자세한 내용은 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.

## 다음 단계

- 지역 중복 백업에서 Azure 포털을 사용하여 Azure SQL 데이터베이스를 복원하는 방법에 대한 자세한 단계는 [Azure 포털을 사용하여 지리적 복원](sql-database-geo-restore-portal.md)을 참조하세요.
- 지역 중복 백업에서 PowerShell을 사용하여 Azure SQL 데이터베이스를 복원하는 방법에 대한 자세한 단계는 [PowerShell을 사용하여 지리적 복원](sql-database-geo-restore-powershell.md)을 참조하세요.
- 가동 중단에서 복구하는 방법에 대한 자세한 내용은 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하세요.

## 추가 리소스

- [비즈니스 연속성 시나리오](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->