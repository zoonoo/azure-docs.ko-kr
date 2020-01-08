---
title: '자습서: 장애 조치 (failover) 그룹에 탄력적 풀 추가'
description: Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀을 추가 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552562"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>자습서: 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀 추가

Azure Portal를 사용 하 여 Azure SQL Database 탄력적 풀 및 테스트 장애 조치 (failover) 그룹을 구성 합니다.  이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다.
> - 단일 데이터베이스를 탄력적 풀에 추가 합니다. 
> - 두 개의 논리 SQL server 간에 두 개의 탄력적 풀에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다. 

- Azure 구독 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1-단일 데이터베이스 만들기 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-탄력적 풀에 단일 데이터베이스 추가
이 단계에서는 탄력적 풀을 만들고 단일 데이터베이스를 추가 합니다. 


# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 탄력적 풀을 만듭니다. 


1. Azure Portal의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. **+ 추가**를 선택하여 **SQL 배포 옵션 선택** 페이지를 엽니다. 데이터베이스 타일에 대 한 자세한 정보 표시를 선택 하 여 다른 데이터베이스에 대 한 추가 정보를 볼 수 있습니다.
1. **SQL 데이터베이스** 타일의 **리소스 종류** 드롭다운에서 **탄력적 풀** 을 선택 합니다. **만들기** 를 선택 하 여 탄력적 풀을 만듭니다. 

    ![탄력적 풀 선택](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. 다음 값을 사용 하 여 탄력적 풀을 구성 합니다.
   - **이름**: 탄력적 풀에 대 한 고유 이름 (예: `myElasticPool`)을 제공 합니다. 
   - **구독**: 드롭다운에서 구독을 선택 합니다.
   - **ResourceGroup**: 드롭다운에서 섹션 1에서 만든 리소스 그룹의 `myResourceGroup`을 선택 합니다. 
   - **서버**: 드롭다운에서 섹션 1에서 만든 서버를 선택 합니다.  

       ![탄력적 풀에 대 한 새 서버 만들기](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + storage**: **탄력적 풀 구성** 을 선택 하 여 계산 및 저장소를 구성 하 고 단일 데이터베이스를 탄력적 풀에 추가 합니다. **풀 설정** 탭에서 기본 Gen5을 2 개 vcores 및 32gb로 그대로 둡니다. 

1. **구성** 페이지에서 **데이터베이스** 탭을 선택한 다음 **데이터베이스 추가**를 선택 합니다. 섹션 1에서 만든 데이터베이스를 선택한 다음 **적용** 을 선택 하 여 탄력적 풀에 추가 합니다. 다시 **적용** 을 선택 하 여 탄력적 풀 설정을 적용 하 고 **구성** 페이지를 닫습니다. 

    ![탄력적 풀에 SQL DB 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. **검토 + 만들기** 를 선택 하 여 탄력적 풀 설정을 검토 한 후 **만들기** 를 선택 하 여 탄력적 풀을 만듭니다. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 탄력적 풀 및 보조 서버를 만듭니다. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

자습서의이 부분에서는 다음 PowerShell cmdlet을 사용 합니다.

| 명령 | 메모 |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL Database에 대 한 탄력적 데이터베이스 풀을 만듭니다.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동. | 

---

## <a name="3---create-the-failover-group"></a>3-장애 조치 (failover) 그룹 만들기 
이 단계에서는 기존 Azure SQL server와 다른 지역의 새 Azure SQL server 간에 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다. 그런 다음 장애 조치 (failover) 그룹에 탄력적 풀을 추가 합니다. 


# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 장애 조치 (failover) 그룹을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 이전 섹션에서 만든 탄력적 풀 (예: `myElasticPool`)을 선택 합니다. 
1. **개요** 창에서 서버 **이름** 아래의 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.
  
    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 **그룹 추가** 를 선택 하 여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 (failover) 그룹 추가](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. **장애 조치 (Failover) 그룹** 페이지에서 다음 값을 입력 하거나 선택한 다음 **만들기**를 선택 합니다.
    - **장애 조치 (Failover) 그룹 이름**: `failovergrouptutorial`와 같은 고유한 장애 조치 (failover) 그룹 이름을 입력 합니다. 
    - **보조 서버**: *필수 설정을 구성* 하는 옵션을 선택한 다음 **새 서버를 만들도록**선택 합니다. 또는 기존 서버를 보조 서버로 선택할 수도 있습니다. 새 보조 서버에 대해 다음 값을 입력 한 후 **선택**을 선택 합니다. 
        - **서버 이름**: `mysqlsecondary`와 같은 보조 서버에 대 한 고유한 이름을 입력 합니다. 
        - **서버 관리자 로그인**: 유형 `azureuser`
        - **암호**: 암호 요구 사항을 충족 하는 복잡 한 암호를 입력 합니다.
        - **위치**: 드롭다운에서 `East US`와 같은 위치를 선택 합니다. 이 위치는 주 서버와 동일한 위치가 될 수 없습니다.

       > [!NOTE]
       > 서버 로그인 및 방화벽 설정은 주 서버의 이름과 일치 해야 합니다. 
    
       ![장애 조치 (failover) 그룹에 대 한 보조 서버 만들기](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. **그룹 내의 데이터베이스** 를 선택한 다음 섹션 2에서 만든 탄력적 풀을 선택 합니다. 보조 서버에서 탄력적 풀을 만들지 묻는 경고가 표시 됩니다. 경고를 선택한 다음 **확인** 을 선택 하 여 보조 서버에서 탄력적 풀을 만듭니다. 
        
    ![장애 조치 (failover) 그룹에 탄력적 풀 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. **선택** 을 선택 하 여 탄력적 풀 설정을 장애 조치 (failover) 그룹에 적용 한 다음 **만들기** 를 선택 하 여 장애 조치 그룹을 만듭니다. 장애 조치 (failover) 그룹에 탄력적 풀을 추가 하면 지역에서 복제 프로세스가 자동으로 시작 됩니다.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 장애 조치 (failover) 그룹을 만듭니다. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

자습서의이 부분에서는 다음 PowerShell cmdlet을 사용 합니다.

| 명령 | 메모 |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 단일 데이터베이스 및 탄력적 풀을 호스트하는 SQL Database 서버를 만듭니다. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 논리 서버에 대한 방화벽 규칙을 만듭니다. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL Database에 대 한 탄력적 데이터베이스 풀을 만듭니다.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 새 장애 조치(failover) 그룹을 만듭니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 장애 조치(failover) 그룹에 하나 이상의 Azure SQL Database 데이터베이스를 추가합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database 장애 조치(failover) 그룹을 가져오거나 나열합니다. |

---


## <a name="4---test-failover"></a>4-테스트 장애 조치 
이 단계에서는 장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 Azure Portal를 사용 하 여 장애 복구 (failback) 합니다. 


# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 장애 조치 (failover) 그룹의 장애 조치를 테스트 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 이전 섹션에서 만든 탄력적 풀 (예: `myElasticPool`)을 선택 합니다. 
1. 서버 **이름** 아래에서 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.

    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 섹션 2에서 만든 장애 조치 (failover) 그룹을 선택 합니다. 
  
   ![포털에서 장애 조치 (failover) 그룹을 선택 합니다.](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. 주 서버와 보조 서버를 검토 합니다. 
1. 작업 창에서 **장애 조치 (Failover)** 를 선택 하 여 탄력적 풀이 포함 된 장애 조치 그룹을 장애 조치 (failover) 합니다. 
1. TDS 세션의 연결을 끊을 것을 알리는 경고에서 **예** 를 선택 합니다. 

   ![SQL database를 포함 하는 장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. 주 서버가 보조 서버인 서버를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 서버에 교환 된 역할이 있어야 합니다. 
1. 장애 **조치 (** failover)를 다시 선택 하 여 장애 조치 그룹을 다시 원래 설정으로 장애 조치 합니다. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 장애 조치 그룹의 장애 조치를 테스트 합니다. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 PowerShell을 사용 하 여 장애 복구 (failback) 합니다. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

자습서의이 부분에서는 다음 PowerShell cmdlet을 사용 합니다.

| 명령 | 메모 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database 장애 조치(failover) 그룹을 가져오거나 나열합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. |


---

## <a name="clean-up-resources"></a>리소스 정리 

리소스 그룹을 삭제 하 여 리소스를 정리 합니다. 


# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)


1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 합니다.
1. 리소스 그룹 자체 뿐만 아니라 그룹의 모든 리소스를 삭제 하려면 **리소스 그룹 삭제** 를 선택 합니다. 
1. 리소스 그룹의 이름을 입력 하 고, `myResourceGroup`텍스트 상자에을 입력 한 다음 **삭제** 를 선택 하 여 리소스 그룹을 삭제 합니다. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 리소스를 정리 합니다. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

자습서의이 부분에서는 다음 PowerShell cmdlet을 사용 합니다.

| 명령 | 메모 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. | 

---

> [!IMPORTANT]
> 리소스 그룹을 유지 하지만 보조 데이터베이스를 삭제 하려는 경우에는 장애 조치 (failover) 그룹을 삭제 하기 전에 해당 그룹에서 제거 합니다. 장애 조치 (failover) 그룹에서 보조 데이터베이스를 제거 하기 전에 삭제 하면 예기치 않은 동작이 발생할 수 있습니다. 

## <a name="full-script"></a>전체 스크립트

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 단일 데이터베이스 및 탄력적 풀을 호스트하는 SQL Database 서버를 만듭니다. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 논리 서버에 대한 방화벽 규칙을 만듭니다. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 새 Azure SQL Database 단일 데이터베이스를 만듭니다. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL Database에 대 한 탄력적 데이터베이스 풀을 만듭니다.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 새 장애 조치(failover) 그룹을 만듭니다. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 하나 이상의 SQL 데이터베이스를 가져옵니다. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 장애 조치(failover) 그룹에 하나 이상의 Azure SQL Database 데이터베이스를 추가합니다. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL Database 장애 조치(failover) 그룹을 가져오거나 나열합니다. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Azure SQL Database 장애 조치(failover) 그룹의 장애 조치(failover)를 실행합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹을 제거합니다. | 


# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
Azure Portal 사용할 수 있는 스크립트가 없습니다.

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 장애 조치 (failover) 그룹에 Azure SQL Database 탄력적 풀을 추가 하 고 장애 조치 (failover)를 테스트 했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> - Azure SQL Database 단일 데이터베이스를 만듭니다.
> - 단일 데이터베이스를 탄력적 풀에 추가 합니다. 
> - 두 개의 논리 SQL server 간에 두 개의 탄력적 풀에 대 한 [장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 만듭니다.
> - 테스트 장애 조치 (failover)

DMS를 사용한 마이그레이션 방법을 설명하는 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [자습서: DMS를 사용 하 여 풀링된 데이터베이스로 SQL Server 마이그레이션](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
