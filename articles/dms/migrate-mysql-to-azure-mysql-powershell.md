---
title: 'PowerShell: DMS를 사용하여 MySQL 데이터베이스에서 Azure Database for MySQL로 오프라인 마이그레이션 실행'
titleSuffix: Azure Database Migration Service
description: PowerShell 스크립트를 통해 Azure Database Migration Service를 사용하여 온-프레미스 MySQL 데이터베이스에서 Azure Database for MySQL로 마이그레이션하는 방법을 알아봅니다.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: eea4a539c8a2b17a9a6280a8f847e68542d1a8d5
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950643"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>PowerShell과 Azure Database Migration Service를 사용하여 MySQL에서 Azure Database for MySQL로 오프라인 마이그레이션

이 문서에서는 Microsoft Azure PowerShell을 통해 Azure Database Migration Service의 오프라인 마이그레이션 기능을 사용하여 온-프레미스 인스턴스로 복원된 MySQL 데이터베이스에서 Azure Database for MySQL로 마이그레이션합니다. 이 문서에서는 MySQL 데이터베이스에서 Azure로 오프라인 마이그레이션을 수행하기 위해 순차적으로 실행되는 PowerShell 스크립트의 모음을 설명합니다. 이 자습서에 설명된 전체 PowerShell 스크립트는 [Github 리포지토리](https://github.com/Azure/azure-mysql/tree/master/Azure%20DMS%20-%20MySQL%20Offline%20Migration%20Script)에서 다운로드할 수 있습니다.


> [!NOTE]
> 현재는 Az.DataMigration 모듈을 사용하여 전체 데이터베이스 마이그레이션을 실행할 수 없습니다. 한편 샘플 PowerShell 스크립트는 [DMS Rest API](/rest/api/datamigration/tasks/get)를 사용하여 마이그레이션을 자동화해주며 "있는 그대로" 제공됩니다. 이 스크립트는 Az.DataMigration 모듈과 Azure CLI에 공식 지원이 추가되면 수정되거나 사용 중단될 예정입니다. 

> [!NOTE]
> MySQL용 Amazon RDS(Relational Database Service) 및 Amazon Aurora(MySQL 기반)도 마이그레이션 원본으로 지원됩니다.

> [!IMPORTANT]
> 온라인 마이그레이션의 경우에는 [데이터 입력 복제](../mysql/concepts-data-in-replication.md) 기능이 있는 [MyDumper/MyLoader](https://centminmod.com/mydumper.html)와 같은 오픈 소스 도구를 사용합니다.


이 문서는 원본 및 대상 데이터베이스 이름이 같거나 다르고 마이그레이션의 일부로 이름과 테이블 구조가 같은 대상 데이터베이스 테이블을 모두 또는 일부 마이그레이션해야 하는 시나리오를 자동화하는 데 도움이 됩니다. 이 문서에서는 원본이 MySQL 데이터베이스 인스턴스이고 대상이 Azure Database for MySQL인 것으로 가정하지만 원본 서버 이름과 자격 증명을 변경하기만 하면 같은 방법으로 서로 다른 Azure Database for MySQL 간에 마이그레이션할 수 있습니다. 또한 낮은 버전의 MySQL 서버(v5.6 이상)에서 더 높은 버전으로의 마이그레이션도 지원됩니다.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 데이터베이스 스키마를 마이그레이션합니다.
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * MySQL에 오프라인 마이그레이션 기능을 사용하는 마이그레이션 프로젝트를 구성합니다.
> * 마이그레이션을 실행합니다.

## <a name="prerequisites"></a>필수 구성 요소

이러한 단계를 완료하려면 다음이 필요합니다.

* 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free).
* 버전 5.6 이상의 온-프레미스 MySQL 데이터베이스가 있어야 합니다. 없으면 [MySQL 커뮤니티 버전](https://dev.mysql.com/downloads/mysql/) 5.6 이상을 다운로드하여 설치합니다.
* [Azure Database for MySQL에 인스턴스를 만듭니다](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Workbench 애플리케이션을 사용하여 데이터베이스를 연결하고 만드는 방법에 대한 자세한 내용은 [MySQL Workbench를 사용하여 데이터 연결 및 쿼리](../mysql/connect-workbench.md) 문서를 참조하세요. Azure Database for MySQL 버전은 온-프레미스 MySQL 버전과 같거나 그 이상이어야 합니다. 예를 들어 MySQL 5.7은 Azure Database for MySQL 5.7로 마이그레이션하거나 8로 업그레이드할 수 있습니다.  
* Azure Resource Manager 배포 모델을 사용하여 Azure Database Migration Service용 Microsoft Azure Virtual Network를 만듭니다. 그러면 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 원본 서버에 대한 사이트 간 연결이 제공됩니다. 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](../virtual-network/index.yml)를 참조하세요. 특히 단계별 세부 정보를 제공하는 빠른 시작 문서를 참조하세요.

    > [!NOTE]
    > 가상 networkNet 설정 중에 Microsoft에 대한 네트워크 피어링에서 ExpressRoute를 사용하는 경우 *Microsoft.Sql* 서비스 [엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 서비스가 프로비전되는 서브넷에 추가합니다. Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

* 가상 네트워크 Network Security Group 규칙이 Storage 및 AzureMonitor용 ServiceTag의 아웃바운드 포트 443을 차단하지 않는지 확인합니다. 가상 네트워크 NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용하여 네트워크 트래픽 필터링](../virtual-network/virtual-network-vnet-plan-design-arm.md) 문서를 참조하세요.
* Azure Database Migration Service가 원본 MySQL Server에 액세스할 수 있게 Virtual Network의 연결을 허용하도록 Windows 방화벽을 엽니다(기본적으로 TCP 포트 3306).
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우에는 Azure Database Migration Service가 마이그레이션을 위해 원본 데이터베이스에 액세스할 수 있게 Virtual Network의 연결을 허용하는 방화벽 규칙을 추가해야 합니다.
* Azure Database Migration Service의 Virtual Network가 대상 데이터베이스에 액세스할 수 있게 허용하도록 대상 Azure Database for MySQL에 대한 서버 수준 [방화벽 규칙](../azure-sql/database/firewall-configure.md)을 만들거나 [VNET 서비스 엔드포인트를 구성](../mysql/howto-manage-vnet-using-portal.md)합니다.
* 원본 MySQL은 지원되는 MySQL 커뮤니티 버전에 있어야 합니다. MySQL 유틸리티 또는 MySQL Workbench에서 MySQL 인스턴스의 버전을 확인하려면 다음 명령을 실행합니다.

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL은 InnoDB 테이블만 지원합니다. MyISAM 테이블을 InnoDB로 변환하려면 [MyISAM에서 InnoDB로 테이블 변환](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) 문서를 참조하세요.
* 사용자에게 원본 데이터베이스에서 데이터를 읽을 수 있는 권한이 있어야 합니다.
* 이 가이드에서는 [설치 가이드](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true)에 따라 설치할 수 있는 PowerShell v7.1과 PSEdition Core를 사용합니다.
* [Install-Module PowerShell cmdlet](/powershell/module/powershellget/Install-Module)을 사용하여 PowerShell 갤러리에서 다음 모듈을 다운로드 및 설치합니다. 관리자 권한으로 실행을 사용하여 PowerShell 명령 창을 열어야 합니다.
    * Az.Resources
    * Az.Network
    * Az.DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>데이터베이스 스키마 마이그레이션

테이블 스키마, 인덱스 및 저장 프로시저와 같은 모든 데이터베이스 개체를 전송하려면 원본 데이터베이스에서 스키마를 추출하여 대상 데이터베이스에 적용해야 합니다. 스키마를 추출하려면 `--no-data` 매개 변수가 있는 mysqldump를 사용할 수 있습니다. 이를 위해서는 원본 MySQL 데이터베이스와 대상 Azure Database for MySQL 모두에 연결할 수 있는 컴퓨터가 필요합니다.

mysqldump를 사용하여 스키마를 내보내려면 다음 명령을 실행합니다.

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

예를 들면 다음과 같습니다.

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

스키마를 대상 Azure Database for MySQL로 가져오려면 다음 명령을 실행합니다.

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

예를 들면 다음과 같습니다.

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

스키마에 외래 키가 있는 경우 마이그레이션 중의 병렬 데이터 로드는 마이그레이션 작업을 통해 처리됩니다. 스키마를 마이그레이션하는 동안 외래 키를 삭제할 필요는 없습니다.

데이터베이스에 트리거가 있는 경우 원본의 전체 데이터 마이그레이션에 앞서 대상에 데이터 무결성을 적용합니다. 마이그레이션 중에 대상의 모든 테이블에서 트리거를 사용하지 않도록 설정한 다음, 마이그레이션이 완료되면 트리거를 사용하도록 설정하는 것이 좋습니다.

MySQL Workbench에서 대상 데이터베이스에 다음 스크립트를 실행하여 drop trigger 스크립트와 add trigger 스크립트를 추출합니다.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

결과에서 생성된 drop trigger 쿼리(DropQuery 열)를 실행하여 대상 데이터베이스의 트리거를 삭제합니다. add trigger 쿼리는 저장했다가 데이터 마이그레이션 완료 후에 사용하면 됩니다.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

[Azure PowerShell로 로그인](/powershell/azure/authenticate-azureps)문서의 지침에 따라 PowerShell을 사용하여 [Connect-AzAccount PowerShell 명령](/powershell/module/az.accounts/connect-azaccount)으로 Azure 구독에 로그인합니다.

다음 스크립트는 PowerShell 세션 후 로그인에 대한 기본 구독을 설정하고 형식이 지정된 콘솔 로그에 대한 도우미 로깅 함수를 만듭니다.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

각 Azure 구독에서 리소스 공급자 등록을 한 번 수행해야 합니다. 등록하지 않으면 **Azure Database Migration Service** 의 인스턴스를 만들 수 없습니다.

[Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) 명령을 사용하여 리소스 공급자를 등록합니다. 다음 스크립트는 **Azure Database Migration Service** 에 필요한 리소스 공급자를 등록합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. DMS 리소스를 만들기 전에 먼저 리소스 그룹을 만듭니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹을 *미국 서부 2* 지역에 기본 구독 *mySubscription* 아래에 만듭니다.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

[New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice) 명령을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다. 이 명령에는 다음 매개 변수가 필요합니다.
* *Azure 리소스 그룹 이름*. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 앞에서와 같이 Azure 리소스 그룹을 만들고 매개 변수로 해당 이름을 제공합니다.
* *서비스 이름*. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당하는 문자열입니다. 
* *위치* - 서비스의 위치를 지정합니다. 미국 서부 또는 동남 아시아 등 Azure 데이터 센터 위치를 지정합니다.
* *SKU*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원되는 Sku 이름은 *Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores*, *Premium_4vCores* 입니다.
* *가상 서브넷 식별자*. [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) 명령을 사용하여 서브넷의 정보를 가져올 수 있습니다. 

다음 스크립트는 *myVirtualNetwork* 가상 네트워크가 *default* 라는 서브넷과 함께 존재하는 것으로 가정하고, 동일한 지역에서 **3단계** 에서 만든 리소스 그룹 아래에 *myDmService* 라는 이름의 Database Migration Service를 만듭니다.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

Azure Database Migration Service 인스턴스를 만든 후에는 마이그레이션 프로젝트를 만듭니다. 마이그레이션 프로젝트는 수행할 마이그레이션 유형을 지정합니다.

다음 스크립트는 MySQL에서 Azure Database for MySQL로 오프라인 마이그레이션을 위해 동일한 지역에서 **4단계** 에서 만든 Database Migration Service 인스턴스 아래에 *myfirstmysqlofflineproject* 라는 마이그레이션 프로젝트를 만듭니다.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>원본 및 대상 연결에 대한 데이터베이스 연결 정보 개체 만들기

마이그레이션 프로젝트를 만든 후에는 데이터베이스 연결 정보를 만듭니다. 이 연결 정보는 마이그레이션 프로세스 중에 원본 서버와 대상 서버에 연결하는 데 사용됩니다.

다음 스크립트는 원본 및 대상 MySQL 인스턴스의 서버 이름, 사용자 이름 및 암호를 받아들여 연결 정보 개체를 만듭니다. 이 스크립트는 사용자에게 원본 및 대상 MySQL 인스턴스에 대한 암호를 입력하라는 메시지를 표시합니다. 자동 스크립트의 경우 Azure Key Vault에서 자격 증명을 가져올 수 있습니다. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>대상 데이터베이스에서 테이블 이름 목록 추출

데이터베이스 테이블 목록은 마이그레이션 작업과 연결 정보를 사용하여 추출할 수 있습니다. 적절한 매핑 및 유효성 검사를 수행할 수 있도록 원본 데이터베이스와 대상 데이터베이스에서 모두 테이블 목록을 추출합니다. 

다음 스크립트는 원본 데이터베이스와 대상 데이터베이스의 이름을 받아들인 후 *GetUserTablesMySql* 마이그레이션 작업을 통해 데이터베이스에서 테이블 목록을 추출합니다. 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>사용자 구성에 따라 테이블 매핑 빌드

마이그레이션 작업을 구성하는 과정에서 원본 테이블과 대상 테이블 간의 매핑을 만들게 됩니다. 매핑은 테이블 이름 수준으로 이루어지지만 매핑되는 테이블의 테이블 구조(열 수, 열 이름, 데이터 형식 등)가 정확히 동일한 것으로 가정합니다.

다음 스크립트는 **7단계** 에서 추출된 대상 및 원본 테이블 목록을 기준으로 매핑을 만듭니다. 일부 데이터만 로드하는 경우에는 테이블 목록을 제공하여 테이블을 필터링하면 됩니다. 사용자 입력을 제공하지 않으면 모든 대상 테이블이 매핑됩니다. 또한 이 스크립트는 같은 이름을 가진 테이블이 원본에 있는지 여부를 확인합니다. 해당 테이블 이름이 원본에 없는 대상 테이블은 마이그레이션 시 무시합니다. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>마이그레이션 작업 입력 만들기 및 구성

테이블 매핑을 빌드한 후에는 *Migrate.MySql.AzureDbForMySql* 형식의 마이그레이션 작업에 대한 입력을 만들고 속성을 구성합니다.

다음 스크립트는 마이그레이션 작업을 만들고 연결, 데이터베이스 이름 및 테이블 매핑을 설정합니다.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>성능 조정 매개 변수 구성

PowerShell 모듈의 일부로 몇 가지 선택적 매개 변수를 사용할 수 있으며, 이 매개 변수는 환경에 따라 조정 가능합니다. 이러한 매개 변수를 사용하면 마이그레이션 작업의 성능을 향상시킬 수 있습니다. 이러한 매개 변수는 모두 선택 사항이며 기본값은 NULL입니다.

> [!NOTE]
> 다음 성능 구성은 프리미엄 SKU에서 마이그레이션 시 처리량을 증가시키는 것으로 나타났습니다.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 seconds
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

다음 스크립트는 매개 변수의 사용자 값을 받아들여 마이그레이션 작업 속성의 매개 변수를 설정합니다.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>마이그레이션 작업 만들기 및 실행

작업 입력을 구성한 후에는 에이전트에서 작업이 생성되고 실행됩니다. 스크립트는 작업 실행을 트리거하고 마이그레이션이 완료될 때까지 기다립니다.

다음 스크립트는 구성된 마이그레이션 작업을 호출하고 완료될 때까지 기다립니다.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Database Migration Service 삭제

여러 마이그레이션에 동일한 Database Migration Service를 사용할 수 있으므로 한 번 생성된 인스턴스를 다시 사용할 수 있습니다. Database Migration Service를 계속해서 사용하지 않을 경우 [Remove-AzDataMigrationService](/powershell/module/az.datamigration/remove-azdatamigrationservice?) 명령으로 서비스를 삭제할 수 있습니다.

다음 스크립트는 Azure Database Migration Service 인스턴스 및 연결된 프로젝트를 삭제합니다.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>다음 단계

* DMS를 사용하여 마이그레이션을 수행할 때의 알려진 문제 및 제한 사항에 대한 자세한 내용은 [일반적인 문제 - Azure Database Migration Service](./known-issues-troubleshooting-dms.md) 문서를 참조하세요.
* DMS 사용 시 원본 데이터베이스 연결 문제를 해결하려면 [원본 데이터베이스 연결 문제](./known-issues-troubleshooting-dms-source-connectivity.md) 문서를 참조하세요.
* Azure Database Migration Service에 대한 자세한 내용은 [Azure Database Migration Service란?](./dms-overview.md) 문서를 참조하세요.
* Azure Database for MySQL에 대한 자세한 내용은 [Azure Database for MySQL이란?](../mysql/overview.md) 문서를 참조하세요.
* 포털을 통해 DMS를 사용하는 방법에 대한 자습서는 [자습서: DMS를 사용하여 MySQL에서 Azure Database for MySQL로 오프라인 마이그레이션](./tutorial-mysql-azure-mysql-offline-portal.md) 문서를 참조하세요.