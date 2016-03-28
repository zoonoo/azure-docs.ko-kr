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
   ms.date="03/03/2016"
   ms.author="sahajs;barbkess;sonyama"/>

# SQL 데이터 웨어하우스의 사용자 오류에서 데이터베이스 복구

SQL 데이터 웨어하우스는 의도하지 않은 데이터 손상 또는 삭제를 유발 하는 사용자 오류에서 복구하기 위한 두 가지 핵심 기능을 제공합니다.

- 라이브 데이터베이스 복원
- 삭제된 데이터베이스 복원

두 기능 모두 동일한 서버의 새 데이터베이스로 복원합니다.

SQL 데이터 웨어하우스 데이터베이스 복원: Azure PowerShell 및 REST API를 지원하는 두 가지 다른 API가 있습니다. 둘 중 하나를 사용하여 SQL 데이터 웨어하우스 복원 기능에 액세스할 수 있습니다.

## 라이브 데이터베이스 복구
의도하지 않은 데이터 수정을 유발하는 사용자 오류가 발생한 경우, 데이터베이스를 보존 기간 이내의 임의 복원 지점으로 복원할 수 있습니다. 라이브 데이터베이스에 대한 데이터베이스 스냅숏은 적어도 8시간마다 생성되며 7일 동안 유지됩니다.

### PowerShell

프로그래밍 방식으로 데이터베이스 복원을 수행하려면 Azure PowerShell을 사용합니다. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다. Get-Module -ListAvailable -Name Azure를 실행하여 버전을 확인할 수 있습니다. 이 문서는 Microsoft Azure PowerShell 버전 1.0.4를 기반으로 합니다.

데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore][] cmdlet을 사용합니다.

1. Windows PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 데이터베이스가 포함된 구독을 선택합니다.
4. 데이터베이스에 대한 복원 지점을 나열합니다(Azure 리소스 관리 모드 필요).
5. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.
6. 데이터베이스를 원하는 복원 지점으로 복원합니다.
7. 복원의 진행률을 모니터링합니다.

```

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>").RestorePointCreationDate)[-10 .. -1]

	# Or for all restore points
	Get-AzureRmSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database name to restore
(Get-AzureRmSqlDatabase -ServerName "<YourServerName>" -ResourceGroupName "<YourResourceGroupName>").DatabaseName | where {$_ -ne "master" }
#or
Get-AzureRmSqlDatabase -ServerName "<YourServerName>" –ResourceGroupName "<YourResourceGroupName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabaseName "<YourDatabaseName>" -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

서버가 foo.database.windows.net인 경우 위의 PowerShell cmdlet에서 -ServerName으로 "foo"를 사용합니다.

### REST API
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

## 삭제된 데이터베이스 복구
데이터베이스가 삭제된 경우, 삭제된 데이터베이스를 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터 웨어하우스는 데이터베이스가 삭제되기 전에 데이터베이스 스냅숏을 생성하여 7일 동안 유지합니다.

### PowerShell
프로그래밍 방식으로 삭제된 데이터베이스 복원을 수행하려면 Azure PowerShell을 사용합니다. Azure PowerShell 모듈을 다운로드하려면 [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행합니다.

삭제된 데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore][] cmdlet을 사용합니다.

1. Microsoft Azure PowerShell을 엽니다.
2. Azure 계정에 연결하고 사용자 계정과 연결된 모든 구독을 나열합니다.
3. 복원할 삭제된 데이터베이스가 포함된 구독을 선택합니다.
4. 삭제된 데이터베이스 목록에서 데이터베이스와 삭제 날짜를 찾습니다.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

5. 특정 삭제된 데이터베이스를 가져와서 복원을 시작합니다.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

서버가 foo.database.windows.net인 경우 위의 PowerShell cmdlet에서 -ServerName으로 "foo"를 사용합니다.

### REST API
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1.	[복원 가능한 삭제된 데이터베이스 나열][] 작업을 사용하여 복원 가능한 모든 삭제된 데이터베이스를 나열합니다.
2.	[복원 가능한 삭제된 데이터베이스 가져오기][] 작업을 사용하여 복원하려는 삭제된 데이터베이스에 대한 세부 정보를 가져옵니다.
3.	[데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
4.	[데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.


## 다음 단계
다른 Azure SQL 데이터베이스 버전의 비즈니스 연속성 기능에 대해 알아보려면 [Azure SQL 데이터베이스 비즈니스 연속성 개요][]를 읽으세요.


<!--Image references-->

<!--Article references-->
[Azure SQL 데이터베이스 비즈니스 연속성 개요]: sql-database/sql-database-business-continuity.md
[복구된 데이터베이스 마무리]: sql-database/sql-database-recovered-finalize.md

<!--MSDN references-->
[데이터베이스 복원 요청 만들기]: http://msdn.microsoft.com/library/azure/dn509571.aspx
[데이터베이스 작업 상태]: http://msdn.microsoft.com/library/azure/dn720371.aspx
[복원 가능한 삭제된 데이터베이스 가져오기]: http://msdn.microsoft.com/library/azure/dn509574.aspx
[복원 가능한 삭제된 데이터베이스 나열]: http://msdn.microsoft.com/library/azure/dn509562.aspx
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=AcomDC_0316_2016-->