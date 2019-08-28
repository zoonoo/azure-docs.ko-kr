---
title: '자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 단일 데이터베이스 추가 | Microsoft Docs'
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 장애 조치 (failover) 그룹에 Azure SQL Database 단일 데이터베이스를 추가 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 6cf688750ac73763c7f0da4eea152cf6bf0c8285
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935011"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 단일 데이터베이스 추가

Azure SQL Database 단일 데이터베이스에 대 한 장애 조치 그룹을 구성 하 고 Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 장애 조치 (failover)를 테스트 합니다.  이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다.
> - 두 논리 SQL server 간에 단일 데이터베이스에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

## <a name="prerequisites"></a>필수 구성 요소

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
이 자습서를 완료하려면 다음이 설치되어 있어야 합니다. 

- Azure 구독. 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) .


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
자습서를 완료 하려면 다음 항목이 있는지 확인 합니다.

- Azure 구독. 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
자습서를 완료 하려면 다음 항목이 있는지 확인 합니다.

- Azure 구독. 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) .
- 최신 버전의 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)입니다. 

---

## <a name="1---create-a-single-database"></a>1-단일 데이터베이스 만들기 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-장애 조치 (failover) 그룹 만들기 
이 단계에서는 기존 Azure SQL server와 다른 지역의 새 Azure SQL server 간에 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다. 그런 다음 장애 조치(failover) 그룹에 샘플 데이터베이스를 추가합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
장애 조치 (failover) 그룹을 만들고 Azure Portal를 사용 하 여 단일 데이터베이스를 추가 합니다. 


1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에 있는 **모든 서비스** 를 선택 합니다. 
1. 검색 `sql servers` 상자에을 입력 합니다. 
1. 필드 Sql server에서 즐겨 찾는 **sql** server 옆의 별표 아이콘을 선택 하 고 왼쪽의 탐색 창에 추가 합니다. 
    
    ![SQL Server 찾기](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. **SQL 서버** 를 선택 하 고 섹션 1에서 만든 서버 (예: `mysqlserver`)를 선택 합니다.
1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 **그룹 추가** 를 선택 하 여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 (failover) 그룹 추가](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **장애 조치 (Failover) 그룹** 페이지에서 다음 값을 입력 하거나 선택한 다음 **만들기**를 선택 합니다.
    - **장애 조치 (Failover) 그룹 이름**: 고유한 장애 조치 (failover) 그룹 이름 (예 `failovergrouptutorial`:)을 입력 합니다. 
    - **보조 서버**: *필수 설정을 구성* 하는 옵션을 선택한 다음 **새 서버를 만들도록**선택 합니다. 또는 기존 서버를 보조 서버로 선택할 수도 있습니다. 다음 값을 입력 한 후 **선택**을 선택 합니다. 
        - **서버 이름**: 보조 서버에 대 한 고유한 이름 (예: `mysqlsecondary`)을 입력 합니다. 
        - **서버 관리자 로그인**: 입력할`azureuser`
        - **암호**: 암호 요구 사항을 충족하는 복잡한 암호를 입력합니다.
        - **위치**: 드롭다운 (예: 미국 동부 2)에서 위치를 선택 합니다. 이 위치는 주 서버와 동일한 위치가 될 수 없습니다.

    > [!NOTE]
    > 서버 로그인 및 방화벽 설정은 주 서버의 이름과 일치 해야 합니다. 
    
      ![장애 조치 (failover) 그룹에 대 한 보조 서버 만들기](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **그룹 내의 데이터베이스**: 보조 서버를 선택 하면이 옵션의 잠금이 해제 됩니다. 이를 선택 하 여 **추가할 데이터베이스를 선택한** 다음 섹션 1에서 만든 데이터베이스를 선택 합니다. 장애 조치 (failover) 그룹에 데이터베이스를 추가 하면 지역에서 복제 프로세스가 자동으로 시작 됩니다. 
        
    ![장애 조치 (failover) 그룹에 SQL DB 추가](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 장애 조치 그룹을 만들고 단일 데이터베이스를 추가 합니다. 

   > [!NOTE]
   > 서버 로그인 및 방화벽 설정은 주 서버의 이름과 일치 해야 합니다. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLI를 사용 하 여 장애 조치 그룹을 만들고 단일 데이터베이스를 추가 합니다. 

   > [!NOTE]
   > 서버 로그인 및 방화벽 설정은 주 서버의 이름과 일치 해야 합니다. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-장애 조치 (failover) 테스트 
이 단계에서는 장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 Azure Portal를 사용 하 여 장애 복구 (failback) 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
Azure Portal를 사용 하 여 테스트 장애 조치 (failover) 

1. [Azure Portal](https://portal.azure.com)내의 **SQL** server 서버로 이동 합니다. 
1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 섹션 2에서 만든 장애 조치 (failover) 그룹을 선택 합니다. 
  
   ![포털에서 장애 조치 (failover) 그룹을 선택 합니다.](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. 주 서버 및 보조 서버를 검토 합니다. 
1. 작업 창에서 **장애 조치** (failover)를 선택 하 여 샘플 단일 데이터베이스가 포함 된 장애 조치 그룹을 장애 조치 합니다. 
1. TDS 세션의 연결을 끊을 것을 알리는 경고에서 **예** 를 선택 합니다. 

   ![SQL database를 포함 하는 장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. 현재 주 서버와 보조 서버가 있는 서버를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 서버에 교환 된 역할이 있어야 합니다. 
1. **장애 조치 (Failover)** 를 다시 선택 하 여 서버가 원래 역할로 다시 장애 조치 (Failover) 합니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 테스트 장애 조치 (failover) 


보조 복제본의 역할을 확인 합니다. 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```


보조 서버로 장애 조치 (failover): 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to sucessfully to" $drServerName 
   ```

장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLI를 사용 하 여 테스트 장애 조치 (failover) 


보조 서버를 확인 합니다.

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

보조 서버로 장애 조치 (failover): 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>리소스 정리 
리소스 그룹을 삭제 하 여 리소스를 정리 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
Azure Portal를 사용 하 여 리소스 그룹을 삭제 합니다. 


1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 합니다.
1. 리소스 그룹 자체 뿐만 아니라 그룹의 모든 리소스를 삭제 하려면 **리소스 그룹 삭제** 를 선택 합니다. 
1. 텍스트 상자에 리소스 그룹 `myResourceGroup`의 이름을 입력 하 고 **삭제** 를 선택 하 여 리소스 그룹을 삭제 합니다.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 리소스 그룹을 삭제 합니다. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLI를 사용 하 여 리소스 그룹을 삭제 합니다. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>전체 스크립트

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
Azure Portal 사용할 수 있는 스크립트가 없습니다.
 
---

다른 Azure SQL Database 스크립트는 다음 위치에서 찾을 수 있습니다. [Azure PowerShell](sql-database-powershell-samples.md) 및 [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 장애 조치 (failover) 그룹에 Azure SQL Database 단일 데이터베이스를 추가 하 고 장애 조치 (failover)를 테스트 했습니다. 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다. 
> - 두 논리 SQL server 간에 단일 데이터베이스에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

탄력적 풀을 장애 조치 (failover) 그룹에 추가 하는 방법에 대 한 다음 자습서로 이동 합니다. 

> [!div class="nextstepaction"]
> [자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀 추가](sql-database-elastic-pool-failover-group-tutorial.md)
