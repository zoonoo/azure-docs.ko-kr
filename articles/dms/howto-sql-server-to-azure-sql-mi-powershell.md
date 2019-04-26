---
title: Database Migration Service 및 PowerShell을 사용하여 SQL Server를 Azure SQL Database Managed Instance로 마이그레이션 | Microsoft Docs
description: Azure PowerShell을 사용하여 온-프레미스 SQL Server에서 Azure SQL DB Managed Instance로 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: ed0d65a0f00bd5ebc3227a249beec6bafd791347
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532560"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-database-managed-instance-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure SQL Database Managed Instance로 SQL Server 온-프레미스 마이그레이션
이 문서에서는 Microsoft Azure PowerShell을 사용하여 SQL Server 2005 이상의 온-프레미스 인스턴스로 복원된 **Adventureworks2012** 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션합니다. Microsoft Azure PowerShell에서 `Az.DataMigration` 모듈을 사용하여 온-프레미스 SQL Server 인스턴스의 데이터베이스를 Azure SQL Database Managed Instance로 마이그레이션할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.

## <a name="prerequisites"></a>필수 조건
이러한 단계를 완료하려면 다음이 필요합니다.

- [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)(모든 버전)
- SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
- [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
- Azure SQL Database 인스턴스. [Azure Portal에서 Azure SQL Database 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) 문서의 세부 지침을 수행하여 Azure SQL Database 인스턴스를 만들 수 있습니다.
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상.
- Azure Resource Manager 배포 모델을 사용하여 VNET을 만들었습니다. VNET은 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용하여 Azure Database Migration Service에 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공합니다.
- 문서에 설명 된 대로 Data Migration Assistant를 사용 하 여 온-프레미스 데이터베이스 및 스키마 마이그레이션 평가 완료 하려면 [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)
- 다운로드 하 고 사용 하 여 PowerShell 갤러리에서 Az.DataMigration 모듈을 설치 [Install-module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)
- 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)(서버 제어) 권한이 있어야 합니다.
- 대상 Azure SQL DB 인스턴스에 연결하는 데 사용되는 자격 증명에 대상 Azure SQL Database 데이터베이스에 대한 CONTROL DATABASE(데이터베이스 제어) 권한이 있어야 합니다.
- Azure 구독. 구독이 없으면 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만드세요.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인
[Azure PowerShell을 사용하여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps) 문서의 지침에 따라 PowerShell을 사용하여 Azure 구독에 로그인합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기
Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신을 만들려면 먼저 리소스 그룹을 만듭니다.

사용 하 여 리소스 그룹을 만듭니다는 [새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령입니다. 

다음 예제에서는 *EastUS* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기 
`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.
- *Azure 리소스 그룹 이름*. 사용할 수 있습니다 [새로 만들기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 설명한 대로 Azure 리소스 그룹을 만들고 해당 이름을 매개 변수로 제공 하는 명령입니다.
- *서비스 이름*. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당하는 문자열입니다. 
- *위치* - 서비스의 위치를 지정합니다. 미국 서부 또는 동남 아시아 등 Azure 데이터 센터 위치를 지정합니다.
- *SKU*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원되는 Sku 이름은 *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*입니다.
- *가상 서브넷 식별자*. Cmdlet을 사용할 수 있습니다 [새로 만들기-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) 서브넷을 만들어야 합니다. 

다음 예제에서는 *MyVNET*이라는 가상 네트워크 및 *MySubnet*이라는 서브넷을 사용하여 *미국 동부* 지역에 있는 *MyDMSResourceGroup* 리소스 그룹에 *MyDMS*라는 서비스를 만듭니다.

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>원본 및 대상 연결에 대한 데이터베이스 연결 정보 개체 만들기
`New-AzDmsConnInfo` cmdlet을 사용하여 데이터베이스 연결 정보 개체를 만들 수 있습니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.
- *ServerType*. SQL, Oracle 또는 MySQL 등 요청된 데이터베이스 연결 유형입니다. SQL Server 및 Azure SQL에 대해 SQL을 사용합니다.
- *DataSource*. SQL Server 인스턴스 또는 Azure SQL 데이터베이스의 이름 또는 IP입니다.
- *AuthType*. 연결에 대한 인증 유형이며 SqlAuthentication 또는 WindowsAuthentication일 수 있습니다.
- *TrustServerCertificate* 매개 변수는 인증서 체인 검사를 무시할 때 채널의 암호화 여부를 나타내는 값을 설정하여 신뢰의 유효성을 검사합니다. 값은 True 또는 False일 수 있습니다.

다음 예제에서는 SQL 인증을 사용하여 MySourceSQLServer라는 원본 SQL Server에 대한 연결 정보 개체를 만듭니다. 

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

다음 예제에서는 SQL 인증을 사용하여 'targetmanagedinstance.database.windows.net'이라는 Azure SQL Database Managed Instance 서버에 대한 연결 정보를 만드는 방법을 보여 줍니다.

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>마이그레이션 프로젝트에 대한 데이터베이스 제공
프로젝트 만들기를 위한 매개 변수로 제공될 수 있는 Azure Database Migration 프로젝트의 일부로 데이터베이스를 지정하는 `AzDataMigrationDatabaseInfo` 개체의 목록을 만듭니다. Cmdlet은 `New-AzDataMigrationDatabaseInfo` AzDataMigrationDatabaseInfo를 만드는 데 사용할 수 있습니다. 

다음 예제에서는 **AdventureWorks** 데이터베이스에 대한 `AzDataMigrationDatabaseInfo` 프로젝트를 만들고, 프로젝트를 만들기 위한 매개 변수로 제공할 목록에 이를 추가합니다.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>프로젝트 개체 만들기
마지막으로 `New-AzDataMigrationProject`를 사용하여 이전에 만든 원본 및 대상 연결과 마이그레이션할 데이터베이스 목록을 추가하면 *미국 동부*에 있는 *MyDMSProject*라는 Azure Database Migration 프로젝트를 만들 수 있습니다.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작
마지막으로 Azure Database Migration 작업을 만들고 시작합니다. Azure Database Migration 작업은 필수 조건으로 만들어진 프로젝트와 이미 제공된 정보 외에도 원본 및 대상 모두에 대한 연결 자격 증명 정보와 마이그레이션할 데이터베이스 테이블 목록이 필요합니다. 

### <a name="create-credential-parameters-for-source-and-target"></a>원본 및 대상에 대한 자격 증명 매개 변수 만들기
[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체로 연결 보안 자격 증명을 만들 수 있습니다. 

다음 예제에서는 *$sourcePassword* 및 *$targetPassword* 문자열 변수로 암호를 제공하여 원본 및 대상 연결 모두에 대한 *PSCredential* 개체 만들기를 보여 줍니다. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>백업 파일 공유 개체 만들기
이제 파일 공유를 Azure Database Migration Service 수행 될 수 있음을 원본 데이터베이스 백업을 새 AzDmsFileShare cmdlet을 사용 하 여 로컬 SMB 네트워크 공유를 나타내는 개체를 만듭니다.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>선택한 데이터베이스 개체 만들기
다음 단계는 다음과 같습니다. 다음 예제에서와 같이 새 AzDmsSelectedDB cmdlet을 사용 하 여 원본 및 대상 데이터베이스를 선택 합니다.

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure Storage 컨테이너에 대한 SAS URI
서비스에서 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI가 포함된 변수를 만듭니다.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>로그인 선택
아래 예제와 같이 마이그레이션할 로그인 목록을 만듭니다.  참고로, 현재 DMS는 SQL 로그인 마이그레이션만 지원합니다. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>에이전트 작업 선택
아래 예제와 같이 마이그레이션할 에이전트 작업 목록을 만듭니다.

>[!NOTE]
>DMS는 현재 T-SQL 하위 시스템 작업 단계에서만 작업을 지원합니다.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작

`New-AzDataMigrationTask` cmdlet을 사용하여 마이그레이션 작업을 만들고 시작합니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.
- *TaskType* SQL Server를 *MigrateSqlServerSqlDbMi* Azure SQL Database Managed Instance 마이그레이션 유형으로 만드는 마이그레이션 작업 유형이 필요합니다. 
- *리소스 그룹 이름*. 작업을 만들 Azure 리소스 그룹의 이름입니다.
- *ServiceName*. 작업을 만들 Azure Database Migration Service 인스턴스입니다.
- *ProjectName*. 작업을 만들 Azure Database Migration Service 프로젝트의 이름입니다. 
- *TaskName*. 만들 작업의 이름입니다. 
- *SourceConnection*. 원본 SQL Server 연결을 나타내는 AzDmsConnInfo 개체입니다.
- *TargetConnection*. 대상 Azure SQL Database 관리 되는 인스턴스 연결을 나타내는 AzDmsConnInfo 개체입니다.
- *SourceCred*. 원본 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
- *TargetCred*. 대상 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
- *SelectedDatabase*. 원본 및 대상 데이터베이스 매핑을 나타내는 AzDataMigrationSelectedDB 개체입니다.
- *BackupFileShare*. Azure Database Migration Service에서 원본 데이터베이스 백업을 수행할 수 있는 로컬 네트워크 공유를 나타내는 FileShare 개체입니다.
- *BackupBlobSasUri*. 서비스에서 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI입니다. Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아보세요.
- *SelectedLogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
- *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

다음 예제에서는 myDMSTask라는 마이그레이션 작업을 만들고 시작합니다.

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>마이그레이션 모니터링
다음 예제와 같이 작업의 상태 속성을 쿼리하여 실행 중인 마이그레이션 작업을 모니터링할 수 있습니다.

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>DMS 인스턴스 삭제
마이그레이션이 완료된 후에는 Azure DMS 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-steps"></a>다음 단계
- Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.
