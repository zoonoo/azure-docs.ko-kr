---
title: 유지 관리 기간 구성(미리 보기)
description: Azure SQL Database, 탄력적 풀 및 관리되는 인스턴스 데이터베이스에서 계획된 유지 관리를 수행해야 하는 시간을 설정하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 8688458d85084f3d3dab4678fa91ed827a337739
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047354"
---
# <a name="configure-maintenance-window-preview"></a>유지 관리 기간 구성(미리 보기)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


리소스 생성 중이거나 리소스 생성 후에 언제든지 Azure SQL Database, 탄력적 풀 또는 Azure SQL Managed Instance 데이터베이스에 대한 [유지 관리 기간(미리 보기)](maintenance-window.md)을 구성합니다. 

*시스템 기본* 유지 관리 기간은 매일 오후 5시 ~ 오전 8시(리소스가 있는 Azure 지역의 현지 시간)로 바쁜 업무 시간의 중단을 방지합니다. *시스템 기본* 유지 관리 기간이 최적의 시기가 아닌 경우 사용 가능한 다른 유지 관리 기간 중 하나를 선택합니다.

모든 서비스 수준 또는 모든 지역에 대해 다른 유지 관리 기간을 변경하는 기능을 사용할 수 없습니다. 가용성에 대한 자세한 내용은 [유지 보수 기간 가용성](maintenance-window.md#availability)을 참조하세요.

> [!Important]
> 유지 관리 기간을 구성하는 작업은 Azure SQL 리소스의 서비스 계층을 변경하는 것과 유사한 장기 실행 비동기 작업입니다. 리소스는 작업이 끝날 때 발생하는 짧은 재구성을 제외하고 작업 중에 사용할 수 있으며 일반적으로 중단된 장기 실행 트랜잭션의 경우에도 최대 8초 동안 지속됩니다. 재구성의 영향을 최소화하려면 사용량이 많은 시간 외에 작업을 수행해야 합니다.

## <a name="configure-maintenance-window-during-database-creation"></a>데이터베이스를 만드는 동안 유지 관리 기간 구성 

# <a name="portal"></a>[포털](#tab/azure-portal)

데이터베이스, 탄력적 풀 또는 관리되는 인스턴스를 만들 때 유지 관리 기간을 구성하려면 **추가 설정** 페이지에서 원하는 **유지 관리 기간** 을 설정합니다. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>단일 데이터베이스 또는 탄력적 풀을 만드는 동안 유지 관리 기간 설정

새 데이터베이스 또는 풀을 만드는 방법에 대한 단계별 정보는 [Azure SQL Database 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="데이터베이스 추가 설정 만들기 탭":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>관리되는 인스턴스를 만드는 동안 유지 관리 기간 설정

새 관리되는 인스턴스를 만드는 방법에 대한 단계별 정보는 [Azure SQL Managed Instance 만들기](../managed-instance/instance-create-quickstart.md)를 참조하세요.

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="관리되는 인스턴스 추가 설정 만들기 탭":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 예에서는 Azure PowerShell을 사용하여 유지 관리 기간을 구성하는 방법을 보여 줍니다. [Azure PowerShell을 설치](/powershell/azure/install-az-ps)하거나 Azure Cloud Shell을 사용할 수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.


## <a name="discover-available-maintenance-windows"></a>사용 가능한 유지 관리 기간 검색

유지 관리 기간을 설정할 때 각 지역에는 데이터베이스 또는 풀이 있는 지역의 시간대에 해당하는 자체 유지 관리 기간 옵션이 있습니다. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL Database 및 탄력적 풀 유지 관리 기간 검색 

다음 예에서는 [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet을 사용하여 *eastus2* 지역에 대해 사용 가능한 유지 관리 기간을 반환합니다. 데이터베이스 및 탄력적 풀의 경우 `MaintenanceScope`를 `SQLDB`로 설정합니다.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL Managed Instance 유지 관리 기간 검색 

다음 예에서는 [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet을 사용하여 *eastus2* 지역에 대해 사용 가능한 유지 관리 기간을 반환합니다. 관리되는 인스턴스의 경우 `MaintenanceScope`을 `SQLManagedInstance`로 설정하세요.

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>단일 데이터베이스를 만드는 동안 유지 관리 기간 설정

다음 예에서는 새 데이터베이스를 만들고 [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet을 사용하여 유지 관리 기간을 설정합니다. `-MaintenanceConfigurationId`는 데이터베이스 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>탄력적 풀을 만드는 동안 유지 관리 기간 설정

다음 예에서는 새 탄력적 풀을 만들고 [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) cmdlet을 사용하여 유지 관리 기간을 설정합니다. 유지 관리 기간은 탄력적 풀에 설정되므로 풀의 모든 데이터베이스에는 풀의 유지 관리 기간 일정이 있습니다. `-MaintenanceConfigurationId`는 풀 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>관리되는 인스턴스를 만드는 동안 유지 관리 기간 설정

다음 예에서는 새 관리되는 인스턴스를 만들고 [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) cmdlet을 사용하여 유지 관리 기간을 설정합니다. 유지 관리 기간은 인스턴스에 설정되므로 인스턴스의 모든 데이터베이스에는 인스턴스의 유지 관리 기간 일정이 있습니다. `-MaintenanceConfigurationId`의 경우 *MaintenanceConfigName* 은 인스턴스 지역에 유효한 값이어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

다음 예에서는 Azure CLI를 사용하여 유지 관리 기간을 구성하는 방법을 보여 줍니다. [Azure CLI를 설치](/cli/azure/install-azure-cli)하거나 Azure Cloud Shell을 사용할 수 있습니다. 

Azure CLI를 사용하여 유지 관리 기간을 구성하는 것은 SQL Managed Instance에만 사용할 수 있습니다.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/cli](https://shell.azure.com/cli)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

## <a name="discover-available-maintenance-windows"></a>사용 가능한 유지 관리 기간 검색

유지 관리 기간을 설정할 때 각 지역에는 데이터베이스 또는 풀이 있는 지역의 시간대에 해당하는 자체 유지 관리 기간 옵션이 있습니다.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL Database 및 탄력적 풀 유지 관리 기간 검색

다음 예는 [az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) 명령을 사용하여 *eastus2* 지역에 대해 사용 가능한 유지 관리 기간을 반환합니다. 데이터베이스 및 탄력적 풀의 경우 `maintenanceScope`를 `SQLDB`로 설정합니다.

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL Managed Instance 유지 관리 기간 검색

다음 예는 [az maintenance public-configuration list](/cli/azure/ext/maintenance/maintenance/public-configuration#ext_maintenance_az_maintenance_public_configuration_list) 명령을 사용하여 *eastus2* 지역에 대해 사용 가능한 유지 관리 기간을 반환합니다. 관리되는 인스턴스의 경우 `maintenanceScope`을 `SQLManagedInstance`로 설정합니다.

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>단일 데이터베이스를 만드는 동안 유지 관리 기간 설정

다음 예는 새 데이터베이스를 만들고 [az sql db create](/cli/azure/sql/db#az_sql_db_create) 명령을 사용하여 유지 관리 기간을 설정합니다. `--maint-config-id`(또는 `-m`)는 데이터베이스 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>탄력적 풀을 만드는 동안 유지 관리 기간 설정

다음 예에서는 새 탄력적 풀을 만들고 [az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet을 사용하여 유지 관리 기간을 설정합니다. 유지 관리 기간은 탄력적 풀에 설정되므로 풀의 모든 데이터베이스에는 풀의 유지 관리 기간 일정이 있습니다. `--maint-config-id`(또는 `-m`)는 풀 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>관리되는 인스턴스를 만드는 동안 유지 관리 기간 설정

다음 예에서는 새 관리되는 인스턴스를 만들고 [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)를 사용하여 유지 관리 기간을 설정합니다. 유지 관리 기간은 인스턴스에 설정되므로 인스턴스의 모든 데이터베이스에는 인스턴스의 유지 관리 기간 일정이 있습니다. *MaintenanceConfigName* 은 인스턴스 지역에 유효한 값이어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>기존 데이터베이스에 대한 유지 관리 기간 구성


유지 관리 기간 선택을 데이터베이스에 적용할 때 Azure가 필요한 변경 사항을 적용하기 때문에 경우에 따라 간단한 다시 구성(몇 초)이 발생할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계에서는 Azure Portal을 사용하여 기존 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스에 대한 유지 관리 기간을 설정합니다.


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>기존 데이터베이스 또는 탄력적 풀에 대한 유지 관리 기간 설정

1. 유지 관리 기간을 설정하려는 SQL Database 또는 탄력적 풀로 이동합니다.
1. **설정** 메뉴에서 **유지 관리** 를 선택한 다음 원하는 유지 관리 기간을 선택합니다.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL Database 유지 관리 페이지":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>기존 관리되는 인스턴스에 대한 유지 관리 기간 설정

1. 유지 관리 기간을 설정하려는 관리되는 인스턴스로 이동합니다.
1. **설정** 메뉴에서 **유지 관리** 를 선택한 다음 원하는 유지 관리 기간을 선택합니다.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL Managed Instance 유지 관리 페이지":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>기존 데이터베이스에 대한 유지 관리 기간 설정

다음 예에서는 [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet을 사용하여 기존 데이터베이스에 유지 관리 기간을 설정합니다. `-MaintenanceConfigurationId`는 데이터베이스 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>기존 탄력적 풀에서 유지 관리 기간 설정

다음 예에서는 [Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) cmdlet을 사용하여 기존 탄력적 풀에 유지 관리 기간을 설정합니다. `$maintenanceConfig` 값이 풀 지역에 유효한 값인지 확인하는 것이 중요합니다.  지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>기존 관리되는 인스턴스에서 유지 관리 기간 설정

다음 예에서는 [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) cmdlet을 사용하여 기존 관리되는 인스턴스에 유지 관리 기간을 설정합니다. `$maintenanceConfig` 값이 인스턴스 지역에 유효한 값이어야 합니다.  지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

다음 예에서는 Azure CLI를 사용하여 유지 관리 기간을 구성하는 방법을 보여 줍니다. [Azure CLI를 설치](/cli/azure/install-azure-cli)하거나 Azure Cloud Shell을 사용할 수 있습니다.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>기존 데이터베이스에 대한 유지 관리 기간 설정

다음 예는 [az sql db update](/cli/azure/sql/db#az_sql_db_update) 명령을 사용하여 기존 데이터베이스에 유지 보수 기간을 설정합니다. `--maint-config-id`(또는 `-m`)는 데이터베이스 지역에 유효한 값으로 설정되어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>기존 탄력적 풀에서 유지 관리 기간 설정

다음 예에서는 [az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) 명령을 사용하여 기존 탄력적 풀에 유지 관리 기간을 설정합니다. `maintenanceConfig` 값이 풀 지역에 유효한 값인지 확인하는 것이 중요합니다.  지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>기존 관리되는 인스턴스에서 유지 관리 기간 설정

다음 예는 [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)를 사용하여 유지 보수 기간을 설정합니다. 유지 관리 기간은 인스턴스에 설정되므로 인스턴스의 모든 데이터베이스에는 인스턴스의 유지 관리 기간 일정이 있습니다. `-MaintenanceConfigurationId`의 경우 *MaintenanceConfigName* 은 인스턴스 지역에 유효한 값이어야 합니다. 지역에 대한 유효한 값을 얻으려면 [사용 가능한 유지 관리 기간 검색](#discover-available-maintenance-windows)을 참조하세요.

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>리소스 정리

불필요한 리소스를 완료한 후에 불필요한 리소스를 삭제하여 불필요한 요금을 방지하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 더 이상 필요하지 않은 SQL Database 또는 탄력적 풀로 이동합니다.
1. **개요** 메뉴에서 리소스 삭제 옵션을 선택합니다.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>다음 단계

- 유지 관리 기간에 대한 자세한 내용은 [유지 관리 기간(미리 보기)](maintenance-window.md)을 참조하세요.
- 자세한 내용은 [유지 관리 기간 FAQ](maintenance-window-faq.yml)를 참조하세요.
- 성능 최적화에 대한 자세한 내용은 [Azure SQL Database 및 Azure SQL Managed Instance의 모니터링 및 성능 튜닝](monitor-tune-overview.md)을 참조하세요.
