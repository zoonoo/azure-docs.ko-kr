---
title: "PowerShell: Azure SQL Database 서버 만들기 및 관리 | Microsoft Docs"
description: "PowerShell을 사용하여 Azure SQL Database 서버를 만들고 관리하는 방법에 대한 빠른 참조입니다."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 144774c9106bf5a0e389c99075c822d1c5282692
ms.openlocfilehash: c22243db06c783bc4f5b311aa4d49ce12032ab02


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>PowerShell을 사용하여 Azure SQL Database 서버 만들기 및 관리

[Azure Portal](https://portal.azure.com/), PowerShell, REST API 또는 C#을 사용하여 Azure SQL Database 서버를 만들고 관리할 수 있습니다. 이 항목에서는 PowerShell 사용에 대해 설명합니다. Azure Portal의 경우 [Azure Portal을 사용하여 서버 관리 및 만들기](sql-database-manage-servers-portal.md)를 참조하세요. 

## <a name="create-an-azure-sql-database-server-using-powershell"></a>PowerShell을 사용하여 Azure SQL Database 서버 만들기

SQL 데이터베이스 서버를 만들려면 [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver) cmdlet을 사용합니다. *server1* 을 서버의 이름으로 바꿉니다. 서버 이름은 모든 Azure SQL 데이터베이스 서버 간에 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 이 명령을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 리소스 그룹이 구독에 이미 있어야 합니다.

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

> [!TIP]
> 샘플 스크립트는 [PowerShell 스크립트를 사용하여 SQL 데이터베이스 만들기](sql-database-get-started-powershell.md)를 참조하세요.
>

## <a name="next-steps"></a>다음 단계
* 관리 도구에 대한 개요는 [관리 도구 개요](sql-database-manage-overview.md)를 참조하세요.
* Azure Portal을 사용하여 관리 작업을 수행하는 방법을 보려면 [Azure Portal을 사용하여 Azure SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.
* PowerShell을 사용하여 관리 작업을 수행하는 방법을 보려면 [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.
* SQL Server Management Studio를 사용하여 추가 작업을 수행하는 방법을 보려면 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)를 참조하세요.
* SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 
* Azure 데이터베이스 서버 및 데이터베이스 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.



<!--HONumber=Feb17_HO3-->


