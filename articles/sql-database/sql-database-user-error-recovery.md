<properties 
   pageTitle="SQL 데이터베이스 사용자 오류 복구" 
   description="Azure SQL 데이터베이스의 PITR(지정 시간 복원) 기능을 사용하여 사용자 오류, 실수로 인한 데이터 손상 또는 삭제된 데이터베이스를 복구하는 방법을 알아봅니다." 
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
   ms.date="10/08/2015"
   ms.author="elfish"/>

# 사용자 오류에서 Azure SQL 데이터베이스 복구

Azure SQL 데이터베이스는 사용자 오류나 의도하지 않은 데이터 수정에서 복구할 수 있는 두 가지 핵심 기능을 제공합니다.

- 지정 시간 복원 
- 삭제된 데이터베이스 복원

이러한 기능에 대한 자세한 내용은 이 [블로그 게시물](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)을 참조하세요.

Azure SQL 데이터베이스는 항상 새로운 데이터베이스로 복원됩니다. 이러한 복원 기능은 모든 기본, 표준 및 프리미엄 데이터베이스에 대해 제공됩니다.
##지정 시간 복원 기능으로 복구
사용자 오류나 의도하지 않은 데이터 수정 시, 지정 시간 복원 기능을 사용하여 데이터베이스 보존 기간 내의 지정된 임의의 시간으로 데이터베이스를 복원할 수 있습니다.

기본 데이터베이스는 7일 동안 보존되고 표준 데이터베이스는 14일 동안, 프리미엄 데이터베이스는 35일 동안 보존됩니다. 데이터베이스 보존에 대해 자세히 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 읽어 보시기 바랍니다.

> [AZURE.NOTE]데이터베이스를 복원하면 새 데이터베이스가 생성됩니다. 복원하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)하여 이 할당량을 늘리도록 요청할 수 있습니다.

###Azure 포털
1. [Azure 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다.
4. 데이터베이스의 블레이드 맨 위에서 **복원**을 선택합니다.
5. 데이터베이스 이름, 시간을 지정하고 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 화면 왼쪽의 **알림**을 사용하여 모니터링할 수 있습니다.

복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.
###PowerShell
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 PowerShell을 사용합니다.

> [AZURE.IMPORTANT]이 문서에서는 Azure PowerShell 버전 1.0 *미만*의 명령을 포함합니다. **Get-Module azure | format-table version** 명령으로 Azure PowerShell의 버전을 확인할 수 있습니다.

지정 시간 복원으로 데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) cmdlet을 사용합니다. 자세한 연습에 대해서는 [방법 비디오](http://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/)를 참조하세요.

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

###REST API 
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1. [데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn505708.aspx) 작업을 통해 복원할 데이터베이스를 가져옵니다.

2.	[데이터베이스 복원 요청 만들기](http://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 통해 복원 요청을 만듭니다.
	
3.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 통해 복원 요청을 추적합니다.

복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

##삭제된 데이터베이스 복구
데이터베이스가 삭제된 경우, Azure SQL 데이터베이스를 사용하면 삭제된 데이터베이스를 지정된 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터베이스는 데이터베이스 보존 기간 동안 삭제된 데이터베이스의 백업을 저장합니다.

삭제된 데이터베이스의 보존 기간은 해당 데이터베이스가 존재했던 서비스 계층 또는 데이터베이스의 존재 일 수 중 더 작은 일 수에 의해 결정됩니다. 데이터베이스 보존에 대해 자세히 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 읽어 보시기 바랍니다.

> [AZURE.NOTE]데이터베이스를 복원하면 새 데이터베이스가 생성됩니다. 복원하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락](http://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)하여 이 할당량을 늘리도록 요청할 수 있습니다.

###Azure 포털
1. [Azure 포털](https://portal.Azure.com)에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버**를 선택합니다.
3. 사용자의 서버로 이동한 후 선택합니다.
4. 서버 블레이드의 **작업**에서 **삭제된 데이터베이스**를 선택합니다.
5. 복원할 삭제된 데이터베이스를 선택합니다.
6. 데이터베이스 이름을 지정하고 **만들기**를 클릭합니다.
7. 데이터베이스 복원 프로세스가 시작되며 화면 왼쪽의 **알림**을 사용하여 모니터링할 수 있습니다.

복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

###PowerShell
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 PowerShell을 사용합니다.

삭제된 데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396) cmdlet을 사용합니다. 자세한 연습에 대해서는 [방법 비디오](http://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/)를 참조하세요.

1. 삭제된 데이터베이스 목록에서 삭제된 데이터베이스와 삭제 날짜를 찾습니다.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. 특정 삭제된 데이터베이스를 가져와서 복원을 시작합니다.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 
복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

###REST API 
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1.	[복원 가능한 삭제된 데이터베이스 나열](http://msdn.microsoft.com/library/azure/dn509562.aspx) 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
	
2.	[복원 가능한 삭제된 데이터베이스 가져오기](http://msdn.microsoft.com/library/azure/dn509574.aspx) 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.

3.	[데이터베이스 복원 요청 만들기](http://msdn.microsoft.com/library/azure/dn509571.aspx) 작업을 사용하여 복원을 시작합니다.
	
4.	[데이터베이스 작업 상태](http://msdn.microsoft.com/library/azure/dn720371.aspx) 작업을 사용하여 복원 상태를 추적합니다.

복원이 완료된 후 [복구된 데이터베이스 마무리](sql-database-recovered-finalize.md) 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.
 

<!---HONumber=Oct15_HO3-->