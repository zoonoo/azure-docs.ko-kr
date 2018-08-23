---
title: Azure SQL Data Warehouse용 PowerShell cmdlet
description: 데이터베이스를 일시 중지하고 다시 시작하는 방법을 포함하여 Azure SQL Data Warehouse용 PowerShell cmdlet을 확인합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 82e635f58ab559480b55df6cee8e966c8d32bf01
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2018
ms.locfileid: "42144855"
---
# <a name="powershell-cmdlets-and-rest-apis-for-sql-data-warehouse"></a>SQL Data Warehouse용 PowerShell cmdlet 및 REST API
많은 SQL Data Warehouse 관리 작업을 Azure PowerShell cmdlet 또는 REST API를 사용하여 관리할 수 있습니다.  다음은 PowerShell 명령을 사용하여 SQL Data Warehouse의 일반적인 작업을 자동화하는 방법에 대한 몇 가지 예제입니다.  유용한 REST 예제는 [REST를 사용하여 확장성 관리][Manage scalability with REST] 문서를 참조하세요.

> [!NOTE]
> SQL Data Warehouse에서 Azure PowerShell을 사용하려면 Azure PowerShell 버전 1.0.3 이상을 설치해야 합니다.  **Get-Module -ListAvailable -Name Azure**를 실행하여 버전을 확인할 수 있습니다.  최신 버전은 [Microsoft 웹 플랫폼 설치 관리자][Microsoft Web Platform Installer]를 통해 설치할 수 있습니다.  최신 버전 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법][How to install and configure Azure PowerShell]을 참조하세요.
> 
> 

## <a name="get-started-with-azure-powershell-cmdlets"></a>Azure PowerShell Cmdlet 시작
1. Windows PowerShell을 엽니다.
2. PowerShell 프롬프트에서 다음 명령을 실행하여 Azure Resource Manager에 로그인하고 구독을 선택합니다.
   
    ```PowerShell
    Connect-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

## <a name="pause-sql-data-warehouse-example"></a>SQL Data Warehouse 일시 중지 예제
"Server01."라는 서버에서 호스트하는 "Database02"라는 데이터베이스를 일시 중지합니다.  서버는 "ResourceGroup1."이라는 Azure 리소스 그룹 내에 있습니다.

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```
변형인 이 예제에서는 검색된 개체를 [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]에 파이프합니다.  그 결과로 데이터베이스가 일시 중지됩니다. 마지막 명령은 결과를 보여 줍니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## <a name="start-sql-data-warehouse-example"></a>SQL Data Warehouse 시작 예제
"Server01."이라는 서버에서 호스트하는 "Database02"라는 데이터베이스의 작동을 다시 시작합니다. 서버는 "ResourceGroup1."이라는 리소스 그룹 내에 있습니다.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

변형인 이 예에서는 "ResourceGroup1."이라는 리소스 그룹에 포함된 "Server01"이라는 서버에서 "Database02"라는 데이터베이스를 검색합니다. 검색된 개체를 [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]에 파이프합니다.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

> [!NOTE]
> 서버가 foo.database.windows.net인 경우 PowerShell cmdlet의 ServerName으로 "foo"를 사용합니다.
> 
> 

## <a name="other-supported-powershell-cmdlets"></a>지원되는 기타 PowerShell cmdlet
다음 PowerShell cmdlet은 Azure SQL Data Warehouse에서 지원됩니다.

* [Get-AzureRmSqlDatabase][Get-AzureRmSqlDatabase]
* [Get-AzureRmSqlDeletedDatabaseBackup][Get-AzureRmSqlDeletedDatabaseBackup]
* [Get-AzureRmSqlDatabaseRestorePoints][Get-AzureRmSqlDatabaseRestorePoints]
* [New-AzureRmSqlDatabase][New-AzureRmSqlDatabase]
* [Remove-AzureRmSqlDatabase][Remove-AzureRmSqlDatabase]
* [Restore-AzureRmSqlDatabase][Restore-AzureRmSqlDatabase]
* [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]
* [Select-AzureRmSubscription][Select-AzureRmSubscription]
* [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]
* [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]

## <a name="next-steps"></a>다음 단계
더 많은 PowerShell 예제는 다음을 참조하세요.

* [Powershell을 사용하여 SQL Data Warehouse 만들기][Create a SQL Data Warehouse using PowerShell]
* [데이터베이스 복원][Database restore]

PowerShell로 자동화할 수 있는 다른 작업은 [Azure SQL Database Cmdlet][Azure SQL Database Cmdlets]을 참조하세요. 모든 Azure SQL Database cmdlet이 Azure SQL Data Warehouse에 대해 지원되는 것은 아닙니다.  REST로 자동화할 수 있는 작업 목록은 [Azure SQL Database에 대한 작업][Operations for Azure SQL Databases]을 참조하세요.

<!--Image references-->

<!--Article references-->
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Create a SQL Data Warehouse using PowerShell]: ./create-data-warehouse-powershell.md
[Database restore]: ./sql-data-warehouse-restore-database-powershell.md
[Manage scalability with REST]: ./sql-data-warehouse-manage-compute-rest-api.md

<!--MSDN references-->
[Azure SQL Database Cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.sql
[Operations for Azure SQL Databases]: https://msdn.microsoft.com/library/azure/dn505719.aspx
[Get-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqldatabase
[Get-AzureRmSqlDeletedDatabaseBackup]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDeletedDatabaseBackup
[Get-AzureRmSqlDatabaseRestorePoints]: https://docs.microsoft.com/powershell/module/azurerm.sql/get-AzureRmSqlDatabaseRestorePoints
[New-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/New-AzureRmSqlDatabase
[Remove-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Remove-AzureRmSqlDatabase
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Restore-AzureRmSqlDatabase
[Resume-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Resume-AzureRmSqlDatabase
<!-- It appears that Select-AzureRmSubscription isn't documented, so this points to Select-AzureSubscription -->
[Select-AzureRmSubscription]: https://msdn.microsoft.com/library/dn722499.aspx
[Set-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Set-AzureRmSqlDatabase
[Suspend-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/Suspend-AzureRmSqlDatabase

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
