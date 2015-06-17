<properties 
   pageTitle="Azure PowerShell의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구" 
   description="지역에서 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, Azure PowerShell" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Azure PowerShell의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구

> [AZURE.SELECTOR]
- [지역에서 복원 - 포털](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)
- [지역에서 복원 - REST API](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/)   

## 개요

이 자습서에서는 [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스를 복구하는 방법을 설명합니다. 지역에서 복원은 Azure SQL 데이터베이스의 Basic, Standard 및 Premium 서비스 계층에 모두 포함되어 있는 핵심적인 재해 복구 보호 기능입니다.

## 제한 사항 및 보안

[Azure 포털의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-management-portal/)를 참조하세요.

## 방법: Azure PowerShell의 지역에서 복원 기능을 사용하여 Azure SQL 데이터베이스 복구

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

다음 cmdlet을 실행하려면 인증서 기반 인증을 사용해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method)의 *인증서 방법 사용* 섹션을 참조하세요.

1. [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) cmdlet을 사용하여 복구 가능한 데이터베이스 목록을 표시합니다. 이때 다음 매개 변수를 지정합니다.
	* **ServerName** - 데이터베이스의 위치입니다.	

	`PS C:>Get-AzureSqlRecoverableDatabase -ServerName "myserver"`

2. [Get-AzureSqlRecoverableDatabase](http://msdn.microsoft.com/library/azure/dn720219.aspx) cmdlet을 사용하여 복구 원본 데이터베이스를 선택합니다. 이때 다음 매개 변수를 지정합니다.
	* **ServerName** - 데이터베이스의 위치입니다.
	* **DatabaseName** - 복구 원본 데이터베이스의 이름입니다.

	`PS C:>$Database = Get-AzureSqlRecoverableDatabase -ServerName "myserver" -DatabaseName "mydb"`
	 
3. [Start-AzureSqlDatabaseRecovery](http://msdn.microsoft.com/library/dn720224.aspx) cmdlet을 사용하여 복구를 시작합니다. 이때 다음 매개 변수를 지정합니다.	
	* **SourceDatabase** - 복구하려는 데이터베이스입니다.
	* **TargetDatabaseName** - 복구 대상 데이터베이스의 이름입니다.
	* **TargetServerName** -데이터베이스를 복구할 서버의 이름입니다.

	**$RestoreRequest** 변수로 반환되는 결과를 저장합니다. 이 변수는 복원 상태를 모니터링하는 데 사용되는 복원 요청 ID를 포함합니다.

	`PS C:>$RecoveryRequest = Start-AzureSqlDatabaseRecovery -SourceDatabase $Database -TargetDatabaseName "myrecoveredDB" -TargetServerName "mytargetserver"`
	
데이터베이스 복구를 완료하려면 다소 시간이 걸릴 수 있습니다. 복구 상태를 모니터링하려면 다음 매개 변수를 지정하여 [Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) cmdlet을 사용합니다.

* **ServerName** - 복원 대상 데이터베이스의 서버 이름입니다.
* **OperationGuid** - 3단계의 **$RecoveryRequest** 변수에 저장된 복원 요청 ID입니다.

	`PS C:>Get-AzureSqlDatabaseOperation -ServerName "mytargetserver" -OperationGuid $RecoveryRequest.ID`

**State** 및 **PercentComplete** 필드에 복원 상태가 표시됩니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.  

[Azure 포털의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL 데이터베이스 지역에서 복원(블로그)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
 