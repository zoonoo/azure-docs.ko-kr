---
title: 'PowerShell: SQL Server를 SQL Managed Instance로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell 및 Azure Database Migration Service를 사용하여 SQL Server에서 Azure SQL Managed Instance로 온라인 마이그레이션을 수행하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 8947a9bb62306d3497e3888c15ea6f82d82d056e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110702862"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>PowerShell 및 Azure Database Migration Service를 사용하여 SQL Server에서 SQL Managed Instance로 온라인 마이그레이션

이 문서에서는 Microsoft Azure PowerShell을 사용하여 SQL Server 2005 이상의 온-프레미스 인스턴스로 복원된 **Adventureworks2016** 데이터베이스를 Azure SQL Managed Instance로 온라인 마이그레이션합니다. Microsoft Azure PowerShell에서 `Az.DataMigration` 모듈을 사용하여 SQL Server 인스턴스의 데이터베이스를 SQL Managed Instance로 마이그레이션할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 생성합니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 온라인 마이그레이션을 실행합니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 온라인 마이그레이션에 대한 단계를 제공하지만 [오프라인](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md) 마이그레이션도 가능합니다.


## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)(모든 버전).
* **AdventureWorks2016** 데이터베이스의 로컬 복사본으로, [여기](/sql/samples/adventureworks-install-configure)에서 다운로드할 수 있습니다.
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* SQL Managed Instance. [ASQL Managed Instance 만들기](../azure-sql/managed-instance/instance-create-quickstart.md) 문서의 세부 정보에 따라 SQL Managed Instance를 만들 수 있습니다.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드 및 설치합니다.
* Azure Resource Manager 배포 모델을 사용하여 만든 Microsoft Azure Virtual Network는 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 사이트 간 연결되는 Azure Database Migration Service를 제공합니다.
* 문서 [ SQL Server 마이그레이션 평가 수행](/sql/dma/dma-assesssqlonprem)에 설명된 대로 Data Migration Assistant를 사용하여 온-프레미스 데이터베이스 및 스키마 마이그레이션의 평가를 완료했습니다.
* PowerShell 갤러리에서 [Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module)을 사용하여 `Az.DataMigration` 모듈(버전 0.7.2 이상)을 다운로드 및 설치합니다.
* 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에 [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)(서버 제어) 권한이 있어야 합니다.
* 대상 SQL Managed Instance에 연결하는 데 사용되는 자격 증명에 대상 SQL Managed Instance 데이터베이스에 대한 CONTROL DATABASE(데이터베이스 제어) 권한이 있어야 합니다.

    > [!IMPORTANT]
    > 온라인 마이그레이션의 경우 Azure Active Directory 자격 증명을 미리 설정해야 합니다. 자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md) 문서를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 ‘미국 동부’ 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>DMS 인스턴스 만들기

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다.
이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름*. [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 앞에서와 같이 Azure 리소스 그룹을 만들고 그 이름을 매개 변수로 제공합니다.
* *서비스 이름*. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당하는 문자열입니다.
* *위치* - 서비스의 위치를 지정합니다. 미국 서부 또는 동남 아시아 등 Azure 데이터 센터 위치를 지정합니다.
* *SKU*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원되는 SKU 이름은 *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores* 입니다.
* *가상 서브넷 식별자*. [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet을 사용하여 서브넷을 만들 수 있습니다.

다음 예제에서는 *MyVNET* 이라는 가상 네트워크 및 *MySubnet* 이라는 서브넷을 사용하여 ‘미국 동부’ 지역에 있는 *MyDMSResourceGroup* 리소스 그룹에 *MyDMS* 라는 서비스를 만듭니다.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 생성

Azure Database Migration Service 인스턴스를 만든 후 마이그레이션 프로젝트를 만듭니다. Azure Database Migration Service 프로젝트는 프로젝트의 일부로 마이그레이션할 데이터베이스의 목록뿐만 아니라 원본 및 대상 인스턴스 모두에 대한 연결 정보가 필요합니다.
원본 및 대상 연결 문자열을 정의합니다.

다음 스크립트는 원본 SQL Server 연결 세부 정보를 정의합니다. 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

다음 스크립트는 대상 SQL Managed Instance 연결 세부 정보를 정의합니다. 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>원본 및 대상 데이터베이스 매핑 정의

이 마이그레이션 프로젝트에서 마이그레이션할 데이터베이스 제공

다음 스크립트는 제공된 이름을 사용하여 대상 SQL Managed Instance의 새로운 데이터베이스마다 원본 데이터베이스를 매핑합니다.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

여러 데이터베이스의 경우 다음 형식을 사용하여 위의 스크립트에 데이터베이스 목록을 추가합니다.

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS 프로젝트 만들기

DMS 인스턴스 내에서 Azure Database Migration Service 프로젝트를 만들 수 있습니다.

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



### <a name="create-a-backup-fileshare-object"></a>백업 FileShare 개체 만들기

이제 Azure Database Migration Service에서 New-AzDmsFileShare cmdlet을 사용하여 원본 데이터베이스 백업을 수행할 수 있는 로컬 SMB 네트워크 공유를 나타내는 FileShare 개체를 만듭니다.

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

마이그레이션에 사용할 Azure Storage 컨테이너를 선택합니다. 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory 앱 구성

SQL Managed Instance 온라인 마이그레이션을 위해 Azure Active Directory에 필요한 세부 정보를 제공합니다. 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작

다음으로, Azure Database Migration Service 작업을 만들고 시작합니다. 변수를 사용하여 원본 및 대상을 호출하고 마이그레이션할 데이터베이스 테이블을 나열합니다. 


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

다음 예제에서는 온라인 마이그레이션 작업을 만들고 시작합니다. 

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

자세한 내용은 [New-AzDataMigrationTask ](/powershell/module/az.datamigration/new-azdatamigrationtask)를 참조하세요.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

마이그레이션을 모니터링하려면 다음 작업을 수행합니다.

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

다음을 사용하여 오류 목록을 가져옵니다.

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

## <a name="performing-the-cutover"></a>중단 수행 

온라인 마이그레이션을 통해 데이터베이스의 전체 백업 및 복원을 수행한 다음 BackupFileShare에 저장된 트랜잭션 로그에 대한 복원 작업을 진행합니다.

Azure SQL Managed Instance 데이터베이스가 최신 데이터로 업데이트되고 원본 데이터베이스와 동기화될 때 중단을 수행할 수 있습니다.

다음 예제에서는 cutover\migration을 수행합니다. 사용자는 재량에 따라 이 명령을 호출합니다.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 삭제

마이그레이션이 완료된 후에는 Azure Database Migration Service 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>추가 리소스

추가 마이그레이션 시나리오(원본/대상 쌍)에 대한 자세한 내용은 Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](./dms-overview.md) 문서를 참조하세요.
