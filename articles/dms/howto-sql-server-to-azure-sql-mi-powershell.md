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
ms.date: 04/29/2019
ms.openlocfilehash: d83410efd26f8c2078d3abdb01d061db0b83d33d
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233722"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>SQL Server 온-프레미스 Azure PowerShell을 사용 하 여 Azure SQL Database 관리 되는 인스턴스로 마이그레이션
이 문서에서는 마이그레이션 합니다 **Adventureworks2016** 데이터베이스 SQL Server 2005의 온-프레미스 인스턴스로 복원 하거나 위의 Azure SQL Database 관리 되는 인스턴스 Microsoft Azure PowerShell을 사용 하 여 합니다. 사용 하 여 Azure SQL Database 관리 되는 인스턴스에 데이터베이스를 온-프레미스 SQL Server 인스턴스에서 마이그레이션할 수는 `Az.DataMigration` Microsoft Azure PowerShell 모듈.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서는 온라인 및 오프 라인 마이그레이션을 수행 하는 방법에 대 한 세부 정보를 포함 합니다.

## <a name="prerequisites"></a>필수 조건

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads) (모든 버전).
* 로컬 복사본을 **AdventureWorks2016** 다운로드할 수 있는 데이터베이스 [여기](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)합니다.
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure 구독. 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* Azure SQL Database 관리 되는 인스턴스. 문서의 세부 지침을 수행 하 여 Azure SQL Database 관리 되는 인스턴스를 만들 수 있습니다 [Azure SQL Database 관리 되는 인스턴스를 만들](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)합니다.
* 다운로드 및 설치 [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상.
* Azure Database Migration Service를 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 사용 하 여 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 만든 Azure Virtual Network (VNet) [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 나 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)합니다.
* 이 문서에 설명 된 대로 Data Migration Assistant를 사용 하 여 온-프레미스 데이터베이스 및 스키마 마이그레이션 완료 된 평가 [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)합니다.
* 다운로드 및 설치 합니다 `Az.DataMigration` 모듈 (버전 0.7.2입니다 이상)를 사용 하 여 PowerShell 갤러리에서 [Install-module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)합니다.
* 원본 SQL Server 인스턴스에 연결 하는 데 자격 증명이 있는지 확인 하는 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한.
* 관리 되는 인스턴스는 대상 Azure SQL Database에 연결 하는 데 사용 하는 자격 증명을 확인 하는 대상 Azure SQL Database 관리 되는 인스턴스 데이터베이스에 CONTROL DATABASE 권한이 있습니다.

    > [!IMPORTANT]
    > 이미 해야 online 마이그레이션에 대 한 Azure Active Directory 자격 증명 설정 합니다. 자세한 내용은 문서를 참조 [포털을 사용 하 여 Azure AD 리소스에 액세스할 수 있는 응용 프로그램 및 서비스 주체를 만들려면](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)합니다.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

PowerShell을 사용 하 여 Azure 구독에 로그인 합니다. 자세한 내용은 문서 참조 [Azure PowerShell을 사용 하 여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

사용 하 여 리소스 그룹을 만듭니다는 [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령입니다.

다음 예제에서는 명명 된 리소스 그룹을 만듭니다 *myResourceGroup* 에 *미국 동부* 지역입니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service의 인스턴스를 만듭니다

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다.
이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름*. 사용할 수 있습니다 [ `New-AzResourceGroup` ](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 설명한 대로 Azure 리소스 그룹을 만들고 해당 이름을 매개 변수로 제공 하는 명령입니다.
* *서비스 이름*. Azure Database Migration Service에 대 한 원하는 고유 서비스 이름에 해당 하는 문자열입니다.
* *위치* - 서비스의 위치를 지정합니다. 미국 서 부 또는 동남 아시아 등 Azure 데이터 센터 위치를 지정 합니다.
* *SKU*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원 되는 Sku 이름은 *Basic_1vCore*를 *Basic_2vCores*하십시오 *GeneralPurpose_4vCores*합니다.
* *가상 서브넷 식별자*. Cmdlet을 사용할 수 있습니다 [ `New-AzVirtualNetworkSubnetConfig` ](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) 서브넷을 만들어야 합니다.

다음 예제에서는 명명 된 서비스를 만듭니다 *MyDMS* 리소스 그룹에서 *MyDMSResourceGroup* 에 *미국 동부* 이라는가상네트워크를사용하여지역 *MyVNET* 이라는 서브넷 *MySubnet*합니다.

> [!IMPORTANT]
> 아래 코드 조각 프리미엄 SKU에 따라 Azure Database Migration Service의 인스턴스를 요구 하지 않는 오프 라인 마이그레이션,입니다. 온라인 마이그레이션에 대 한-Sku 매개 변수 값에는 Premium SKU를 포함 해야 합니다.

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

사용 하 여 데이터베이스 연결 정보 개체를 만들 수 있습니다는 `New-AzDmsConnInfo` cmdlet에 다음 매개 변수가 필요 합니다.

* *ServerType*. SQL, Oracle 또는 MySQL 등 요청된 데이터베이스 연결 유형입니다. SQL Server 및 Azure SQL에 대해 SQL을 사용합니다.
* *DataSource*. 이름 또는 SQL Server 인스턴스 또는 Azure SQL Database 인스턴스의 IP입니다.
* *AuthType*. 연결에 대한 인증 유형이며 SqlAuthentication 또는 WindowsAuthentication일 수 있습니다.
* *TrustServerCertificate*. 이 매개 변수는 신뢰의 유효성을 검사 하 고 인증서 체인을 무시 하는 동안 채널 암호화 되는지 여부를 나타내는 값을 설정 합니다. 값은 `$true` 또는 `$false`합니다.

다음 예제에서는 SQL Server 호출 하는 원본에 대 한 연결 정보 개체를 만듭니다 *MySourceSQLServer* sql 인증을 사용 하 여:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

다음 예제에서는 라는 'targetmanagedinstance.database.windows.net' sql 인증을 사용 하는 Azure SQL Database 관리 되는 인스턴스 서버에 대 한 연결 정보 만드는 방법을 보여 줍니다.

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>마이그레이션 프로젝트에 대한 데이터베이스 제공

목록 만들기 `AzDataMigrationDatabaseInfo` 프로젝트 생성에 대 한 매개 변수로 제공 될 수 있는 Azure Database Migration Service 프로젝트의 일부로 데이터베이스를 지정 하는 개체입니다. Cmdlet을 사용할 수 있습니다 `New-AzDataMigrationDatabaseInfo` 만들려면 `AzDataMigrationDatabaseInfo`합니다.

다음 예제에서는 합니다 `AzDataMigrationDatabaseInfo` 에 대 한 프로젝트를 **AdventureWorks2016** 데이터베이스 및 프로젝트 만들기에 대 한 매개 변수로 제공 되는 목록에 추가 합니다.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>프로젝트 개체 만들기

마지막으로 호출 하는 Azure Database Migration Service 프로젝트를 만들 수 있습니다 *MyDMSProject* 에 있는 *미국 동부* 사용 하 여 `New-AzDataMigrationProject` 이전에 만든된 원본 및 대상 연결을 추가 하며 마이그레이션할 데이터베이스의 목록입니다.

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

다음으로, 만들기 및 Azure Database Migration Service 작업을 시작 합니다. 이 작업에는 모두 원본 및 대상으로 마이그레이션할 데이터베이스 테이블의 목록에 대 한 연결 자격 증명 정보 및 필수 조건으로 만든 프로젝트를 사용 하 여 이미 제공 되는 정보에 필요 합니다.

### <a name="create-credential-parameters-for-source-and-target"></a>원본 및 대상에 대한 자격 증명 매개 변수 만들기

연결 보안 자격 증명을 만듭니다는 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.

다음 예제를 만드는 방법을 보여 줍니다 *PSCredential* 문자열 변수로 암호를 제공 하는 원본 및 대상 연결에 대 한 개체 *$sourcePassword* 고 *$ targetPassword*합니다.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>백업 파일 공유 개체 만들기

이제는 Azure Database Migration Service 백업을 수행할 수는 원본 데이터베이스를 사용 하 여 로컬 SMB 네트워크 공유를 나타내는 파일 공유를 만들는 `New-AzDmsFileShare` cmdlet.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>선택한 데이터베이스 개체 만들기

다음 단계를 사용 하 여 원본 및 대상 데이터베이스를 선택 하는 것은 `New-AzDmsSelectedDB` cmdlet.

다음 예제는 Azure SQL Database 관리 되는 인스턴스로 SQL Server에서 단일 데이터베이스를 마이그레이션하는:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

전체 SQL Server 인스턴스를 해야 하는 경우를 리프트 앤 시프트를 Azure SQL Database로 관리 되는 인스턴스를 다음 원본에서 모든 데이터베이스를 사용 하는 루프를 아래에 제공 됩니다. 다음 예에서 $Server, $SourceUserName, 및 $SourcePassword를 자세히 설명 원본 SQL Server 합니다.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure Storage 컨테이너에 대한 SAS URI

서비스에서 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI가 포함된 변수를 만듭니다.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>추가 구성 요구 사항

를 처리 해야 할 몇 가지 추가 요구 사항이 있지만 오프 라인 또는 온라인 마이그레이션을 수행 하는 여부에 따라 다릅니다.

#### <a name="offline-migrations"></a>오프 라인 마이그레이션

오프 라인 마이그레이션의 경우 다음 추가 구성 작업을 수행 합니다.

* **로그인 선택**합니다. 다음 예제에서와 같이 마이그레이션해야 하는 로그인의 목록을 만듭니다.

    ```powershell
    $selectedLogins = @(“user1”, “user2”)
    ```

    > [!IMPORTANT]
    > 현재, Azure Database Migration Service 마이그레이션 SQL 로그인만 지원합니다.

* **에이전트 작업을 선택**합니다. 다음 예제에서와 같이 마이그레이션해야 하는 작업 에이전트의 목록에 만듭니다.

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 현재, Azure Database Migration Service는만 T-SQL 하위 시스템 작업 단계를 사용 하 여 작업을 지원합니다.

#### <a name="online-migrations"></a>온라인 마이그레이션

온라인 마이그레이션에 해당, 다음 추가 구성 작업을 수행 합니다.

* **Azure Active Directory 앱 구성**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **저장소 리소스 구성**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>만들고 마이그레이션 작업 시작

`New-AzDataMigrationTask` cmdlet을 사용하여 마이그레이션 작업을 만들고 시작합니다.

#### <a name="specify-parameters"></a>매개 변수 지정

##### <a name="common-parameters"></a>일반 매개 변수

오프 라인 또는 온라인 마이그레이션을 수행 하는 여부에 관계 없이 `New-AzDataMigrationTask` cmdlet에 다음 매개 변수:

* *TaskType* SQL Server를 *MigrateSqlServerSqlDbMi* Azure SQL Database Managed Instance 마이그레이션 유형으로 만드는 마이그레이션 작업 유형이 필요합니다. 
* *리소스 그룹 이름*. 작업을 만들 Azure 리소스 그룹의 이름입니다.
* *ServiceName*. 작업을 만들 Azure Database Migration Service 인스턴스입니다.
* *ProjectName*. 작업을 만들 Azure Database Migration Service 프로젝트의 이름입니다. 
* *TaskName*. 만들 작업의 이름입니다. 
* *SourceConnection*. 원본 SQL Server 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *TargetConnection*. 대상 Azure SQL Database 관리 되는 인스턴스 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *SourceCred*. 원본 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *TargetCred*. 대상 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *SelectedDatabase*. 원본 및 대상 데이터베이스 매핑을 나타내는 AzDataMigrationSelectedDB 개체입니다.
* *BackupFileShare*. Azure Database Migration Service에서 원본 데이터베이스 백업을 수행할 수 있는 로컬 네트워크 공유를 나타내는 FileShare 개체입니다.
* *BackupBlobSasUri*. 서비스에서 백업 파일을 업로드하는 저장소 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI입니다. Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아보세요.
* *SelectedLogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
* *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

##### <a name="additional-parameters"></a>추가 매개 변수

`New-AzDataMigrationTask` cmdlet에는 또한 마이그레이션의: 오프 라인 또는 온라인으로 수행 되는 형식에 고유한 매개 변수가 필요 합니다.

* **오프 라인 마이그레이션을**합니다. 오프 라인 마이그레이션에 대 한는 `New-AzDataMigrationTask` cmdlet에는 또한 다음 매개 변수가 필요 합니다.

  * *SelectedLogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
  * *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

* **온라인 마이그레이션**합니다. 온라인 마이그레이션에 대 한는 `New-AzDataMigrationTask` cmdlet에는 또한 다음 매개 변수가 필요 합니다.

* *AzureActiveDirectoryApp*. Active Directory 응용 프로그램입니다.
* *StorageResourceID*. 저장소 계정의 리소스 ID입니다.

#### <a name="create-and-start-an-offline-migration-task"></a>만들고 오프 라인 마이그레이션 작업을 시작 합니다.

다음 예제에서는 만들고 명명 된 오프 라인 마이그레이션 작업을 시작 **myDMSTask**:

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

#### <a name="create-and-start-an-online-migration-task"></a>만들고 온라인 마이그레이션 작업을 시작 합니다.

다음 예제에서는 만들고 라는 온라인 마이그레이션 작업을 시작 **myDMSTask**:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
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
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

마이그레이션을 모니터링 하려면 다음 작업을 수행 합니다.

1. $CheckTask 라는 변수에 모든 마이그레이션 세부 정보를 통합 합니다.

    속성, 상태 및 마이그레이션과 관련 된 데이터베이스 정보 같은 마이그레이션 세부 정보를 결합 하려면 다음 코드 조각을 사용 합니다.

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 사용 된 `$CheckTask` 변수 마이그레이션 작업의 현재 상태를 가져옵니다.

    사용 하 여 `$CheckTask` 마이그레이션 작업의 현재 상태를 가져오려면 변수를 모니터링할 수 있습니다 다음 예제에서와 같이 작업의 상태 속성을 쿼리하여 실행 중인 마이그레이션 작업:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      write-host "migration task running"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      write-host "Migration task is completed Successfully"
    }
    Else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation"  -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      write-host “Migration Task Failed”
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>중단 (온라인 마이그레이션이)를 수행합니다.

온라인 마이그레이션 인를 사용 하 여 전체 백업 및 복원 데이터베이스의이 수행 되 고는 BackupFileShare에 저장 된 트랜잭션 로그 복원 작업을 진행 하는 다음입니다.

Azure SQL Database 관리 되는 인스턴스에 있는 데이터베이스는 최신 데이터를 사용 하 여 업데이트 되 고 원본 데이터베이스와 동기화 되어, 경우 단독형을 수행할 수 있습니다.

다음 예제는 cutover\migration을 완료 됩니다. 사용자가 자체 판단에이 명령을 호출합니다.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스를 삭제합니다.

마이그레이션이 완료 되 면 Azure Database Migration Service 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>추가 리소스

추가 마이그레이션 시나리오 (원본/대상 쌍)에 대 한 자세한 내용은 Microsoft을 참조 하세요 [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)합니다.

## <a name="next-steps"></a>다음 단계

문서에서 Azure Database Migration Service에 대해 자세히 알아봅니다 [Azure Database Migration Service 란?](https://docs.microsoft.com/azure/dms/dms-overview)합니다.
