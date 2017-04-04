---
title: "Azure PowerShell: SQL Database 만들기 | Microsoft Docs"
description: "Azure Portal에서 SQL Database 논리 서버, 서버 수준 방화벽 규칙 및 데이터베이스를 만드는 방법을 알아봅니다."
keywords: "SQL 데이터베이스 자습서, SQL 데이터베이스 만들기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 70cf89a5832aee2d0c303e0d40e104d84837b50c
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>PowerShell을 사용하여 단일 Azure SQL Database 만들기

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 PowerShell이 사용됩니다. 이 가이드에서는 PowerShell을 사용하여 [Azure SQL Database 논리 서버](sql-database-features.md)의 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)에서 Azure SQL Database를 배포하는 방법에 대해 자세히 설명합니다.

시작하기 전에, 최신 버전의 PowerShell을 설치했는지 확인합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azureps-cmdlets-docs)을 참조하세요. 

## <a name="log-in-to-azure"></a>Azure에 로그인

[Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) 명령을 사용하여 [Azure 리소스 그룹](../azure-resource-manager/resource-group-overview.md)을 만듭니다. 리소스 그룹은 Azure 리소스가 그룹으로 배포되고 관리되는 논리 컨테이너입니다. 다음 예제는 `westeurope` 위치에 `myResourceGroup`이라는 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>논리 서버 만들기

[New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) 명령을 사용하여 [Azure SQL Database 논리 서버](sql-database-features.md)를 만듭니다. 논리 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함합니다. 다음 예제에서는 관리자 로그인 이름이 `ServerAdmin`이고 암호가 `ChangeYourAdminPassword1`인 리소스 그룹에 임의로 이름이 지정된 서버를 생성합니다. 이러한 미리 정의된 값은 필요에 따라 바꿉니다.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) 명령을 사용하여 [Azure SQL Database 서버 수준 방화벽 규칙](sql-database-firewall-configure.md)을 만듭니다. 서버 수준 방화벽 규칙을 통해 외부 응용 프로그램(예: SQL Server Management Studio 또는 SQLCMD 유틸리티)이 SQL Database 서비스 방화벽을 통해 SQL Database에 연결되도록 할 수 있습니다. 다음 예제에서는 미리 정의된 주소 범위(이 예제에서는 전체 가능한 IP 주소 범위)에 대해 방화벽 규칙을 만듭니다. 이러한 미리 정의된 값은 외부 IP 주소 또는 IP 주소 범위 값으로 바꿉니다. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>빈 데이터베이스 만들기

[New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) 명령으로 서버에 [S0 성능 수준](sql-database-service-tiers.md)인 비어 있는 SQL Database를 만듭니다. 다음 예제에서는 `mySampleDatabase`라는 데이터베이스를 만듭니다. 이 미리 정의된 값은 필요에 따라 바꿉니다.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>리소스 정리

이 컬렉션의 다른 빠른 시작은 이 빠른 시작을 기반으로 합니다. 다음 빠른 시작 또는 자습서를 사용하여 계속하려는 경우 이 빠른 시작에서 만든 리소스를 정리하지 않습니다. 계속하지 않으려는 경우 다음 명령을 사용하여 이 빠른 시작에서 만든 모든 리소스를 삭제합니다.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>다음 단계

- SQL Server Management Studio를 사용하여 연결 및 쿼리하려면 [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요.
- Visual Studio를 사용하여 연결하려면 [Visual Studio를 사용하여 연결 및 쿼리](sql-database-connect-query.md)를 참조하세요.
* SQL Database에 대한 기술적 개요는 [SQL Database 서비스 정보](sql-database-technical-overview.md)를 참조하세요.

