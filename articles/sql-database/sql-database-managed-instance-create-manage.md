---
title: Azure SQL Database Managed Instance에 대한 관리 API 참조 | Microsoft Docs
description: Azure SQL Database Managed Instance를 만들고 관리하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 30efbcd5ba36ea912a7238eef9e300085cb1d3d1
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040203"
---
# <a name="managed-api-reference-for-azure-sql-database-managed-instances"></a>Azure SQL Database Managed Instance에 대한 관리 API 참조

Azure Portal, PowerShell, Azure CLI, REST API 및 Transact-SQL을 사용하여 Azure SQL Database Managed Instance를 만들고 관리할 수 있습니다. 이 문서에서는 Managed Instance를 만들고 구성하는 데 사용할 수 있는 기능 및 API 개요를 찾을 수 있습니다.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Managed Instance 만들기

Azure SQL Database Managed Instance를 만드는 방법을 보여 주는 빠른 시작은 [빠른 시작: Azure SQL Database Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.

## <a name="powershell-create-and-manage-a-managed-instance"></a>PowerShell: Managed Instance 만들기 및 관리

Azure PowerShell을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

> [!TIP]
> PowerShell 예제 스크립트에 대해서는 [빠른 시작 스크립트: PowerShell 라이브러리를 사용하여 Azure SQL Managed Instance 만들기](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)를 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstance)|Azure SQL Database Managed Instance를 만듭니다. |
|[Get-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstance)|Azure SQL Managed Instance에 대한 정보를 반환합니다.|
|[Set-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlinstance)|Azure SQL Database Managed Instance에 대한 속성을 설정합니다.|
|[Remove-AzureRmSqlInstance](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstance)|Azure SQL Database Managed Instance를 제거합니다.|
|[New-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlinstancedatabase)|Azure SQL Database Managed Instance 데이터베이스를 만듭니다.|
|[Get-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlinstancedatabase)|Azure SQL Managed Instance 데이터베이스에 대한 정보를 반환합니다.|
|[Remove-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/remove-azurermsqlinstancedatabase)|Azure SQL Database Managed Instance 데이터베이스를 제거합니다.|
|[Restore-AzureRmSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqlinstancedatabase)|Azure SQL Database Managed Instance 데이터베이스를 복원합니다.|

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: 논리 서버 및 데이터베이스 관리

[Azure CLI](/cli/azure)를 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 [Azure CLI SQL Managed Instance](/cli/azure/sql/mi) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다.

> [!TIP]
> Azure CLI 빠른 시작을 보려면 [Azure CLI를 사용하여 SQL Managed Instance 작업](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[az sql mi create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) |Managed Instance를 만듭니다.|
|[az sql mi list](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-list)|사용 가능한 Managed Instance를 나열합니다.|
|[az sql mi show](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-show)|Managed Instance에 대한 세부 정보를 가져옵니다.|
|[az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)|Managed Instance를 업데이트합니다.|
|[az sql mi delete](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-delete)|Managed Instance를 제거합니다.|
|[az sql midb create](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-create) |관리되는 데이터베이스를 만듭니다.|
|[az sql midb list](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-list)|사용 가능한 관리되는 데이터베이스를 나열합니다.|
|[az sql midb restore](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-restore)|관리되는 데이터베이스를 복원합니다.|
|[az sql midb update](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-update)|관리되는 데이터베이스를 업데이트합니다.|
|[az sql midb delete](https://docs.microsoft.com/cli/azure/sql/midb#az-sql-midb-delete)|관리되는 데이터베이스를 제거합니다.|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: 논리 서버 및 데이터베이스 관리

Managed Instance를 만든 후에 Azure SQL Database Managed Instance 데이터베이스를 만들고 관리하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하고 Transact-SQL 명령을 전달할 수 있는 기타 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다.

> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용하여 Managed Instance를 구성하고 연결하는 방법을 나타내는 빠른 시작을 보려면 [빠른 시작: Azure SQL Database Managed Instance에 연결하도록 Azure VM 구성](sql-database-managed-instance-configure-vm.md) 및 [빠른 시작: 온-프레미스에서 Azure SQL Database Managed Instance로의 지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.
> [!IMPORTANT]
> Transact-SQL을 사용하여 Managed Instance를 만들거나 삭제할 수 없습니다.

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current)|새 Managed Instance 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) |Azure SQL Managed Instance 데이터베이스를 수정합니다.|

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API: 논리 서버 및 데이터베이스 관리

Azure SQL Database Managed Instance를 만들고 관리하려면 다음 REST API 요청을 사용하세요.

| 명령 | 설명 |
| --- | --- |
|[Managed Instances - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)|Managed Instance를 만들거나 업데이트합니다.|
|[Managed Instances - Delete](https://docs.microsoft.com/rest/api/sql/managedinstances/delete)|Managed Instance를 삭제합니다.|
|[Managed Instances - Get](https://docs.microsoft.com/rest/api/sql/managedinstances/get)|Managed Instance를 가져옵니다.|
|[Managed Instances - List](https://docs.microsoft.com/rest/api/sql/managedinstances/list)|구독에서 Managed Instance 목록을 반환합니다.|
|[Managed Instances - List By Resource Group](https://docs.microsoft.com/rest/api/sql/managedinstances/listbyresourcegroup)|리소스 그룹에서 Managed Instance 목록을 반환합니다.|
|[Managed Instances - Update](https://docs.microsoft.com/rest/api/sql/managedinstances/update)|Managed Instance를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.
