---
title: "PowerShell: 단일 Azure SQL Database 만들기 및 관리 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure SQL Database를 만들고 관리하는 방법에 대한 빠른 참조입니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>PowerShell을 사용하여 단일 Azure SQL Database 만들기 및 관리

[Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, REST API 또는 C#을 사용하여 단일 Azure SQL Database를 만들고 관리할 수 있습니다. 이 항목에서는 PowerShell 사용에 대해 설명합니다. Azure Portal의 경우 [Azure Portal을 사용하여 단일 Azure SQL Database 만들기 및 관리](sql-database-manage-single-databases-powershell.md)를 참조하세요. Transact-SQL의 경우 [Transact-SQL을 사용하여 단일 데이터베이스 만들기 및 관리](sql-database-manage-single-databases-tsql.md)를 참조하세요. 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>PowerShell을 사용하여 Azure SQL Database 만들기

SQL 데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase) cmdlet을 사용합니다. 리소스 그룹 및 서버가 구독에 이미 있어야 합니다. 

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

> [!TIP]
> 샘플 스크립트는 [SQL 데이터베이스 PowerShell 스크립트 만들기](sql-database-get-started-powershell.md)를 참조하세요.
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>단일 데이터베이스의 서비스 계층 및 성능 수준 변경

[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet를 실행하고 **-RequestedServiceObjectiveName**을 원하는 가격 책정 계층의 성능 수준(예: *S0*, *S1*, *S2*, *S3*, *P1*, *P2*...)으로 설정합니다.

```{variables}```를 원하는 값으로 바꿉니다(중괄호를 포함하지 않음).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>다음 단계
* 관리 도구에 대한 개요는 [관리 도구 개요](sql-database-manage-overview.md)를 참조하세요.
* Azure Portal을 사용하여 관리 작업을 수행하는 방법을 보려면 [Azure Portal을 사용하여 Azure SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.
* PowerShell을 사용하여 관리 작업을 수행하는 방법을 보려면 [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.
* SQL Server Management Studio를 사용하여 관리 작업을 수행하는 방법을 보려면 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)를 참조하세요.
* SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 
* Azure 데이터베이스 서버 및 데이터베이스 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.



<!--HONumber=Feb17_HO2-->


