<properties 
   pageTitle="Azure PowerShell에서 삭제된 Azure SQL 데이터베이스 복원" 
   description="Microsoft Azure SQL 데이터베이스, 삭제된 데이터베이스 복원, 삭제된 데이터베이스 복구, Azure PowerShell" 
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
   ms.date="10/08/2015"
   ms.author="elfish; v-romcal; v-stste"/>

# Azure PowerShell에서 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.SELECTOR]
- [Restore deleted database - portal](sql-database-restore-deleted-database-tutorial-management-portal.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## 개요

이 자습서에서는 [Azure PowerShell](../powershell-install-configure.md)에서 삭제된 Azure SQL 데이터베이스를 복원하는 방법을 설명합니다. 삭제된 데이터베이스는 보존 기간이 지나지 않은 경우 삭제된 시점으로 복원할 수 있습니다. 보존 기간은 데이터베이스의 서비스 계층에 의해 결정됩니다.

삭제된 Azure SQL 데이터베이스를 복원하면 새 데이터베이스가 작성됩니다. 서비스는 복원 지점에서 사용되는 백업을 기준으로 서비스 계층을 자동으로 선택합니다. 논리 서버에 다른 데이터베이스를 만들 수 있는 할당량이 있는지 확인하세요. 할당량 증가를 요청하려면 [Azure 지원](http://azure.microsoft.com/support/options/)에 문의하세요.

## 제한 사항 및 보안

[Azure 포털에서 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-tutorial-management-portal.md)을 참조하세요.

## 방법: Azure PowerShell에서 삭제된 Azure SQL 데이터베이스 복원

> [AZURE.VIDEO restore-a-deleted-sql-database-with-microsoft-azure-powershell]

다음 cmdlet을 실행하려면 인증서 기반 인증을 사용해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md#use-the-certificate-method)의 *인증서 방법 사용* 섹션을 참조하세요.

> [AZURE.IMPORTANT]이 문서에서는 Azure PowerShell 버전 1.0 *미만*의 명령을 포함합니다. **Get-Module azure | format-table version** 명령으로 Azure PowerShell의 버전을 확인할 수 있습니다.

1. [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546735.aspx) cmdlet을 사용하여 복구 가능한 데이터베이스 목록을 표시합니다.
	* **RestorableDropped** 스위치를 사용하여 데이터베이스를 삭제한 서버의 **ServerName**을 지정합니다.
	* 다음 명령을 실행하면 결과가 **$RecoverableDBs** 변수에 저장됩니다.
	
	`$RecoverableDBs = Get-AzureSqlDatabase -ServerName "myserver" –RestorableDropped`

2. 삭제된 데이터베이스 목록에서 복원할 삭제된 데이터베이스를 선택합니다.

	* **$RecoverableDBs** 목록에서 삭제된 데이터베이스 번호를 입력합니다.  

	`$Database = $RecoverableDBs[<deleted database number>]`

	* 복원 가능한 삭제된 데이터베이스 개체를 가져오는 방법에 대한 자세한 내용은 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/dn546735.aspx)를 참조하세요.

3. [Start-AzureSqlDatabaseRestore](http://msdn.microsoft.com/library/azure/dn720218.aspx) cmdlet을 사용하여 복원을 시작합니다. 이때 다음 매개 변수를 지정합니다.
	* **SourceRestorableDroppedDatabase**
	* **TargetDatabaseName** - 복원 대상 데이터베이스의 이름입니다.

	**$RestoreRequest**라는 변수로 반환되는 결과를 저장합니다. 이 변수는 복원 상태를 모니터링하는 데 사용되는 복원 요청 ID를 포함합니다.
	
	`$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “myrestoredDB”`

복원을 완료하려면 다소 시간이 걸릴 수 있습니다. 복원 상태를 모니터링하려면 [Get-AzureSqlDatabase](http://msdn.microsoft.com/library/azure/dn546738.aspx) cmdlet을 사용하고 다음 매개 변수를 지정합니다.

* **ServerName** - 복원 대상 데이터베이스의 서버 이름입니다.
* **OperationGuid** - 3단계의 **$RestoreRequest** 변수에 저장된 복원 요청 ID입니다.

	`Get-AzureSqlDatabaseOperation –ServerName "myserver" –OperationGuid $RestoreRequest.RequestID`

**State** 및 **PercentComplete** 필드에 복원 상태가 표시됩니다.

## 다음 단계

자세한 내용은 다음을 참조하세요.

[Azure SQL 데이터베이스 비즈니스 연속성](sql-database-business-continuity.md)

[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx)

<!---HONumber=Oct15_HO3-->