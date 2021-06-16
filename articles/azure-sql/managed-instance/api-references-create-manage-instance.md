---
title: Azure SQL Managed Instance에 대한 관리 API 참조
description: Azure SQL Managed Instance의 관리형 인스턴스 만들기 및 구성에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: development
ms.devlang: ''
ms.topic: reference
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 03/12/2019
ms.openlocfilehash: c82fb7879c33c53721a3f7193ab55bd4803679d3
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707481"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance에 대한 관리 API 참조
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure Portal, PowerShell, Azure CLI, REST API, Transact-SQL을 사용하여 Azure SQL Managed Instance의 관리형 인스턴스를 만들고 구성할 수 있습니다. 이 문서에서는 관리형 인스턴스를 만들고 구성하는 데 사용할 수 있는 기능 및 API 개요를 제공합니다.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: 관리형 인스턴스 만들기

관리형 인스턴스를 만드는 방법을 보여 주는 빠른 시작은 [빠른 시작: 관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: 관리형 인스턴스 만들기 및 구성

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRM 모듈의 명령에 대한 인수는 사실상 동일합니다.

Azure PowerShell을 사용하여 Managed Instance를 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!TIP]
> PowerShell 예제 스크립트는 [빠른 시작 스크립트: PowerShell 라이브러리를 이용한 관리형 인스턴스 만들기](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)를 참조하세요.

| Cmdlet | Description |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|관리형 인스턴스를 만듭니다. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|관리형 인스턴스 관련 정보를 반환합니다.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|관리형 인스턴스의 속성을 설정합니다.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|관리형 인스턴스를 제거합니다.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|관리형 인스턴스 또는 특정 작업에서 수행되는 관리 작업의 목록을 가져옵니다.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|관리형 인스턴스에서 수행되는 특정 관리 작업을 취소합니다.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 만듭니다.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스 관련 정보를 반환합니다.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 제거합니다.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|SQL Managed Instance 데이터베이스를 복원합니다.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: 관리형 인스턴스 만들기 및 구성

[Azure CLI](/cli/azure)를 사용하여 관리형 인스턴스를 만들고 구성하려면 다음 [Azure CLI commands for SQL Managed Instance](/cli/azure/sql/mi) 명령을 사용합니다. [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다.

> [!TIP]
> Azure CLI 빠른 시작을 보려면 [Azure CLI를 사용하여 SQL Managed Instance 작업](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)을 참조하세요.

| Cmdlet | Description |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) |관리형 인스턴스를 만듭니다.|
|[az sql mi list](/cli/azure/sql/mi#az_sql_mi_list)|사용 가능한 관리형 인스턴스를 나열합니다.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|관리형 인스턴스 관련 세부 정보를 가져옵니다.|
|[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)|관리형 인스턴스를 업데이트합니다.|
|[az sql mi delete](/cli/azure/sql/mi#az_sql_mi_delete)|관리형 인스턴스를 제거합니다.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|관리형 인스턴스에서 수행한 관리 작업 목록을 가져옵니다.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|관리형 인스턴스에서 수행되는 특정 관리 작업을 가져옵니다.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|관리형 인스턴스에서 수행되는 특정 관리 작업을 취소합니다.|
|[az sql midb create](/cli/azure/sql/midb#az_sql_midb_create) |관리형 데이터베이스를 만듭니다.|
|[az sql midb list](/cli/azure/sql/midb#az_sql_midb_list)|사용 가능한 관리형 데이터베이스를 나열합니다.|
|[az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)|관리형 데이터베이스를 복원합니다.|
|[az sql midb delete](/cli/azure/sql/midb#az_sql_midb_delete)|관리형 데이터베이스를 제거합니다.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: 인스턴스 데이터베이스 만들기 및 구성

관리형 인스턴스를 만든 후에 인스턴스 데이터베이스를 만들고 구성하려면 다음 T-SQL 명령을 사용합니다. Azure portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio](/sql/azure-data-studio/what-is), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다.

> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용하여 관리형 인스턴스를 구성하고 연결하는 방법을 보여 주는 빠른 시작은 [빠른 시작: Azure SQL Managed Instance에 연결하도록 Azure VM 구성](connect-vm-instance-configure.md)과 [빠른 시작: 온-프레미스에서 Azure SQL Managed Instance로의 지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 관리형 인스턴스를 만들거나 삭제할 수 없습니다.

| 명령 | Description |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|SQL Managed Instance에서 새 인스턴스 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결해야 합니다.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |SQL Managed Instance에서 인스턴스 데이터베이스를 수정합니다.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: 관리형 인스턴스 만들기 및 구성

관리형 인스턴스를 만들고 구성하려면 다음 REST API 요청을 사용합니다.

| 명령 | Description |
| --- | --- |
|[Managed Instances - Create 또는 Update](/rest/api/sql/managedinstances/createorupdate)|관리형 인스턴스를 만들거나 업데이트합니다.|
|[Managed Instances - Delete](/rest/api/sql/managedinstances/delete)|관리형 인스턴스를 삭제합니다.|
|[Managed Instances - Get](/rest/api/sql/managedinstances/get)|관리형 인스턴스를 가져옵니다.|
|[Managed Instances - List](/rest/api/sql/managedinstances/list)|구독에서 관리형 인스턴스 목록을 반환합니다.|
|[Managed Instances - List By Resource Group](/rest/api/sql/managedinstances/listbyresourcegroup)|리소스 그룹에서 관리형 인스턴스 목록을 반환합니다.|
|[Managed Instances - Update](/rest/api/sql/managedinstances/update)|관리형 인스턴스를 업데이트합니다.|
|[관리형 인스턴스 작업 - 관리형 인스턴스별 나열](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|관리형 인스턴스에서 수행한 관리 작업 목록을 가져옵니다.|
|[관리형 인스턴스 작업 - 가져오기](/rest/api/sql/managedinstanceoperations/get)|관리형 인스턴스에서 수행되는 특정 관리 작업을 가져옵니다.|
|[관리형 인스턴스 작업 - 취소](/rest/api/sql/managedinstanceoperations/cancel)|관리형 인스턴스에서 수행되는 특정 관리 작업을 취소합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](../database/migrate-to-database-from-sql-server.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](../database/features-comparison.md)을 참조하세요.