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
   ms.date="06/26/2015"
   ms.author="sahajs"/>

# SQL 데이터 웨어하우스의 사용자 오류에서 데이터베이스 복구

SQL 데이터 웨어하우스는 의도하지 않은 데이터 손상 또는 삭제를 유발 하는 사용자 오류에서 복구하기 위한 두 가지 핵심 기능을 제공합니다.

- 라이브 데이터베이스 복원
- 삭제된 데이터베이스 복원

두 기능 모두 동일한 서버의 새 데이터베이스로 복원합니다.

## 라이브 데이터베이스 복구
의도하지 않은 데이터 수정을 유발하는 사용자 오류가 발생한 경우, 데이터베이스를 보존 기간 이내의 임의 복원 지점으로 복원할 수 있습니다. 라이브 데이터베이스에 대한 데이터베이스 스냅숏은 8시간마다 생성되며 7일 동안 유지됩니다.

### PowerShell

프로그래밍 방식으로 데이터베이스 복원을 수행하려면 PowerShell을 사용합니다. 데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore][] cmdlet을 사용합니다.

1. 복원할 데이터베이스가 포함된 계정의 구독을 선택 합니다.
2. 데이터베이스에 대한 복원 지점 나열(Azure 리소스 관리 모드 필요)
3. RestorePointCreationDate를 사용하여 원하는 복원 지점을 선택합니다.
3. 데이터베이스를 원하는 복원 지점으로 복원합니다.
4. 복원의 진행률을 모니터링합니다.

```
Select-AzureSubscription -SubscriptionId <Subscription_GUID>

# List database restore points
Switch-AzureMode AzureResourceManager
Get-AzureSqlDatabaseRestorePoints -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>" -ResourceGroupName "<YourResourceGroupName>"

# Pick desired restore point using RestorePointCreationDate
$PointInTime = "<RestorePointCreationDate>"

# Get the specific database to restore
Switch-AzureMode AzureServiceManagement
$Database = Get-AzureSqlDatabase -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>"

# Restore database
$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceServerName "<YourServerName>" -SourceDatabase $Database -TargetDatabaseName "<NewDatabaseName>" -PointInTime $PointInTime

# Monitor progress of restore operation
Get-AzureSqlDatabaseOperation -ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

### REST API
프로그래밍 방식으로 데이터베이스 복원을 수행하려면 REST를 사용합니다.

1. 데이터베이스 복원 지점 가져오기 작업을 사용하여 데이터베이스 복원 지점 목록을 가져옵니다.
2. [데이터베이스 복원 요청 만들기][] 작업을 사용하여 복원을 시작합니다.
3. [데이터베이스 작업 상태][] 작업을 사용하여 복원 상태를 추적합니다.

복원이 완료된 후 [복구된 데이터베이스 마무리][] 가이드에 따라 복구된 데이터베이스를 사용하도록 구성할 수 있습니다.

## 삭제된 데이터베이스 복구
데이터베이스가 삭제된 경우, 삭제된 데이터베이스를 삭제 시점으로 복원할 수 있습니다. Azure SQL 데이터 웨어하우스는 데이터베이스가 삭제되기 전에 데이터베이스 스냅숏을 생성하여 7일 동안 유지합니다.

### PowerShell
프로그래밍 방식으로 삭제된 데이터베이스 복원을 수행하려면 PowerShell을 사용합니다. 삭제된 데이터베이스를 복원하려면 [Start-AzureSqlDatabaseRestore][] cmdlet을 사용합니다.

1. 삭제된 데이터베이스 목록에서 삭제된 데이터베이스와 삭제 날짜를 찾습니다.

```
Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>"
```

2. 특정 삭제된 데이터베이스를 가져와서 복원을 시작합니다.

```
$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "<YourServerName>" –DatabaseName "<YourDatabaseName>" -DeletionDate "1/01/2015 12:00:00 AM"

$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName "<NewDatabaseName>"

Get-AzureSqlDatabaseOperation –ServerName "<YourServerName>" –OperationGuid $RestoreRequest.RequestID
```

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
[Start-AzureSqlDatabaseRestore]: https://msdn.microsoft.com/ko-kr/library/dn720218.aspx

<!--Other Web references-->

<!---HONumber=July15_HO4-->