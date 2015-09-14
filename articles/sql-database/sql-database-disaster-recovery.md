<properties 
   pageTitle="SQL 데이터베이스 재해 복구"
	description="Azure SQL 데이터베이스의 지역에서 복제 및 지역 복원 기능을 사용하여 하위 지역 데이터 센터 중단 또는 오류로부터 데이터베이스를 복구하는 방법에 대해 알아봅니다."
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
	ms.date="07/14/2015"
	ms.author="elfish"/>

# 중단 상태에서 Azure SQL 데이터베이스 복구

Azure SQL 데이터베이스는 몇 가지 중단 복구 기능을 제공합니다.

- 활성 지역에서 복제[(블로그)](http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/)
- 표준 지역에서 복제[(블로그)](http://azure.microsoft.com/blog/2014/09/03/azure-sql-database-standard-geo-replication/)
- 지역 복원[(블로그)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

재해 대비 및 데이터베이스 복구 시기에 대한 자세한 내용은 [비즈니스 연속성을 위한 설계](sql-database-business-continuity-design.md) 페이지를 참조하세요.

##복구를 시작해야 하는 시기 

복구 작업은 응용 프로그램에 영향을 줍니다. SQL 연결 문자열을 변경해야 하며 데이터가 영구적으로 손상될 수 있습니다. 따라서 중단이 응용 프로그램의 RTO보다 오래 지속될 가능성이 있을 때만 복구를 수행해야 합니다. 응용 프로그램이 프로덕션에 배포되면 응용 프로그램 상태를 정기적으로 모니터링하고 다음 데이터 요소를 사용하여 복구가 보장되는지 확인해야 합니다.

1. 응용 프로그램 계층에서 데이터베이스로 영구적인 연결 실패
2. Azure 포털에 광범위한 영향이 있는 지역 내 인시던트에 대한 경고가 표시됩니다.

## 지역에서 복제된 보조 데이터베이스에 대한 장애 조치(failover)
> [AZURE.NOTE][표준 지역에서 복제](https://msdn.microsoft.com/library/azure/dn758204.aspx) 또는 [활성 지역에서 복제](https://msdn.microsoft.com/library/azure/dn741339.aspx)에 장애 조치에 사용할 보조 데이터베이스가 포함되도록 구성해야 합니다. 지역에서 복제는 표준 및 프리미엄 데이터베이스에서만 사용할 수 있습니다.

주 데이터베이스에 중단이 발생하는 경우 보조 데이터베이스로 장애 조치하여 가용성을 복원할 수 있습니다. 그러려면 연속 복사 관계를 강제로 종료해야 합니다. 연속 복사 관계의 종료에 대한 자세한 설명은 [여기](https://msdn.microsoft.com/library/azure/dn741323.aspx)를 참조하세요.



###Azure 포털
1. [Azure 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다. 
4. 데이터베이스 블레이드의 맨 아래에서 **지역에서 복제 지도**를 선택합니다.
4. **보조** 아래에서 복구하려는 데이터베이스 이름이 있는 행을 마우스 오른쪽 단추로 클릭하고 **중지**를 선택합니다.

연속 복사 관계가 종료된 후, [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 복구된 데이터베이스를 구성할 수 있습니다.
###PowerShell
프로그래밍 방식으로 데이터베이스 복구를 수행하려면 PowerShell을 사용합니다.

보조 데이터베이스와 관계를 종료하려면 [Stop-AzureSqlDatabaseCopy](https://msdn.microsoft.com/library/dn720223) cmdlet을 사용합니다.
		
		$myDbCopy = Get-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -DatabaseName "SecondaryDatabaseName"
		$myDbCopy | Stop-AzureSqlDatabaseCopy -ServerName "SecondaryServerName" -ForcedTermination
		 
연속 복사 관계가 종료된 후, [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 복구된 데이터베이스를 구성할 수 있습니다.
###REST API 
프로그래밍 방식으로 데이터베이스 복구를 수행하려면 REST를 사용합니다.

1. [데이터베이스 복사 가져오기](https://msdn.microsoft.com/library/azure/dn509570.aspx) 작업을 통해 데이터베이스 연속 복사를 가져옵니다.
2. [데이터베이스 복사 중지](https://msdn.microsoft.com/library/azure/dn509573.aspx) 작업을 통해 데이터베이스 연속 복사를 중지합니다. 데이터베이스 복사 중지 요청 URI에 있는 보조 서버 이름과 데이터베이스 이름을 사용합니다.

 연속 복사 관계가 종료된 후, [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 복구된 데이터베이스를 구성할 수 있습니다.
## 지역 복원을 사용한 복구

데이터베이스의 중단이 발생하는 경우, 지역 복원을 사용하여 최신 지역 중복 백업으로 데이터베이스를 복구할 수 있습니다.

> [AZURE.NOTE]데이터베이스를 복구하면 새 데이터베이스가 생성됩니다. 복구하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)하여 이 할당량을 늘리도록 요청할 수 있습니다.

###Azure 포털
1. [Azure 포털](https://portal.Azure.com) 로그인
2. 화면 왼쪽에서 **새로 만들기**를 선택한 다음 **데이터 및 저장소**를 선택하고 **SQL 데이터베이스**를 선택합니다.
2. 원본으로 **백업**을 선택한 다음 복구에 사용할 지역 중복 백업을 선택합니다.
3. 나머지 데이터베이스 속성을 지정한 다음 **만들기**를 클릭합니다.
4. 데이터베이스 복원 프로세스가 시작되며 화면 왼쪽의 **알림**을 사용하여 모니터링할 수 있습니다.

데이터베이스가 복구된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 방식을 구성할 수 있습니다.
###PowerShell 
프로그래밍 방식으로 데이터베이스 복구를 수행하려면 PowerShell을 사용합니다.

지역 복원 요청을 시작하려면 [start-AzureSqlDatabaseRecovery](https://msdn.microsoft.com/library/azure/dn720224.aspx) cmdlet를 사용합니다. 자세한 연습에 대해서는 [방법 비디오](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-geo-restore-with-microsoft-azure-powershell/)를 참조하세요.

		$Database = Get-AzureSqlRecoverableDatabase -ServerName "ServerName" –DatabaseName “DatabaseToBeRecovered"
		$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –TargetServerName “TargetServerName”
		Get-AzureSqlDatabaseOperation –ServerName "TargetServerName" –OperationGuid $RecoveryRequest.RequestID

데이터베이스가 복구된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 방식을 구성할 수 있습니다.
###REST API 

프로그래밍 방식으로 데이터베이스 복구를 수행하려면 REST를 사용합니다.

1.	[복구 가능한 데이터베이스 나열](http://msdn.microsoft.com/library/azure/dn800984.aspx) 작업을 통해 복구 가능한 데이터베이스 목록을 가져옵니다.
	
2.	[복구 가능한 데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn800985.aspx) 작업을 통해 복구할 데이터베이스를 가져옵니다.
	
3.	[데이터베이스 복구 요청 만들기](http://msdn.microsoft.com/library/azure/dn800986.aspx) 작업을 통해 복구 요청을 만듭니다.
	
4.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 통해 복구 상태를 추적합니다.

데이터베이스가 복구된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 사용할 방식을 구성할 수 있습니다.
 

<!---HONumber=September15_HO1-->