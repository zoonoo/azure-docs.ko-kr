<properties 
   pageTitle="SQL 데이터베이스 재해 복구" 
   description="Azure SQL 데이터베이스 활성 지역 복제, 표준 지역 복제 및 지역 복원 기능을 사용하여 하위 지역 데이터 센터 중단 또는 오류로부터 데이터베이스를 복구하는 방법에 대해 알아봅니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="11/09/2015"
   ms.author="elfish"/>

# 중단 상태에서 Azure SQL 데이터베이스 복구

Azure SQL 데이터베이스는 중단에서의 복구를 위해 다음 기능을 제공합니다.

- 활성 지역에서 복제[(블로그)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- 표준 지역에서 복제[(블로그)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- 지역 복원[(블로그)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
- 새로운 지역에서 복제 기능[(블로그)](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)

재해 대비 및 데이터베이스 복구 시기에 대한 자세한 내용은 [비즈니스 연속성을 위한 설계](sql-database-business-continuity-design.md) 페이지를 참조하세요.

##복구를 시작해야 하는 시기 

복구 작업은 응용 프로그램에 영향을 줍니다. SQL 연결 문자열을 변경해야 하며 데이터가 영구적으로 손상될 수 있습니다. 따라서 중단이 응용 프로그램의 RTO보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 응용 프로그램이 프로덕션에 배포되면 응용 프로그램 상태를 정기적으로 모니터링하고 다음 데이터 요소를 사용하여 복구가 보장되는지 확인해야 합니다.

1. 응용 프로그램 계층에서 데이터베이스로 영구적인 연결 실패
2. Azure 클래식 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.

> [AZURE.NOTE] 데이터베이스가 복구된 후 [복구 후 데이터베이스 구성](#postrecovery) 가이드에 따라 데이터베이스를 사용하도록 구성할 수 있습니다.

## 지역에서 복제된 보조 데이터베이스로 장애 조치(failover)
> [AZURE.NOTE] 장애 조치(failover)에 사용할 보조 데이터베이스를 구성해야 합니다. 지역에서 복제는 표준 및 프리미엄 데이터베이스에서만 사용할 수 있습니다. [지역에서 복제를 구성하는 방법](sql-database-business-continuity-design.md)을 참조하세요.

###Azure 클래식 포털
Azure 클래식 포털을 사용하여 지역 복제된 보조 데이터베이스와의 연속 복사 관계를 종료합니다.

1. [Azure 클래식 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다. 
4. 데이터베이스 블레이드의 맨 아래에서 **지역에서 복제 지도**를 선택합니다.
4. **보조** 아래에서 복구하려는 데이터베이스 이름이 있는 행을 마우스 오른쪽 단추로 클릭하고 **장애 조치(Failover)**를 선택합니다.

###PowerShell
PowerShell을 사용하여 지역에서 복제된 보조 데이터베이스로 장애 조치(failover)를 시작하려면 [Set-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393.aspx) cmdlet을 사용합니다.
		
		$database = Get-AzureRMSqlDatabase –DatabaseName "mydb” –ResourceGroupName "rg2” –ServerName "srv2”
		$database | Set-AzureRMSqlDatabaseSecondary –Failover -AllowDataLoss

###REST API 
REST를 사용하여 프로그래밍 방식으로 보조 데이터베이스로의 장애 조치(failover)를 시작할 수 있습니다.

1. [복제 링크 가져오기](https://msdn.microsoft.com/library/mt600778.aspx) 작업을 사용하여 특정 보조에 대한 복제 링크를 가져옵니다.
2. 데이터 손실을 허용하는 상태로 [보조 데이터베이스를 주 데이터베이스로 설정](https://msdn.microsoft.com/library/mt582027.aspx)을 사용하여 보조로 장애 조치(failover)합니다. 

## 지역 복원을 사용한 복구

데이터베이스의 중단이 발생하는 경우, 지역 복원을 사용하여 최신 지역 중복 백업으로 데이터베이스를 복구할 수 있습니다.

> [AZURE.NOTE] 데이터베이스를 복구하면 새 데이터베이스가 생성됩니다. 복구하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)하여 이 할당량을 늘리도록 요청할 수 있습니다.

###Azure 클래식 포털
Azure 클래식 포털의 지역 복원을 사용하여 SQL 데이터베이스를 복원하려면 다음 단계를 사용하세요.

1. [Azure 클래식 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **새로 만들기**를 선택한 다음 **데이터 및 저장소**를 선택하고 **SQL 데이터베이스**를 선택합니다.
2. 원본으로 **백업**을 선택한 다음 복구에 사용할 지역 중복 백업을 선택합니다.
3. 나머지 데이터베이스 속성을 지정한 다음 **만들기**를 클릭합니다.
4. 데이터베이스 복원 프로세스가 시작되며 화면 왼쪽의 **알림**을 사용하여 모니터링할 수 있습니다.

###PowerShell 
PowerShell 지역 복원을 사용하여 SQL 데이터베이스를 복원하려면 [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx) cmdlet에서 지역 복원 요청을 시작합니다.

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

###REST API 

프로그래밍 방식으로 데이터베이스 복구를 수행하려면 REST를 사용합니다.

1.	[복구 가능한 데이터베이스 나열](http://msdn.microsoft.com/library/azure/dn800984.aspx) 작업을 통해 복구 가능한 데이터베이스 목록을 가져옵니다.
	
2.	[복구 가능한 데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn800985.aspx) 작업을 통해 복구할 데이터베이스를 가져옵니다.
	
3.	[데이터베이스 복구 요청 만들기](http://msdn.microsoft.com/library/azure/dn800986.aspx) 작업을 통해 복구 요청을 만듭니다.
	
4.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 통해 복구 상태를 추적합니다.
 
## 복구 후 데이터베이스 구성<a name="postrecovery"></a>

복구된 데이터베이스 프로덕션을 준비하는 데 사용할 수 있는 작업의 검사 목록입니다.

### 연결 문자열 업데이트

응용 프로그램의 연결 문자열이 새로 복구된 데이터베이스를 가리키는지 확인합니다. 아래 상황 중 하나에 해당되는 경우 연결 문자열을 업데이트합니다.

  + 복구된 데이터베이스 이름으로 원본 데이터베이스와 다른 이름 사용
  + 복구된 데이터베이스가 원본 서버와 다른 서버에 존재

연결 문자열 변경에 대한 자세한 내용은 [Azure SQL 데이터베이스 연결: 핵심 권장 사항](sql-database-connect-central-recommendations.md)을 참조하세요.
 
### 방화벽 규칙 수정
서버 수준 및 데이터베이스 수준에서 방화벽 규칙을 확인하고 서버 및 새로 복구된 데이터베이스와 클라이언트 컴퓨터 또는 Azure의 연결이 설정되어 있는지 확인합니다. 자세한 내용은 [방법: 방화벽 설정 구성(Azure SQL 데이터베이스)](sql-database-configure-firewall-settings.md)을 참조하세요.

### 서버 로그인 및 데이터베이스 사용자 확인

응용 프로그램에서 사용하는 모든 로그인이 복구된 데이터베이스를 호스팅하는 서버에 존재하는지 확인합니다. 누락된 로그인을 다시 만들고 복구된 데이터베이스에 대한 적절한 사용 권한을 부여합니다. 자세한 내용은 [Azure SQL 데이터베이스에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

복구된 데이터베이스의 각 데이터베이스 사용자가 유효한 서버 로그인과 연결되어 있는지 확인합니다. 유효한 서버 로그인에 사용자를 매핑하려면 ALTER USER 문을 사용합니다. 자세한 내용은 [ALTER USER](http://go.microsoft.com/fwlink/?LinkId=397486)를 참조하세요.


### 원격 분석 경고 설정

기존 경고 규칙 설정이 복구된 데이터베이스에 매핑되어 있는지 확인합니다. 아래 상황 중 하나에 해당되는 경우 설정을 업데이트합니다.

  + 복구된 데이터베이스 이름으로 원본 데이터베이스와 다른 이름 사용
  + 복구된 데이터베이스가 원본 서버와 다른 서버에 존재

데이터베이스 경고 규칙에 대한 자세한 내용은 [경고 알림 수신](insights-receive-alert-notifications.md) 및 [서비스 상태 추적](insights-service-health.md)을 참조하세요.


### 감사 사용

데이터베이스에 액세스하기 위해 감사가 필요한 경우, 데이터베이스 복구 후에 감사 사용을 설정해야 합니다. 감사는 클라이언트 응용 프로그램이 *.database.secure.windows.net 패턴의 보안 연결 문자열을 사용한다는 것을 나타내기 위해 필요한 훌륭한 지표입니다. 자세한 내용은 [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md)을 참조하세요.

<!---HONumber=AcomDC_0128_2016-->