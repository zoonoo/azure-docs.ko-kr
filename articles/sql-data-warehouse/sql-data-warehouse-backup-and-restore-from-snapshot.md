<properties
   pageTitle="SQL 데이터 웨어하우스 | Microsoft Azure의 사용자 오류에서 데이터베이스 복구"
   description="SQL 데이터 웨어하우스의 사용자 오류에서 데이터베이스를 복구하기 위한 단계"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 사용자 오류에서 데이터베이스 복구

SQL 데이터 웨어하우스는 의도하지 않은 데이터 손상 또는 삭제를 유발 하는 사용자 오류에서 복구하기 위한 두 가지 핵심 기능을 제공합니다.

- 라이브 데이터베이스 복원
- 삭제된 데이터베이스 복원

두 기능 모두 동일한 서버의 새 데이터베이스로 복원합니다. 복원하는 서버에 새 데이터베이스를 위한 충분한 DTU 용량이 있는지 확인하는 것이 중요합니다. [지원 센터에 연락][]하여 이 할당량을 늘리도록 요청할 수 있습니다.


## 라이브 데이터베이스 복구
Azure SQL 데이터 웨어하우스 서비스는 적어도 8시간마다 데이터베이스 스냅숏을 사용하여 모든 라이브 데이터베이스를 보호하고 7일 동안 복원 지점의 불연속 집합 제공을 유지합니다. 또한 데이터베이스 스냅숏은 데이터베이스를 일시 중지하거나 삭제한 경우에 만들어지며 7일 동안 보존됩니다. 의도하지 않은 데이터 수정을 유발하는 사용자 오류가 발생한 경우, 데이터베이스를 보존 기간 이내의 임의 복원 지점으로 복원할 수 있습니다.


### Azure 포털

Azure 포털을 사용하여 복원하려면 다음 단계를 사용하세요.

1. [Azure 포털][]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 데이터베이스**를 선택합니다.
3. 사용자의 데이터베이스로 이동한 후 선택합니다.
4. 데이터베이스 블레이드의 위쪽에서 **복원**을 클릭합니다.
5. 새 **데이터베이스 이름**을 지정하고 **복원 지점**을 선택한 다음 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.


### PowerShell

프로그래밍 방식으로 데이터베이스 복원을 수행하려면 Azure PowerShell을 사용합니다. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. Get-Module -ListAvailable -Name AzureRM.Sql을 실행하여 버전을 확인할 수 있습니다. 이 문서는 Microsoft AzureRM.Sql PowerShell 버전 1.0.5를 기반으로 합니다.

데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 데이터베이스의 복원 지점을 나열합니다.
5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.
6. 데이터베이스를 원하는 복원 지점으로 복원합니다.
7. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>").RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" 

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] foo.database.windows.net 서버의 경우 위의 PowerShell cmdlet에서 -ServerName으로 "foo"를 사용합니다.


### REST API
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.


>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.


## 삭제된 데이터베이스 복구
Azure SQL 데이터 웨어하우스는 데이터베이스가 삭제되기 전에 데이터베이스 스냅숏을 생성하여 7일 동안 유지합니다. 데이터베이스가 실수로 삭제된 경우, 삭제된 데이터베이스를 삭제 시점으로 복원할 수 있습니다.


### Azure 포털

Azure 포털을 사용하여 삭제된 데이터베이스를 복원하려면 다음 단계를 사용하세요.

1. [Azure 포털][]에 로그인합니다.
2. 화면 왼쪽에서 **찾아보기**를 선택한 다음 **SQL 서버**를 선택합니다.
3. 사용자의 서버로 이동한 후 선택합니다.
4. 서버 블레이드에서 작업까지 아래로 스크롤하여 **삭제된 데이터베이스** 타일을 클릭합니다.
5. 복원할 삭제된 데이터베이스를 선택합니다.
5. 새 **데이터베이스 이름**을 지정하고 **만들기**를 클릭합니다.
6. 데이터베이스 복원 프로세스가 시작되며 **알림**을 사용하여 모니터링할 수 있습니다.


### PowerShell
프로그래밍 방식으로 삭제된 데이터베이스 복원을 수행하려면 Azure PowerShell을 사용합니다. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. Get-Module -ListAvailable -Name AzureRM.Sql을 실행하여 버전을 확인할 수 있습니다. 이 문서는 Microsoft AzureRM.Sql PowerShell 버전 1.0.5를 기반으로 합니다.

삭제된 데이터베이스를 복원하려면 [Restore-AzureRmSqlDatabase][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 삭제된 데이터베이스가 포함된 구독을 선택합니다.
4. 삭제된 특정 데이터베이스를 가져옵니다.
5. 삭제된 데이터베이스를 복원합니다.
6. 복원된 데이터베이스가 온라인 상태인지 확인합니다.

```Powershell

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "<NewDatabaseName>" –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] foo.database.windows.net 서버의 경우 위의 PowerShell cmdlet에서 -ServerName으로 "foo"를 사용합니다.


### REST API
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1.	[복원 가능한 삭제된 데이터베이스 나열][] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2.	[복원 가능한 삭제된 데이터베이스 가져오기][] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3.	[데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
4.	[데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.


>[AZURE.NOTE] 복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 구성할 수 있습니다.


## 다음 단계
Azure SQL 데이터베이스 버전의 비즈니스 연속성 기능에 대해 알아보려면 [Azure SQL 데이터베이스 비즈니스 연속성 개요][]를 읽으세요.


<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 비즈니스 연속성 개요]: sql-database/sql-database-business-continuity.md
[복구된 데이터베이스 마무리]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[데이터베이스 복원 요청 만들기]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[데이터베이스 작업 상태]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[복원 가능한 삭제된 데이터베이스 가져오기]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[복원 가능한 삭제된 데이터베이스 나열]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure 포털]: https://portal.azure.com/
[지원 센터에 연락]: https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/

<!---HONumber=AcomDC_0406_2016-->