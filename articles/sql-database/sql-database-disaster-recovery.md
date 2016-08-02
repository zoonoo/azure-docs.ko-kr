<properties 
   pageTitle="SQL 데이터베이스 재해 복구 | Microsoft Azure" 
   description="Azure SQL 데이터베이스 활성 지역 복제 및 지역 복원 기능을 사용하여 하위 지역 데이터 센터 중단 또는 오류로부터 데이터베이스를 복구하는 방법에 대해 알아봅니다." 
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
   ms.workload="sqldb-bcdr" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스 복원 또는 보조 데이터베이스에 대한 장애 조치

Azure SQL 데이터베이스는 중단에서의 복구를 위해 다음 기능을 제공합니다.

- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [지역 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

재해 대비 및 데이터베이스 복구 시기에 대한 자세한 내용은 [비즈니스 연속성](sql-database-business-continuity.md) 및 [비즈니스 연속성 설계 및 복구 시나리오]()를 참조하세요.

## 복구를 시작해야 하는 시기

복구 작업은 응용 프로그램에 영향을 줍니다. SQL 연결 문자열을 변경해야 하며 데이터가 영구적으로 손상될 수 있습니다. 따라서 중단이 응용 프로그램의 RTO보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 응용 프로그램이 프로덕션에 배포되면 응용 프로그램 상태를 정기적으로 모니터링하고 다음 데이터 요소를 사용하여 복구가 보장되는지 확인해야 합니다.

1.	응용 프로그램 계층에서 데이터베이스로 영구적인 연결 실패
2.	Azure 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.
3.	Azure SQL 데이터베이스 서버는 성능이 저하됨으로 표시됩니다.

가동 중지 시간에 대한 응용 프로그램 허용 범위 및 가능한 비즈니스 책임에 따라 다음과 같은 복구 옵션을 고려할 수 있습니다.

[Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx)(*LastAvailableBackupDate*)를 사용하여 가장 최근의 지역에서 복제된 복원 지점을 가져옵니다.

## 서비스 복구 대기

Azure 팀은 가능한 한 신속하게 서비스 가용성을 복원하기 위해 열심히 작업하지만 루트에 따라 몇 시간 또는 며칠씩 걸릴 수 있습니다. 응용 프로그램이 가동 중지 시간을 상당히 허용할 수 있는 경우 복구가 완료되기를 기다릴 수밖에 없습니다. 이 경우에 사용자의 조치가 필요하지 않습니다. 서비스의 현재 상태를 [Azure 서비스 상태 대시보드](https://azure.microsoft.com/status/)에서 확인할 수 있습니다. 지역 복구 후에 응용 프로그램의 가용성이 복원됩니다.

## 지역에서 복제된 보조 데이터베이스로 장애 조치(failover)

응용 프로그램의 가동 중지 시간으로 인해 비즈니스 책임이 발생할 수 있는 경우 응용 프로그램에서 지역에서 복제된 데이터베이스를 사용해야 합니다. 가동 중지 시에도 응용 프로그램이 다른 지역의 가용성을 신속하게 복원할 수 있습니다. [지역에서 복제를 구성하는 방법](sql-database-geo-replication-portal.md)을 참조하세요.

데이터베이스의 가용성을 복원하려면 지원되는 방법 중 하나를 사용하여 지역에서 복제된 보조 데이터베이스에 장애 조치를 시작해야 합니다.


다음 가이드 중 하나를 사용하여 지역에서 복제된 보조 데이터베이스를 장애 조치합니다.

- [Azure 포털을 사용하여 지역에서 복제된 보조 데이터베이스에 장애 조치](sql-database-geo-replication-portal.md)
- [PowerShell을 사용하여 지역에서 복제된 보조 데이터베이스에 장애 조치](sql-database-geo-replication-powershell.md)
- [T-SQL을 사용하여 지역에서 복제된 보조 데이터베이스에 장애 조치](sql-database-geo-replication-transact-sql.md)



## 지역 복원을 사용한 복구

응용 프로그램의 가동 중지 시간으로 인해 비즈니스 책임이 발생하지 않은 경우 응용 프로그램 데이터베이스를 복구하는 방법으로 지역 복원을 사용할 수 있습니다. 최신 지역 중복 백업에서 데이터베이스의 복사본을 만듭니다.

다음 가이드 중 하나를 사용하여 새 영역에는 데이터베이스를 지역 복원합니다.

- [Azure 포털을 사용하여 새 지역에 대한 데이터베이스 지역 복원](sql-database-geo-restore-portal.md)
- [PowerShell을 사용하여 새 지역에 대한 데이터베이스 지역 복원](sql-database-geo-restore-powershell.md)


## 복구 후 데이터베이스 구성

지역에서 복제 장애 조치(failover) 또는 지리적 복원을 사용하여 가동 중단에서 복구하는 경우 일반적인 응용 프로그램 함수를 다시 시작할 수 있도록 새 데이터베이스에 대한 연결이 제대로 구성되었는지 확인해야 합니다. 복구된 데이터베이스 프로덕션을 준비하는 작업의 검사 목록입니다.

### 연결 문자열 업데이트

복구된 데이터베이스가 다른 서버에 상주하기 때문에 해당 서버를 가리키도록 응용 프로그램의 연결 문자열을 업데이트해야 합니다.

연결 문자열을 변경하는 방법에 대한 자세한 내용은 [연결 라이브러리](sql-database-libraries.md)에 대한 적절한 개발 언어를 참조하세요.

### 방화벽 규칙 구성

서버 및 데이터베이스에서 구성된 방화벽 규칙이 주 데이터베이스와 주 서버에서 구성된 방화벽 규칙과 일치하는지 확인해야 합니다. 자세한 내용은 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)을 참조하세요.


### 로그인 및 데이터베이스 사용자 구성

응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스팅하는 서버에 존재하도록 해야 합니다. 자세한 내용은 [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)을 참조하세요.

>[AZURE.NOTE] 재해 복구 훈련 동안 서버 방화벽 규칙 및 로그인(및 해당 사용 권한)을 구성하고 테스트해야 합니다. 이러한 서버 수준 개체 및 해당 구성을 중단 도중에는 사용하지 못할 수 있습니다. 자세한 내용은 [재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md)을 참조하세요.

### 원격 분석 경고 설정

기존 경고 규칙 설정을 업데이트하여 복구된 데이터베이스와 다른 서버에 매핑하도록 해야 합니다.

데이터베이스 경고 규칙에 대한 자세한 내용은 [경고 알림 수신](../azure-portal/insights-receive-alert-notifications.md) 및 [서비스 상태 추적](../azure-portal/insights-service-health.md)을 참조하세요.

### 감사 사용

데이터베이스에 액세스하기 위해 감사가 필요한 경우, 데이터베이스 복구 후에 감사 사용을 설정해야 합니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요. 또한 "하위 클라이언트"에 대한 자세한 내용은 [감사 및 하위 수준 클라이언트 지원](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)을 참조합니다.


## 다음 단계

- Azure SQL 데이터베이스 자동화 백업에 대한 자세한 내용은 [SQL 데이터베이스 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 비즈니스 연속성 설계 및 복구 시나리오에 대해 알아보려면 [연속성 시나리오](sql-database-business-continuity-scenarios.md)를 참조하세요.
- 복구를 위해 자동화된 백업을 사용하는 방법을 알아보려면 [서비스에서 시작한 백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-geo-replication-overview.md)를 참조하세요.
- 보관을 위해 자동화된 백업을 사용하는 방법을 알아보려면 [데이터베이스 복사](sql-database-copy.md)를 참조하세요.

<!---HONumber=AcomDC_0713_2016-->