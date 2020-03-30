---
title: 'PowerShell: SQL 서버를 SQL 관리 인스턴스로 마이그레이션합니다.'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell 및 Azure 데이터베이스 마이그레이션 서비스를 사용하여 온-프레미스 SQL 서버에서 Azure SQL Database 관리 인스턴스로 마이그레이션하는 방법을 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 9ea9f55681b93e79eec836f5808d2c6feaa6bb29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650727"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스 & PowerShell을 사용하여 SQL Server 관리 인스턴스로 SQL 서버 마이그레이션

이 문서에서는 Microsoft Azure PowerShell을 사용하여 SQL Server 2005 이상의 온-프레미스 인스턴스로 복원된 **Adventureworks2016** 데이터베이스를 Azure Azure PowerShell을 사용하여 Azure SQL Database 관리 인스턴스로 마이그레이션합니다. Microsoft Azure PowerShell의 모듈을 사용하여 온-프레미스 SQL Server 인스턴스에서 `Az.DataMigration` Azure SQL Database 관리 인스턴스로 데이터베이스를 마이그레이션할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure 데이터베이스 마이그레이션 서비스의 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 온라인 및 오프라인 마이그레이션을 모두 수행하는 방법에 대한 자세한 내용을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상(모든](https://www.microsoft.com/sql-server/sql-server-downloads) 버전)
* **AdventureWorks2016** 데이터베이스의 로컬 복사본은 [여기에서](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)다운로드할 수 있습니다.
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure 구독 계정이 없는 경우 시작하기 전에 [무료 계정을 만드세요.](https://azure.microsoft.com/free/)
* Azure SQL Database 관리 인스턴스입니다. Azure SQL Database 관리 형 인스턴스 만들기 문서의 세부 정보를 따라 [Azure SQL Database 관리 인스턴스를](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)만들 수 있습니다.
* 데이터 마이그레이션 [도우미](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상을 다운로드하여 설치하려면.
* Azure 리소스 관리자 배포 모델을 사용하여 만든 Microsoft Azure 가상 네트워크로, [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 또는 [VPN을](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)사용하여 온-프레미스 소스 서버에 대한 사이트 간 연결을 Azure 데이터베이스 마이그레이션 서비스에 제공합니다.
* [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)문서에 설명된 대로 데이터 마이그레이션 도우미를 사용하여 온-프레미스 데이터베이스 및 스키마 마이그레이션에 대한 완료된 평가입니다.
* [설치 모듈 PowerShell cmdlet을](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)사용하여 PowerShell 갤러리에서 `Az.DataMigration` 모듈(버전 0.7.2 이상)을 다운로드하여 설치하려면.
* 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있는지 확인합니다.
* 대상 Azure SQL Database 관리 인스턴스에 연결하는 데 사용되는 자격 증명에 대상 Azure SQL Database 관리 인스턴스 데이터베이스에 대한 CONTROL 데이터베이스 권한이 있는지 확인합니다.

    > [!IMPORTANT]
    > 온라인 마이그레이션의 경우 Azure Active Directory 자격 증명을 이미 설정해야 합니다. 자세한 내용은 포털 사용 문서에서 [리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체를 만듭니다.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

PowerShell을 사용하여 Azure 구독에 로그인합니다. 자세한 내용은 [Azure PowerShell을 사용할 수 있는 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)문서를 참조하십시오.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *미국 동부* 지역에서 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다.
이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름*. 명령을 사용하여 [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 이전에 표시된 대로 Azure Resource 그룹을 만들고 해당 이름을 매개 변수로 제공할 수 있습니다.
* *서비스 이름*. Azure 데이터베이스 마이그레이션 서비스에 대 한 원하는 고유 서비스 이름에 해당 하는 문자열입니다.
* *위치*. 서비스의 위치를 지정합니다. 미국 서부 또는 동남아시아와 같은 Azure 데이터 센터 위치를 지정합니다.
* *스쿠*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원되는 Sku 이름은 *Basic_1vCore,* *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *가상 서브넷 식별자*. cmdlet을 [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) 사용하여 서브넷을 만들 수 있습니다.

다음 예제에서는 *MyVNET이라는* 가상 네트워크와 *MySubnet이라는*서브넷을 사용하여 *미국 동부* 지역에 있는 리소스 그룹 *MyDMSResourceGroup에서 MyDMS라는* 서비스를 만듭니다. *MyDMS*

> [!IMPORTANT]
> 아래 코드 코드 조각은 프리미엄 SKU를 기반으로 하는 Azure 데이터베이스 마이그레이션 서비스의 인스턴스가 필요하지 않은 오프라인 마이그레이션을 위한 것입니다. 온라인 마이그레이션의 경우 -Sku 매개 변수값에는 프리미엄 SKU가 포함되어야 합니다.

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

다음 매개 변수를 예상하는 `New-AzDmsConnInfo` cmdlet을 사용하여 데이터베이스 연결 정보 개체를 만들 수 있습니다.

* *서버 유형*. SQL, Oracle 또는 MySQL 등 요청된 데이터베이스 연결 유형입니다. SQL Server 및 Azure SQL에 대해 SQL을 사용합니다.
* *데이터 소스*. SQL Server 인스턴스 또는 Azure SQL Database 인스턴스의 이름 또는 IP입니다.
* *AuthType*. 연결에 대한 인증 유형이며 SqlAuthentication 또는 WindowsAuthentication일 수 있습니다.
* *트러스트 서버 인증서*. 이 매개 변수는 신뢰의 유효성을 검사하기 위해 인증서 체인을 우회하는 동안 채널이 암호화되었는지 여부를 나타내는 값을 설정합니다. 값은 또는 `$true` `$false`을 수 있습니다.

다음 예제는 SQL 인증을 사용하여 *MySourceSQLServer라는* 원본 SQL Server에 대한 연결 정보 개체를 만듭니다.

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

다음 예제에서는 SQL 인증을 사용하여 'targetmanagedinstance.database.windows.net'라는 Azure SQL Database 관리 인스턴스 서버에 대한 연결 정보 생성을 보여 주며 있습니다.

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>마이그레이션 프로젝트에 대한 데이터베이스 제공

Azure 데이터베이스 `AzDataMigrationDatabaseInfo` 마이그레이션 서비스 프로젝트의 일부로 데이터베이스를 지정 하는 개체 목록을 만듭니다., 프로젝트 만들기에 대 한 매개 변수로 제공 될 수 있습니다. cmdlet을 `New-AzDataMigrationDatabaseInfo` 사용하여 을 `AzDataMigrationDatabaseInfo`만들 수 있습니다.

다음 예제는 `AzDataMigrationDatabaseInfo` **AdventureWorks2016** 데이터베이스에 대한 프로젝트를 만들고 프로젝트 생성을 위한 매개 변수로 제공될 목록에 추가합니다.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>프로젝트 개체 만들기

마지막으로 *미국 동부에* 위치한 *MyDMSProject라는 Azure* 데이터베이스 마이그레이션 `New-AzDataMigrationProject` 서비스 프로젝트를 만들고 이전에 만든 원본 및 대상 연결및 마이그레이션할 데이터베이스 목록을 추가할 수 있습니다.

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

다음으로 Azure 데이터베이스 마이그레이션 서비스 작업을 만들고 시작합니다. 이 작업에는 원본과 대상 모두에 대한 연결 자격 증명 정보와 마이그레이션할 데이터베이스 테이블 목록 및 필수 구성 조건으로 만든 프로젝트와 함께 이미 제공된 정보가 필요합니다.

### <a name="create-credential-parameters-for-source-and-target"></a>원본 및 대상에 대한 자격 증명 매개 변수 만들기

연결 보안 자격 증명을 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체로 만듭니다.

다음 예제에서는 원본 및 대상 연결모두에 대한 *PSCredential* 개체를 만들어 $sourcePassword 문자열 *변수로* 암호를 제공하고 *$targetPassword.*

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>백업 파일공유 개체 만들기

이제 Azure 데이터베이스 마이그레이션 서비스에서 cmdlet을 사용하여 원본 데이터베이스 백업을 취할 수 `New-AzDmsFileShare` 있는 로컬 SMB 네트워크 공유를 나타내는 FileShare 개체를 만듭니다.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>선택한 데이터베이스 개체 만들기

다음 단계는 `New-AzDmsSelectedDB` cmdlet을 사용하여 원본 및 대상 데이터베이스를 선택하는 것입니다.

다음 예제는 SQL Server에서 Azure SQL Database 관리 인스턴스로 단일 데이터베이스를 마이그레이션하는 경우입니다.

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

전체 SQL Server 인스턴스에 Azure SQL Database 관리 인스턴스로 리프트 앤 시프트가 필요한 경우 원본에서 모든 데이터베이스를 가져올 수 있는 루프가 아래에 제공됩니다. 다음 예제에서는 $Server, $SourceUserName 및 $SourcePassword 대해 원본 SQL Server 세부 정보를 제공합니다.

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

서비스에서 백업 파일을 업로드하는 스토리지 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI가 포함된 변수를 만듭니다.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>추가 구성 요구 사항

해결해야 할 몇 가지 추가 요구 사항이 있지만 오프라인 또는 온라인 마이그레이션을 수행하는지 여부에 따라 다릅니다.

#### <a name="offline-migrations"></a>오프라인 마이그레이션

오프라인 마이그레이션의 경우 다음과 같은 추가 구성 작업을 수행합니다.

* **로그인을 선택합니다.** 다음 예제와 같이 마이그레이션할 로그인 목록을 만듭니다.

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 현재 Azure 데이터베이스 마이그레이션 서비스는 SQL 로그인 마이그레이션만 지원합니다.

* **에이전트 작업 선택**. 다음 예제와 같이 마이그레이션할 에이전트 작업 목록을 만듭니다.

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 현재 Azure 데이터베이스 마이그레이션 서비스는 T-SQL 하위 시스템 작업 단계만 지원합니다.

#### <a name="online-migrations"></a>온라인 마이그레이션

온라인 마이그레이션의 경우 다음과 같은 추가 구성 작업을 수행합니다.

* **Azure Active 디렉터리 앱 구성**

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

### <a name="create-and-start-the-migration-task"></a>마이그레이션 작업 만들기 및 시작

`New-AzDataMigrationTask` cmdlet을 사용하여 마이그레이션 작업을 만들고 시작합니다.

#### <a name="specify-parameters"></a>매개 변수 지정

##### <a name="common-parameters"></a>일반 매개 변수

`New-AzDataMigrationTask` 오프라인 또는 온라인 마이그레이션을 수행하든 관계없이 cmdlet은 다음 매개 변수를 예상합니다.

* *TaskType* SQL Server를 *MigrateSqlServerSqlDbMi* Azure SQL Database Managed Instance 마이그레이션 유형으로 만드는 마이그레이션 작업 유형이 필요합니다. 
* *리소스 그룹 이름*. 작업을 만들 Azure 리소스 그룹의 이름입니다.
* *서비스 이름*. 작업을 만들 Azure Database Migration Service 인스턴스입니다.
* *프로젝트 이름*. 작업을 만들 Azure Database Migration Service 프로젝트의 이름입니다. 
* *작업 이름*. 만들 작업의 이름입니다. 
* *SourceConnection*. 소스 SQL 서버 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *TargetConnection*. 대상 Azure SQL 데이터베이스 관리 인스턴스 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *SourceCred*. 원본 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *TargetCred*. 대상 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *SelectedDatabase*. AzDataMigration소스 및 대상 데이터베이스 매핑을 나타내는 개체입니다.
* *BackupFileShare*. Azure Database Migration Service에서 원본 데이터베이스 백업을 수행할 수 있는 로컬 네트워크 공유를 나타내는 FileShare 개체입니다.
* *BackupBlobSasUri*. 서비스에서 백업 파일을 업로드하는 스토리지 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI입니다. Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아보세요.
* *SelectedLogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
* *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

##### <a name="additional-parameters"></a>추가 매개 변수

또한 `New-AzDataMigrationTask` cmdlet은 수행 중인 마이그레이션 유형, 오프라인 또는 온라인 매개 변수에 고유한 매개 변수를 기대합니다.

* **오프라인 마이그레이션**. 오프라인 마이그레이션의 `New-AzDataMigrationTask` 경우 cmdlet은 다음 매개 변수도 예상합니다.

  * *SelectedLogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
  * *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

* **온라인 마이그레이션 .** 온라인 마이그레이션의 `New-AzDataMigrationTask` 경우 cmdlet은 다음 매개 변수도 기대합니다.

* *AzureActive디렉터리앱*. 활성 디렉터리 응용 프로그램입니다.
* *스토리지리소스 ID*. 저장소 계정의 리소스 ID입니다.

#### <a name="create-and-start-an-offline-migration-task"></a>오프라인 마이그레이션 작업 만들기 및 시작

다음 예제는 **myDMSTask라는**오프라인 마이그레이션 작업을 만들고 시작합니다.

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

#### <a name="create-and-start-an-online-migration-task"></a>온라인 마이그레이션 작업 생성 및 시작

다음 예제는 **myDMSTask라는**온라인 마이그레이션 작업을 만들고 시작합니다.

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

마이그레이션을 모니터링하려면 다음 작업을 수행합니다.

1. 모든 마이그레이션 세부 정보를 $CheckTask라는 변수로 통합합니다.

    마이그레이션과 관련된 속성, 상태 및 데이터베이스 정보와 같은 마이그레이션 세부 정보를 결합하려면 다음 코드 조각을 사용합니다.

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 변수를 `$CheckTask` 사용하여 마이그레이션 작업의 현재 상태를 가져옵니다.

    변수를 `$CheckTask` 사용하여 마이그레이션 작업의 현재 상태를 얻으려면 다음 예제와 같이 작업의 상태 속성을 쿼리하여 실행 중인 마이그레이션 작업을 모니터링할 수 있습니다.

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>컷오버 수행(온라인 마이그레이션에만 만)

온라인 마이그레이션을 사용하면 데이터베이스의 전체 백업 및 복원이 수행된 다음 BackupFileShare에 저장된 트랜잭션 로그를 복원하는 작업이 진행됩니다.

Azure SQL Database 관리 인스턴스의 데이터베이스가 최신 데이터로 업데이트되고 원본 데이터베이스와 동기화되는 경우 컷오버를 수행할 수 있습니다.

다음 예제는 컷오버\마이그레이션을 완료합니다. 사용자는 재량에 따라 이 명령을 호출합니다.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure 데이터베이스 마이그레이션 서비스의 인스턴스 삭제

마이그레이션이 완료되면 Azure 데이터베이스 마이그레이션 서비스 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>추가 리소스

추가 마이그레이션 시나리오(소스/대상 쌍)에 대한 자세한 내용은 Microsoft [데이터베이스 마이그레이션 가이드를](https://datamigration.microsoft.com/)참조하십시오.

## <a name="next-steps"></a>다음 단계

Azure 데이터베이스 마이그레이션 서비스 문서에서 Azure 데이터베이스 마이그레이션 [서비스에](https://docs.microsoft.com/azure/dms/dms-overview)대해 자세히 알아보기.
