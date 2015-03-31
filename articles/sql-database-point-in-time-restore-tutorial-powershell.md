<properties 
   pageTitle="Azure PowerShell의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원" 
   description="지정 시간 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, Azure PowerShell" 
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

# Azure PowerShell의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [지정 시간 복원 - 포털](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)
- [지정 시간 복원 - REST API](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## 개요

이 자습서에서는 [Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스를 복원하는 방법을 설명합니다. Azure SQL 데이터베이스에는 Basic, Standard 및 Premium 서비스 계층의 셀프서비스 지정 시간 복원을 지원하기 위한 기본 제공 백업 기능이 있습니다.

지정 시간 복원을 수행하면 새 데이터베이스가 작성됩니다. 서비스는 복원 지점에서 사용되는 백업을 기준으로 서비스 계층을 자동으로 선택합니다. 논리 서버에 다른 데이터베이스를 만들 수 있는 할당량이 있는지 확인하세요. 할당량 증가를 요청하려면 [Azure 지원](http://azure.microsoft.com/support/options/)에 문의하세요.

## 제한 사항 및 보안

[Azure 포털의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)을 참조하세요.

## 방법: Azure PowerShell의 지정 시간 복원 기능을 사용하여 Azure SQL 데이터베이스 복원

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore-With-Microsoft-Azure-PowerShell/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

다음 cmdlet을 실행하려면 인증서 기반 인증을 사용해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#use-the-certificate-method)의 *Use the certificate method* 섹션을 참조하세요.

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) cmdlet을 사용하여 복원하려는 데이터베이스를 선택합니다. 이때 다음 매개 변수를 지정합니다.
	* **ServerName** - 데이터베이스의 위치입니다.
	* **DatabaseName** - 복원할 데이터베이스의 이름입니다.	

	`PS C:\>$Database = Get-AzureSqlDatabase -ServerName "myserver" -DatabaseName "mydb"`

2. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) cmdlet을 사용하여 복원을 시작합니다. 이때 다음 매개 변수를 지정합니다.	
	* **SourceDatabase** - 복원 원본 데이터베이스입니다.
	* **TargetDatabaseName** - 복원 대상 데이터베이스의 이름입니다.
	* **PointInTime** - 복원하려는 지정 시간입니다.

	**$RestoreRequest** 변수로 반환되는 결과를 저장합니다. 이 변수는 복원 상태를 모니터링하는 데 사용되는 복원 요청 ID를 포함합니다. 

	`PS C:\>$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database -TargetDatabaseName "myrestoredDB" -PointInTime "2015-01-01 06:00:00"`

복원을 완료하려면 다소 시간이 걸릴 수 있습니다. 복원 상태를 모니터링하려면 다음 매개 변수를 지정하여 [Get AzureSqlDatabaseOperation](http://msdn.microsoft.com/library/azure/dn546738.aspx) cmdlet을 사용합니다.

* **ServerName** - 복원 대상 데이터베이스의 서버 이름입니다.
* **OperationGuid** - 2단계의 **$RestoreRequest** 변수에 저장된 복원 요청 ID입니다.

	`PS C:\>Get-AzureSqlDatabaseOperation -ServerName "myserver" -OperationGuid $RestoreRequest.RequestID`

**State** 및 **PercentComplete** 필드에 복원 상태가 표시됩니다. 

## 다음 단계

자세한 내용은 다음을 참조하세요.  

[Azure SQL 데이터베이스 비즈니스 연속성](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Azure SQL 데이터베이스 백업 및 복원](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Azure SQL 데이터베이스 지정 시간 복원(블로그)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

[Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

<!--HONumber=47-->
