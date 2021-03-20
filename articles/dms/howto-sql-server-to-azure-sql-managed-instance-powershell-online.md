---
title: 'PowerShell: 온라인으로 SQL Server SQL Managed Instance 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell 및 Azure Database Migration Service를 사용 하 여 SQL Server에서 Azure SQL Managed Instance로의 온라인 마이그레이션에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697827"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>PowerShell &를 사용 하 여 온라인으로 SQL Managed Instance SQL Server 마이그레이션 Azure Database Migration Service

이 문서에서는 Microsoft Azure PowerShell를 사용 하 여 SQL Server 2005 이상의 온-프레미스 인스턴스로 복원 된 **Adventureworks2016** 데이터베이스를 AZURE sql sql Managed Instance로 온라인으로 마이그레이션합니다. Microsoft Azure PowerShell에서 모듈을 사용 하 여 SQL Server 인스턴스에서 SQL Managed Instance로 데이터베이스를 마이그레이션할 수 있습니다 `Az.DataMigration` .

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service의 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 온라인으로 마이그레이션을 실행 합니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 온라인 마이그레이션에 대 한 단계를 제공 하지만 [오프 라인](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)으로 마이그레이션할 수도 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads) (모든 버전)
* [여기](/sql/samples/adventureworks-install-configure)에서 다운로드할 수 있는 **AdventureWorks2016** 데이터베이스의 로컬 복사본입니다.
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* SQL Managed Instance입니다. [ASQL Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md)문서의 세부 정보에 따라 SQL Managed Instance를 만들 수 있습니다.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 이상을 다운로드 하 여 설치 합니다.
* [Express](../expressroute/expressroute-introduction.md) 경로 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Database Migration Service를 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 만든 Microsoft Azure Virtual Network입니다.
* [SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem)문서에 설명 된 대로 Data Migration Assistant를 사용 하 여 온-프레미스 데이터베이스 및 스키마 마이그레이션의 평가를 완료 했습니다.
* `Az.DataMigration` [Install-module PowerShell cmdlet](/powershell/module/powershellget/Install-Module)을 사용 하 여 PowerShell 갤러리에서 모듈 (버전 0.7.2 이상)을 다운로드 하 고 설치 합니다.
* 원본 SQL Server 인스턴스에 연결 하는 데 사용 되는 자격 증명에 [CONTROL Server](/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있는지 확인 합니다.
* 대상 SQL Managed Instance에 연결 하는 데 사용 되는 자격 증명에 대상 SQL Managed Instance 데이터베이스에 대 한 CONTROL DATABASE 권한이 있는지 확인 합니다.

    > [!IMPORTANT]
    > 온라인 마이그레이션의 경우 이미 Azure Active Directory 자격 증명을 설정 해야 합니다. 자세한 내용은 [포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md)문서를 참조 하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

명령을 사용 하 여 리소스 그룹을 만듭니다 [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) .

다음 예제에서는 *미국 동부* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>DMS의 인스턴스를 만듭니다.

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다.
이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름* 입니다. [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup)명령을 사용 하 여 이전에 표시 된 대로 Azure 리소스 그룹을 만들고 해당 이름을 매개 변수로 제공할 수 있습니다.
* *서비스 이름* 입니다. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당 하는 문자열입니다.
* *위치* - 서비스의 위치를 지정합니다. 미국 서 부 또는 동남 아시아와 같은 Azure 데이터 센터 위치를 지정 합니다.
* *Sku*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원 되는 Sku 이름은 *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores* 입니다.
* *가상 서브넷 식별자*. Cmdlet을 사용 하 여 [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) 서브넷을 만들 수 있습니다.

다음 예제에서는 *Mydms* 이라는 가상 네트워크와 *mydms* 이라는 서브넷을 사용 하 여 *미국 동부* 지역에 있는 *Mydmsresourcegroup* 리소스 그룹에 *mydms* 라는 서비스를 만듭니다.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

Azure Database Migration Service 인스턴스를 만든 후 마이그레이션 프로젝트를 만듭니다. Azure Database Migration Service 프로젝트는 프로젝트의 일부로 마이그레이션할 데이터베이스의 목록뿐만 아니라 원본 및 대상 인스턴스 모두에 대한 연결 정보가 필요합니다.
원본 및 대상 연결 문자열을 정의 합니다.

다음 스크립트는 원본 SQL Server 연결 정보를 정의 합니다. 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

다음 스크립트는 대상 SQL Managed Instance 연결 정보를 정의 합니다. 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>원본 및 대상 데이터베이스 매핑 정의

이 마이그레이션 프로젝트에서 마이그레이션할 데이터베이스를 지정 하십시오.

다음 스크립트는 제공 된 이름을 사용 하 여 원본 데이터베이스를 대상 SQL Managed Instance의 해당 새 데이터베이스에 매핑합니다.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

여러 데이터베이스의 경우 다음 형식을 사용 하 여 위의 스크립트에 데이터베이스 목록을 추가 합니다.

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS 프로젝트 만들기

DMS 인스턴스 내에 Azure Database Migration Service 프로젝트를 만들 수 있습니다.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>백업 파일 공유 개체 만들기

이제 Azure Database Migration Service New-AzDmsFileShare cmdlet을 사용 하 여 원본 데이터베이스 백업을 수행할 수 있는 로컬 SMB 네트워크 공유를 나타내는 파일 공유 개체를 만듭니다.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Azure Storage 정의 

마이그레이션에 사용할 Azure Storage 컨테이너를 선택 합니다. 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory 앱 구성

온라인 SQL Managed Instance 마이그레이션의 Azure Active Directory에 필요한 세부 정보를 제공 합니다. 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작

다음으로 Azure Database Migration Service 작업을 만들고 시작 합니다. 변수를 사용 하 여 소스와 대상을 호출 하 고 마이그레이션할 데이터베이스 테이블을 나열 합니다. 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

다음 예에서는 온라인 마이그레이션 작업을 만들고 시작 합니다. 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

자세한 내용은 [AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask)를 참조 하세요.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

마이그레이션을 모니터링 하려면 다음 작업을 수행 합니다.

### <a name="check-the-status-of-task"></a>작업 상태 확인

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

다음을 사용 하 여 오류 목록을 가져옵니다.

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>가공선 수행 

온라인 마이그레이션을 사용 하 여 데이터베이스의 전체 백업 및 복원이 수행 된 다음 BackupFileShare 공유에 저장 된 트랜잭션 로그를 복원 하는 작업을 진행 합니다.

Azure SQL Managed Instance 데이터베이스를 최신 데이터로 업데이트 하 고 원본 데이터베이스와 동기화 하는 경우에는 조치를 수행할 수 있습니다.

다음 예에서는 cutover\migration.을 완료 합니다. 사용자는 신중 하 게이 명령을 호출 합니다.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스를 삭제 하는 중

마이그레이션이 완료 되 면 Azure Database Migration Service 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>추가 리소스

추가 마이그레이션 시나리오 (원본/대상 쌍)에 대 한 자세한 내용은 Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

[Azure Database Migration Service 이란?](./dms-overview.md)문서에서 Azure Database Migration Service에 대해 자세히 알아보세요.