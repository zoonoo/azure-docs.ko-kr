<properties 
    pageTitle="PowerShell로 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작 | Microsoft Azure" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="08/29/2016"
    ms.author="sstein"/>

# PowerShell로 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작



> [AZURE.SELECTOR]
- [Azure 포털](sql-database-geo-replication-failover-portal.md)
- [PowerShell](sql-database-geo-replication-failover-powershell.md)
- [T-SQL](sql-database-geo-replication-failover-transact-sql.md)


이 문서에서는 PowerShell로 SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치를 시작하는 방법을 보여 줍니다. 지역에서 복제를 구성하려면 [Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-powershell.md)을 참조하세요.



## 계획된 장애 조치(failover) 시작

**Set-AzureRmSqlDatabaseSecondary** cmdlet를 **-Failover** 매개 변수와 함께 사용하여 기존 주가 보조가 되도록 강등시키는 방식으로 보조 데이터베이스가 새로운 주 데이터베이스가 되도록 승격할 수 있습니다. 이 기능은 재해 복구 훈련 중과 같은 계획된 장애 조치(failover)에 대해 설계되었으며 주 데이터베이스는 사용할 수 있어야 합니다.

명령은 다음 워크플로 수행합니다.

1. 일시적으로 복제가 동기 모드로 전환됩니다. 이로 인해 처리되지 않은 모든 트랜잭션이 보조 데이터베이스로 플러시됩니다.

2. 지역에서 복제 파트너 관계에서 두 데이터베이스의 역할을 전환합니다.

이 순서는 두 데이터베이스가 역할이 전환되기 전에 동기화됨을 보장하므로 데이터 손실이 발생하지 않습니다. 역할이 전환되는 동안 두 데이터베이스를 모두 사용할 수 없는 (0-25초의 순서로) 짧은 기간이 있습니다. 전체 작업은 정상적인 상황에서 완료하는데 1분 미만이 걸려야 합니다. 자세한 내용은 [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx)를 참조하세요.




이 cmdlet은 보조 데이터베이스를 주 데이터베이스로 전환하는 프로세스가 완료되면 반환합니다.

다음 명령은 리소스 그룹 "rg2" 아래의 서버 "srv2"에서 데이터베이스 "mydb"의 역할을 주 데이터베이스로 전환합니다. 두 데이터베이스를 완전히 동기화한 후 "db2"가 연결되었던 기존 주 데이터베이스를 보조로 전환합니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [AZURE.NOTE] 드문 경우로 작업을 완료할 수 없으며 응답하지 않는 것으로 나타날 수 있습니다. 이 경우 사용자는 강제 장애 조치(failover) 명령(계획되지 않은 장애 조치(failover))을 호출하고 데이터 손실을 허용할 수 있습니다.


## 주 데이터베이스에서 보조 데이터베이스로 계획되지 않은 장애 조치 시작


**Set-AzureRmSqlDatabaseSecondary** cmdlet와 **–Failover** 및 **-AllowDataLoss** 매개 변수를 함께 사용하여 주 데이터베이스를 더 이상 사용할 수 없는 경우 보조가 되도록 기존 주의 수준 내리기를 강제하는 계획되지 않은 방식으로 보조 데이터베이스가 새로운 주 데이터베이스가 되도록 승격할 수 있습니다.

이 기능은 데이터베이스의 가용성 복원이 중요하고 일부 데이터 손실이 허용되는 경우 재해 복구를 위해 설계되었습니다. 강제 장애 조치가 호출되면 지정된 보조 데이터베이스는 즉시 주 데이터베이스가 되며 쓰기 트랜잭션 승인을 시작합니다. 강제 장애 조치(failover) 작업 후, 기존 주 데이터베이스가 이 새 주 데이터베이스와 다시 연결할 수 있는 즉시 기존 주 데이터베이스에서 증분 백업이 수행되고 이어서 이전 주 데이터베이스는 새 주 데이터베이스에 대한 보조 데이터베이스로 만들어지며 이는 단순히 새 주의 복제본입니다.

하지만 보조 데이터베이스에서는 특정 시점 복원이 지원되지 않으므로 새로운 주 데이터베이스로 복제되지 않은 커밋된 데이터를 이전 주 데이터베이스로 복구하려면 데이터베이스를 알려진 로그 백업으로 복원하는 CSS를 활용해야 합니다.

> [AZURE.NOTE] 주 및 보조가 모두 온라인일 때 명령이 실행되는 경우 데이터 동기화 없이 이전 주는 즉시 새 보조가 됩니다. 주 데이터베이스가 명령이 실행될 때 트랜잭션을 커밋하면 일부 데이터가 손실될 수 있습니다.


주 데이터베이스에 여러 보조가 있는 경우 명령이 부분적으로 성공합니다. 명령이 실행된 보조는 주가 됩니다. 그러나 이전의 주 데이터베이스는 여전히 주로 남아 있습니다. 즉, 두 개의 주가 일관되지 않은 상태로 끝나고 일시 중단된 복제 링크로 연결됩니다. 사용자는 이러한 주 데이터베이스 중 하나에서 "보조 제거" API를 사용하여 이러한 구성을 수동으로 복구해야 합니다.


다음 명령은 주 데이터베이스를 사용할 수 없을 때 "mydb"라는 데이터베이스의 역할을 주로 전환합니다. 다시 온라인 상태가 되면 "mydb"가 연결되었던 기존 주 데이터베이스가 보조로 전환됩니다. 해당 지점에서 동기화로 인해 데이터 손실이 발생할 수 있습니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" –ResourceGroupName "rg2” –ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary –Failover -AllowDataLoss




## 다음 단계   

- 장애 조치(failover) 후에는 새로운 주 데이터베이스에서 서버 및 데이터베이스의 인증 요구 사항이 구성되어 있는지 확인합니다. 자세한 내용은 [재해 복구 후의 SQL Database 보안](sql-database-geo-replication-security-config.md)을 참조하세요.
- 사전 및 사후 복구 단계를 비롯한 활성 지역 복제를 사용하고 재해 복구 훈련을 수행하여 재해 후에 복구하는 방법을 알아보려면 [재해 복구 훈련](sql-database-disaster-recovery.md)을 참조하세요.
- 활성 지역 복제에 대한 Sasha Nosov의 블로그 게시물은 [새로운 지역에서 복제 기능에 대한 주요 내용](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)을 참조하세요.
- 활성 지역 복제를 사용하여 클라우드 응용 프로그램을 설계하는 방법에 대한 자세한 내용은 [지역에서 복제를 사용하여 무중단 업무 방식에 사용 가능하도록 클라우드 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)를 참조하세요.
- 탄력적 데이터베이스 풀에서 활성 지역 복제를 사용하는 방법에 대한 자세한 내용은 [탄력적 풀 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)을 참조하세요.
- 무중단 업무 방식에 대한 개략적 정보는 [무중단 업무 방식 개요](sql-database-business-continuity.md)를 참조하세요.

<!---HONumber=AcomDC_0831_2016-->