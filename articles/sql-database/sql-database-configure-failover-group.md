---
title: 장애 조치(failover) 그룹 구성
description: Azure 포털, Az CLI 및 PowerShell을 사용하여 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리형 인스턴스에 대해 자동 장애 조치 그룹을 구성하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 3b423a25b6b13ad543ef4a74bc0335ce19f5766d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461812"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 장애 조치 그룹 구성

이 항목에서는 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 Azure 포털 또는 PowerShell을 사용하여 관리되는 인스턴스에 대해 [자동 장애 조치 그룹을](sql-database-auto-failover-group.md) 구성하는 방법을 설명합니다. 

## <a name="single-database"></a>단일 데이터베이스
장애 조치 그룹을 만들고 Azure 포털 또는 PowerShell을 사용하여 단일 데이터베이스를 추가합니다.

### <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 조건을 고려하십시오.

- 보조 서버의 서버 로그인 및 방화벽 설정은 주 서버의 서버 설정과 일치해야 합니다. 

### <a name="create-failover-group"></a>장애 조치 그룹 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)
장애 조치 그룹을 만들고 Azure 포털을 사용하여 단일 데이터베이스를 추가합니다.


1. [Azure 포털의](https://portal.azure.com)왼쪽 메뉴에서 **Azure SQL을** 선택합니다. **Azure SQL**이 목록에 없는 경우 **모든 서비스**를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 그룹에 추가할 단일 데이터베이스를 선택합니다. 
1. **서버 이름** 아래에 있는 서버 이름을 선택하여 서버의 설정을 엽니다.

   ![단일 db용 개방형 서버](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치 그룹을** 선택한 다음 그룹 **추가를** 선택하여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 그룹 추가](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 장애 **조치 그룹** 페이지에서 필요한 값을 입력하거나 선택한 다음 을 **선택합니다.**

   - **그룹 내의 데이터베이스**: 장애 조치 그룹에 추가할 데이터베이스를 선택합니다. 장애 조치 그룹에 데이터베이스를 추가하면 지역 복제 프로세스가 자동으로 시작됩니다. 
        
    ![장애 조치 그룹에 SQL DB 추가](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Failover 그룹을 만들고 PowerShell을 사용하여 단일 데이터베이스를 추가합니다. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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

---

### <a name="test-failover"></a>테스트 장애 조치 

Azure 포털 또는 PowerShell을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다. 

1. [Azure 포털의](https://portal.azure.com)왼쪽 메뉴에서 **Azure SQL을** 선택합니다. **Azure SQL**이 목록에 없는 경우 **모든 서비스**를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 그룹에 추가할 단일 데이터베이스를 선택합니다. 

   ![단일 db용 개방형 서버](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치 그룹을** 선택한 다음 방금 만든 장애 조치 그룹을 선택합니다. 
  
   ![포털에서 장애 조치 그룹 선택](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 기본 서버와 보조 서버를 검토합니다. 
1. 작업 창에서 **장애** 조치(Failover)를 선택하여 단일 데이터베이스를 포함하는 장애 조치 그룹에 대해 장애 조치(failover)를 선택합니다. 
1. TDS 세션의 연결이 끊어질 것임을 알려주는 경고에서 **예를** 선택합니다. 

   ![SQL 데이터베이스가 포함된 장애 조치 그룹에 장애 조치(Failover)가 장애 조치(failover)를 초과 장애 조치(failover)합니다.](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 이제 기본 서버와 보조 서버를 검토합니다. 장애 조치(failover)가 성공하면 두 서버가 역할을 교체해야 합니다. 
1. **장애 조치(Failover)를** 다시 선택하여 서버를 원래 역할로 다시 장애 조치(failover)로 다시 장애 조치(failover)로 다시 장애 조치(Failover)로 다시 장애 조치(Failover)를 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다.  

보조 복제본의 역할을 확인합니다. 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
보조 서버로 장애 조치: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제해야 하는 경우 장애 조치 그룹에서 제거한 후 삭제합니다. 장애 조치 그룹에서 제거되기 전에 보조 데이터베이스를 삭제하면 예기치 않은 동작이 발생할 수 있습니다. 

## <a name="elastic-pool"></a>탄력적 풀
장애 조치 그룹을 만들고 Azure 포털 또는 PowerShell을 사용하여 장애 조치 그룹을 만들고 탄력적 풀을 추가합니다.  

### <a name="prerequisites"></a>사전 요구 사항

다음 필수 구성 조건을 고려하십시오.

- 보조 서버의 서버 로그인 및 방화벽 설정은 주 서버의 서버 설정과 일치해야 합니다. 

### <a name="create-the-failover-group"></a>장애 조치 그룹 만들기 

Azure 포털 또는 PowerShell을 사용하여 탄력적 풀에 대한 장애 조치 그룹을 만듭니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
장애 조치 그룹을 만들고 Azure 포털을 사용하여 탄력적 풀을 추가합니다.

1. [Azure 포털의](https://portal.azure.com)왼쪽 메뉴에서 **Azure SQL을** 선택합니다. **Azure SQL**이 목록에 없는 경우 **모든 서비스**를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 그룹에 추가할 탄력적 풀을 선택합니다. 
1. **개요** 창에서 **서버 이름** 아래의 서버 이름을 선택하여 서버설정을 엽니다.
  
    ![탄력적 풀을 위한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 그룹을** 선택한 다음 그룹 **추가를** 선택하여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 그룹 추가](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 장애 **조치 그룹** 페이지에서 필요한 값을 입력하거나 선택한 다음 을 **선택합니다.** 새 보조 서버를 만들거나 기존 보조 서버를 선택합니다. 

1. **그룹 내의 데이터베이스를** 선택한 다음 장애 조치 그룹에 추가할 탄력적 풀을 선택합니다. 보조 서버에 탄력적 풀이 아직 없는 경우 보조 서버에 탄력적 풀을 만들라는 경고가 나타납니다. 경고를 선택한 다음 **확인을** 선택하여 보조 서버에서 탄력적 풀을 만듭니다. 
        
    ![장애 조치 그룹에 탄력적 풀 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 장애 조치 그룹에 탄력적 풀 설정을 적용하려면 **선택을** 선택한 다음 **만들기를** 선택하여 장애 조치 그룹을 만듭니다. 장애 조치 그룹에 탄력적 풀을 추가하면 지역 복제 프로세스가 자동으로 시작됩니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

장애 조치 그룹을 만들고 PowerShell을 사용하여 탄력적 풀을 추가합니다. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>테스트 장애 조치

Azure 포털 또는 PowerShell을 사용하여 탄력적 풀의 장애 조치(failover)를 테스트합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

장애 조치 그룹을 보조 서버로 장애 조치(failover)로 장애 조치(failover)한 다음 Azure 포털을 사용하여 다시 장애 조치(failover)합니다. 

1. [Azure 포털의](https://portal.azure.com)왼쪽 메뉴에서 **Azure SQL을** 선택합니다. **Azure SQL**이 목록에 없는 경우 **모든 서비스**를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 그룹에 추가할 탄력적 풀을 선택합니다. 
1. **개요** 창에서 **서버 이름** 아래의 서버 이름을 선택하여 서버설정을 엽니다.
  
    ![탄력적 풀을 위한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. **설정** 창에서 **장애 조치 그룹을** 선택한 다음 섹션 2에서 만든 장애 조치 그룹을 선택합니다. 
  
   ![포털에서 장애 조치 그룹 선택](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 기본 서버와 보조 서버를 검토합니다. 
1. 작업 창에서 **장애 조치(Failover)를** 선택하여 탄력적 풀을 포함하는 장애 조치 그룹을 장애 조치 그룹에 장애 조치(failover)로 이동합니다. 
1. TDS 세션의 연결이 끊어질 것임을 알려주는 경고에서 **예를** 선택합니다. 

   ![SQL 데이터베이스가 포함된 장애 조치 그룹에 장애 조치(Failover)가 장애 조치(failover)를 초과 장애 조치(failover)합니다.](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 어떤 서버가 기본 서버인지, 어떤 서버가 보조 서버인지 검토합니다. 장애 조치(failover)가 성공하면 두 서버가 역할을 교체해야 합니다. 
1. 장애 조치 그룹을 다시 원래 설정으로 되돌리려면 **장애 조치(Failover)를** 다시 선택합니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다.

보조 복제본의 역할을 확인합니다. 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

보조 서버로 장애 조치: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제해야 하는 경우 장애 조치 그룹에서 제거한 후 삭제합니다. 장애 조치 그룹에서 제거되기 전에 보조 데이터베이스를 삭제하면 예기치 않은 동작이 발생할 수 있습니다. 

## <a name="managed-instance"></a>관리되는 인스턴스

Azure 포털 또는 PowerShell을 사용하여 관리되는 두 인스턴스 간에 장애 조치 그룹을 만듭니다. 

[ExpressRoute를](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 구성하거나 각 관리되는 인스턴스의 가상 네트워크에 대한 게이트웨이를 만들고 두 게이트웨이를 연결한 다음 장애 조치 그룹을 만들어야 합니다. 

### <a name="prerequisites"></a>사전 요구 사항
다음 필수 구성 조건을 고려하십시오.

- 보조 관리 인스턴스는 비어 있어야 합니다.
- 보조 가상 네트워크의 서브넷 범위는 기본 가상 네트워크의 서브넷 범위를 겹쳐서는 안 됩니다. 
- 보조 인스턴스의 데이터 정렬 및 표준 시간대는 기본 인스턴스의 데이터 정렬 및 표준 시간대와 일치해야 합니다. 
- 두 게이트웨이를 연결할 때 **공유 키는** 두 연결모두에 대해 동일해야 합니다. 

### <a name="create-primary-virtual-network-gateway"></a>기본 가상 네트워크 게이트웨이 만들기 

[ExpressRoute를](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)구성하지 않은 경우 Azure 포털 또는 PowerShell을 사용하여 기본 가상 네트워크 게이트웨이를 만들 수 있습니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 기본 가상 네트워크 게이트웨이를 만듭니다. 

1. Azure [포털에서](https://portal.azure.com)리소스 그룹으로 이동하여 기본 관리 인스턴스에 대한 **가상 네트워크** 리소스를 선택합니다. 
1. **설정에서** **서브넷을** 선택한 다음 새 **게이트웨이 서브넷을**추가하려면 선택합니다. 기본값을 그대로 둡니다. 

   ![기본 관리형 인스턴스에 대한 게이트웨이 추가](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 서브넷 게이트웨이가 만들어지면 왼쪽 탐색 창에서 **리소스 만들기를** `Virtual network gateway` 선택한 다음 검색 상자에 입력합니다. **Microsoft에서**게시한 **가상 네트워크 게이트웨이** 리소스를 선택합니다. 

   ![새 가상 네트워크 게이트웨이 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 기본 관리 인스턴스를 구성하는 데 필요한 필드를 채웁니다. 

   다음 표에서는 기본 관리 인스턴스의 게이트웨이에 필요한 값을 보여 주며,
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리 인스턴스가 있는 구독입니다. |
    | **이름** | 가상 네트워크 게이트웨이의 이름입니다. | 
    | **지역** | 보조 관리 인스턴스가 있는 지역입니다. |
    | **게이트웨이 유형** | **VPN을**선택합니다. |
    | **VPN 유형** | **경로 기반** 선택 |
    | **Sku**| 기본값을 `VpnGw1`그대로 둡니다. |
    | **위치**| 보조 관리 인스턴스 및 보조 가상 네트워크가 있는 위치입니다.   |
    | **가상 네트워크**| 보조 관리 인스턴스의 가상 네트워크를 선택합니다. |
    | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
    | **공용 IP 주소 이름**| IP 주소의 이름을 입력합니다. |
    | &nbsp; | &nbsp; |

1. 다른 값을 기본값으로 그대로 두은 다음 **검토 + 만들기를** 선택하여 가상 네트워크 게이트웨이의 설정을 검토합니다.

   ![기본 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. 새 가상 네트워크 게이트웨이를 만들려면 **만들기를** 선택합니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 기본 가상 네트워크 게이트웨이를 만듭니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>보조 가상 네트워크 게이트웨이 생성

Azure 포털 또는 PowerShell을 사용하여 보조 가상 네트워크 게이트웨이를 만듭니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
이전 섹션의 단계를 반복하여 보조 관리 인스턴스에 대한 가상 네트워크 서브넷 및 게이트웨이를 만듭니다. 보조 관리 인스턴스에 대한 게이트웨이를 구성하는 데 필요한 필드를 채웁니다. 

   다음 표에서는 보조 관리 인스턴스의 게이트웨이에 필요한 값을 보여 주며 있습니다.

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  보조 관리 인스턴스가 있는 구독입니다. |
   | **이름** | 와 같은 `secondary-mi-gateway`가상 네트워크 게이트웨이의 이름입니다. | 
   | **지역** | 보조 관리 인스턴스가 있는 지역입니다. |
   | **게이트웨이 유형** | **VPN을**선택합니다. |
   | **VPN 유형** | **경로 기반** 선택 |
   | **Sku**| 기본값을 `VpnGw1`그대로 둡니다. |
   | **위치**| 보조 관리 인스턴스 및 보조 가상 네트워크가 있는 위치입니다.   |
   | **가상 네트워크**| `vnet-sql-mi-secondary`과 같은 섹션 2에서 만든 가상 네트워크를 선택합니다. |
   | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
   | **공용 IP 주소 이름**| 와 같은 `secondary-gateway-IP`IP 주소의 이름을 입력합니다. |
   | &nbsp; | &nbsp; |

   ![보조 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 보조 가상 네트워크 게이트웨이를 만듭니다. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>게이트웨이 연결 
Azure 포털 또는 PowerShell을 사용하여 두 게이트웨이 간에 연결을 만듭니다. 

기본 게이트웨이에서 보조 게이트웨이로의 연결, 보조 게이트웨이에서 기본 게이트웨이로의 연결 등 두 개의 연결을 만들어야 합니다. 

두 연결에 사용되는 공유 키는 각 연결에 대해 동일해야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure 포털을 사용하여 두 게이트웨이 간에 연결을 만듭니다. 

1. [Azure 포털에서](https://portal.azure.com) **리소스 만들기를** 선택합니다.
1. 검색 `connection` 상자를 입력한 다음 Enter를 눌러 검색하여 Microsoft에서 게시한 **연결** 리소스로 이동합니다.
1. 연결을 만들려면 **만들기를** 선택합니다. 
1. 기본 탭에서 다음 값을 선택한 다음 **확인을** **선택합니다.** 
    1. 연결 `VNet-to-VNet` **유형에**대해 선택합니다. 
    1. 드롭다운에서 구독을 선택합니다. 
    1. 드롭다운에서 관리되는 인스턴스에 대한 리소스 그룹을 선택합니다. 
    1. 드롭다운에서 기본 관리 인스턴스의 위치를 선택합니다. 
1. **설정** 탭에서 다음 값을 선택하거나 입력한 다음 **확인을**선택합니다.
    1. 첫 번째 가상 네트워크 게이트웨이에 대한 기본 `Primary-Gateway` **네트워크 게이트웨이를**선택합니다.  
    1. 두 번째 가상 네트워크 게이트웨이에 대한 보조 `Secondary-Gateway` **네트워크 게이트웨이를**선택합니다. 
    1. **양방향 연결 설정**옆의 확인란을 선택합니다. 
    1. 기본 기본 연결 이름을 그대로 두거나 이름을 선택한 값으로 바꿉니다. 
    1. 연결에 대한 **공유 키(PSK)를** `mi1m2psk`제공합니다. 

   ![게이트웨이 연결 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. **요약** 탭에서 양방향 연결의 설정을 검토한 다음 **확인을** 선택하여 연결을 만듭니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 두 게이트웨이 간의 연결을 만듭니다. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>장애 조치 그룹 만들기 
Azure 포털 또는 PowerShell을 사용하여 관리되는 인스턴스에 대한 장애 조치 그룹을 만듭니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 관리되는 인스턴스에 대한 장애 조치 그룹을 만듭니다. 

1. [Azure 포털의](https://portal.azure.com)왼쪽 메뉴에서 **Azure SQL을** 선택합니다. **Azure SQL**이 목록에 없는 경우 **모든 서비스**를 선택한 다음, 검색 상자에 Azure SQL을 입력합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 그룹에 추가할 기본 관리 인스턴스를 선택합니다.  
1. **설정에서**장애 **조치 인스턴스 그룹으로** 이동한 다음 **그룹 추가를** 선택하여 **장애 조치(Failover) 인스턴스 그룹** 페이지를 엽니다. 

   ![장애 조치 그룹 추가](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **Failover 그룹 인스턴스** 페이지에서 장애 조치 그룹의 이름을 입력한 다음 드롭다운에서 보조 관리 인스턴스를 선택합니다. 장애 조치 그룹을 만들려면 **만들기를** 선택합니다. 

   ![장애 조치 그룹 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 장애 조치 그룹 배포가 완료되면 장애 조치 **그룹** 페이지로 돌아갑니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 관리되는 인스턴스에 대한 장애 조치 그룹을 만듭니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>테스트 장애 조치

Azure 포털 또는 PowerShell을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다. 

1. [Azure 포털](https://portal.azure.com) 내에서 _보조_ 관리 되는 인스턴스로 이동 하 고 설정에서 인스턴스 장애 **조치 그룹을** 선택 합니다. 
1. 관리되는 인스턴스가 기본이고 관리되는 인스턴스가 보조 인스턴스인지 검토합니다. 
1. **장애 조치(Failover)를** 선택한 다음 연결이 끊어지는 TDS 세션에 대한 경고에서 **예를** 선택합니다. 

   ![장애 조치 그룹 장애 조치(failover) 그룹 장애 조치](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 어떤 망달 인스턴스가 기본 인스턴스이고 어떤 인스턴스가 보조 인스턴스인지 검토합니다. 장애 조치(failover)가 성공하면 두 인스턴스가 역할을 전환해야 합니다. 

   ![관리되는 인스턴스가 장애 조치 후 역할을 전환했습니다.](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. 새 _보조_ 관리형 인스턴스로 이동하여 **장애 조치(Failover)를** 다시 선택하여 기본 인스턴스를 기본 역할로 다시 실패시합니다. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell을 사용하여 장애 조치 그룹의 장애 조치(failover)를 테스트합니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>리스너 끝점 찾기

장애 조치 그룹이 구성되면 응용 프로그램의 연결 문자열을 수신기 끝점으로 업데이트합니다. 이렇게 하면 응용 프로그램이 기본 데이터베이스, 탄력적 풀 또는 관리되는 인스턴스가 아닌 장애 조치 그룹 리스너에 연결됩니다. 이렇게 하면 Azure SQL 데이터베이스 엔터티가 장애 조치될 때마다 연결 문자열을 수동으로 업데이트할 필요가 없으며 트래픽이 현재 기본 엔터티인 엔터티로 라우팅됩니다. 

수신기 끝점은 `fog-name.database.windows.net`의 형태로 이며 장애 조치 그룹을 볼 때 Azure 포털에 표시 됩니다.

![장애 조치 그룹 연결 문자열](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>설명

- 단일 또는 풀풀된 데이터베이스에 대한 장애 조치 그룹을 제거해도 복제가 중지되지 않으며 복제된 데이터베이스는 삭제되지 않습니다. 단일 또는 풀레이션된 데이터베이스를 제거한 후 장애 조치 그룹에 다시 추가하려면 지역 복제를 수동으로 중지하고 보조 서버에서 데이터베이스를 삭제해야 합니다. 두 작업을 수행하지 않으면 장애 조치 `The operation cannot be performed due to multiple errors` 그룹에 데이터베이스를 추가하려고 할 때와 유사한 오류가 발생할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

장애 조치 그룹을 구성하는 자세한 단계는 다음 자습서를 참조하십시오.
- [장애 조치 그룹에 단일 데이터베이스 추가](sql-database-single-database-failover-group-tutorial.md)
- [장애 조치(failover) 그룹에 탄력적 풀 추가](sql-database-elastic-pool-failover-group-tutorial.md)
- [장애 조치 그룹에 관리되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md)
 
Azure SQL Database 고가용성 옵션에 대한 개요는 [지역 복제](sql-database-active-geo-replication.md) 및 자동 장애 [조치 그룹을](sql-database-auto-failover-group.md)참조하십시오. 
