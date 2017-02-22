---
title: "PowerShell을 사용하여 Azure SQL Database 관리 | Microsoft Docs"
description: "PowerShell을 사용한 Azure SQL 데이터베이스 관리."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 32d8c5f13d95c3de8b06782f4f6541866389be5b


---
# <a name="managing-azure-sql-database-using-powershell"></a>PowerShell을 사용하여 Azure SQL Database 관리

이 항목에서는 많은 Azure SQL 데이터베이스 작업을 수행하는 데 사용되는 PowerShell cmdlet을 보여줍니다. 전체 목록은 [Azure SQL Database cmdlet](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)을 참조하세요.

> [!TIP]
> 서버 만들기, 서버 기반 방화벽 만들기, 서버 속성 보기, master 데이터베이스 연결 및 쿼리, 샘플 데이터베이스 및 빈 데이터베이스 만들기, 데이터베이스 속성 쿼리, 샘플 데이터베이스 연결 및 쿼리를 수행하는 방법을 보여 주는 자습서에 대해서는 [시작 자습서](sql-database-get-started-powershell.md)를 참조하세요.
>

## <a name="how-do-i-create-a-resource-group"></a>리소스 그룹을 어떻게 만드나요?
SQL 데이터베이스 및 관련 Azure 리소스에 대한 리소스 그룹을 만들려면 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) cmdlet을 사용합니다.

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
전체 자습서에 대해서는 [Azure PowerShell를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started-powershell.md)을 참조하세요.

## <a name="how-do-i-create-a-sql-database-server"></a>SQL 데이터베이스 서버를 어떻게 만드나요?
SQL 데이터베이스 서버를 만들려면 [New-AzureRmSqlServer](/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) cmdlet을 사용합니다. *server1* 을 서버의 이름으로 바꿉니다. 서버 이름은 모든 Azure SQL 데이터베이스 서버 간에 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 이 명령을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 리소스 그룹이 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

서버에 대한 자세한 내용은 [SQL Database 기능](sql-database-features.md)을 참조하세요. 전체 자습서에 대해서는 [Azure PowerShell를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started-powershell.md)을 참조하세요.

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>SQL 데이터베이스 서버 방화벽 규칙을 어떻게 만드나요?
서버에 액세스하기 위한 방화벽 규칙을 만들려면 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) cmdlet을 사용합니다. 다음 명령을 실행하여 시작 및 끝 IP 주소를 사용자 컴퓨터에 유효한 값으로 바꿉니다. 리소스 그룹 및 서버가 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 만들고 `-StartIpAddress` 및 `-EndIpAddress`를 모두 **0.0.0.0**으로 설정합니다. 이렇게 하면 특별한 방화벽 규칙을 통해 서버에 대한 모든 Azure 트래픽 액세스가 허용됩니다.

자세한 내용은 [Azure SQL 데이터베이스 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 참조하세요. 전체 자습서에 대해서는 [Azure PowerShell를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started-powershell.md)을 참조하세요.

## <a name="how-do-i-create-a-sql-database"></a>SQL 데이터베이스를 어떻게 만드나요?
SQL 데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) cmdlet을 사용합니다. 리소스 그룹 및 서버가 구독에 이미 있어야 합니다. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

자세한 내용은 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 전체 자습서에 대해서는 [Azure PowerShell를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started-powershell.md)을 참조하세요.

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>SQL 데이터베이스의 성능 수준을 어떻게 변경하나요?
성능 수준을 변경하려면 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet을 사용하여 데이터베이스 규모를 확장하거나 축소합니다. 리소스 그룹, 서버 및 데이터베이스가 구독에 이미 있어야 합니다. 기본 계층의 경우 `-RequestedServiceObjectiveName` 을 단일 공백(예: 다음 코드 조각)으로 설정합니다. 이전의 다른 계층에 대한 예처럼 *S0*, *S1*, *P1*, *P6* 등으로 설정합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

자세한 내용은 [SQL Database 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해](sql-database-service-tiers.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 샘플 PowerShell 스크립트](sql-database-manage-single-databases-powershell.md#change-the-service-tier-and-performance-level-of-a-single-database)를 참조하세요.

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>동일한 서버에 SQL 데이터베이스를 어떻게 복사하나요?
SQL 데이터베이스를 동일한 서버에 복사하려면 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) cmdlet을 사용합니다. `-CopyServerName` 및 `-CopyResourceGroupName`을 원본 데이터베이스 서버 및 리소스 그룹과 동일한 값으로 설정합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

자세한 내용은 [Azure SQL 데이터베이스 복사](sql-database-copy.md)를 참조하세요. 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 복사](sql-database-copy-powershell.md#example-powershell-script)를 참조하세요.

## <a name="how-do-i-delete-a-sql-database"></a>SQL Database 삭제 방법
SQL 데이터베이스를 삭제하려면 [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx) cmdlet을 사용합니다. 리소스 그룹, 서버 및 데이터베이스가 구독에 이미 있어야 합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>SQL 데이터베이스 서버를 어떻게 삭제하나요?
SQL 데이터베이스 서버를 삭제하려면 [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) cmdlet을 사용합니다.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>PowerShell을 사용하여 탄력적 풀을 어떻게 만들고 관리하나요?
PowerShell을 사용하여 탄력적 풀 만들기에 대한 자세한 내용은 [PowerShell을 사용하여 새 탄력적 풀 만들기](sql-database-elastic-pool-manage-powershell.md)를 참조하세요.

PowerShell을 사용하여 탄력적 풀 관리에 대한 자세한 내용은 [PowerShell을 사용하여 탄력적 풀 모니터링 및 관리](sql-database-elastic-pool-manage-powershell.md)를 참조하세요.

## <a name="related-information"></a>관련 정보
* [Azure SQL 데이터베이스 cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure Cmdlet 참조](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO3-->


