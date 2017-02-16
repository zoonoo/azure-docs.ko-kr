---
title: "Azure SQL Database 만들기 | Microsoft Docs"
description: "Azure Portal, PowerShell, Transact-SQL을 사용하여 Azure SQL Database를 만드는 방법에 대한 빠른 참조입니다."
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
ms.date: 02/01/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0a647294b41b7f9ce386b47cf0501a92486b80cc
ms.openlocfilehash: aca33871d83a34769fd2b09be4b382c872a65f0a


---
# <a name="create-an-azure-sql-database"></a>Azure SQL 데이터베이스 만들기

[Azure Portal](https://portal.azure.com/), PowerShell, Transact-SQL, REST API 또는 C#을 사용하여 Azure SQL Database를 만들 수 있습니다. 

## <a name="create-an-azure-sql-database-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL Database 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **SQL Database** 블레이드를 엽니다. 

    ![SQL 데이터베이스](./media/sql-database-get-started/sql-databases.png)
2. SQL 데이터베이스 블레이드에서 **추가**를 클릭합니다.

    ![SQL 데이터베이스 추가](./media/sql-database-get-started/add-sql-database.png)

> [!TIP]
> Azure Portal 및 SQL Server Management Studio를 사용하여 자습서 시작하기는 [Azure Portal 및 SQL Server Management Studio를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started.md)을 참조하세요.
>

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

## <a name="create-an-azure-sql-database-using-transact-sql-in-sql-server-management-studio"></a>SQL Server Management Studio의 Transact-SQL을 사용하여 Azure SQL Database 만들기

SQL Server Management Studio에서 Transact-SQL을 사용하여 SSQL Database를 만들려면 다음을 수행합니다.

1. SQL Server Management Studio에서는 서버 수준 보안 주체 로그인 또는 **dbmanager** 역할의 구성원인 로그인을 사용하여 Azure 데이터베이스 서버에 연결합니다. 로그인에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.
2. 개체 탐색기에서 데이터베이스 노드를 열고 **시스템 데이터베이스** 폴더를 확장한 후 **master**를 마우스 오른쪽 단추로 클릭한 다음 **새 쿼리**를 클릭합니다.
3. 데이터베이스를 만들려면 **CREATE DATABASE** 문을 사용하세요. 자세한 내용은 [CREATE DATABASE(SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx)를 참조하세요. 다음 문은 **myTestDB** 라는 데이터베이스를 만들고 기본 최대 크기가 250GB인 Standard S0 Edition 데이터베이스로 지정합니다.
  
      CREATE DATABASE myTestDB    (EDITION='Standard',     SERVICE_OBJECTIVE='S0');

4. **실행** 을 클릭하여 쿼리를 실행합니다.
5. 개체 탐색기에서 데이터베이스 노드를 마우스 오른쪽 단추로 클릭하고 **새로 고침**을 클릭하여 개체 탐색기에서 새 데이터베이스를 봅니다. 

> [!TIP]
> Azure Portal 및 SQL Server Management Studio를 사용하여 자습서 시작하기는 [Azure Portal 및 SQL Server Management Studio를 사용하여 Azure SQL Database 서버, 데이터베이스 및 방화벽 규칙 시작](sql-database-get-started.md)을 참조하세요.
>

## <a name="additional-resources"></a>추가 리소스
* 관리 도구에 대한 개요는 [관리 도구 개요](sql-database-manage-overview.md)를 참조하세요.
* Azure Portal을 사용하여 관리 작업을 수행하는 방법을 보려면 [Azure Portal을 사용하여 Azure SQL Database 관리](sql-database-manage-portal.md)를 참조하세요.
* PowerShell을 사용하여 관리 작업을 수행하는 방법을 보려면 [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md)를 참조하세요.
* SQL Server Management Studio를 사용하여 관리 작업을 수행하는 방법을 보려면 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)를 참조하세요.
* SQL Database 서비스에 대한 정보는 [SQL Database 정의](sql-database-technical-overview.md)를 참조하세요. 
* Azure 데이터베이스 서버 및 데이터베이스 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.



<!--HONumber=Dec16_HO3-->


