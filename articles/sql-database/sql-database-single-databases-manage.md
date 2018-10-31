---
title: Azure SQL Server 및 단일 데이터베이스 만들기 및 관리 | Microsoft Docs
description: 논리 서버 및 단일 데이터베이스를 만들고 관리하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: ac3ce8a417a63ce0a91339c38c2a5ae8b3013228
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466786"
---
# <a name="create-and-manage-logical-servers-and-single-databases-in-azure-sql-database"></a>Azure SQL Database에서 논리 서버 및 단일 데이터베이스 만들기 및 관리

Azure Portal, PowerShell, Azure CLI, REST API 및 Transact-SQL을 사용하여 Azure SQL 데이터베이스 논리 서버 및 단일 데이터베이스를 만들고 관리할 수 있습니다.

## <a name="azure-portal-manage-logical-servers-and-databases"></a>Azure Portal: 논리 서버 및 데이터베이스 관리

Azure SQL Database의 리소스 그룹을 미리 만들거나 서버 자체를 만드는 동안 만들 수 있습니다. 새 SQL Server를 만들거나 새 데이터베이스 만들기의 일부분으로 새 SQL Server 양식을 가져오는 여러 방법이 있습니다.

### <a name="create-a-blank-sql-server-logical-server"></a>비어 있는 SQL Server(논리 서버) 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 (데이터베이스 없이) Azure SQL Database 서버를 만들려면 비어 있는 SQL Server(논리 서버) 양식으로 이동합니다.  

### <a name="create-a-blank-or-sample-sql-database"></a>비어 있거나 샘플인 SQL Database 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 Azure SQL Database를 만들려면 비어 있는 SQL Database 양식으로 이동하고 요청된 정보를 제공합니다. Azure SQL Database의 리소스 그룹 및 논리 서버를 미리 만들거나 데이터베이스 자체를 만드는 동안 만들 수 있습니다. 비어 있는 데이터베이스를 만들거나 Adventure Works LT에 따라 샘플 데이터베이스를 만들 수 있습니다.

  ![create database-1](./media/sql-database-get-started-portal/create-database-1.png)

> [!IMPORTANT]
> 데이터베이스의 가격 책정 계층 선택에 대한 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요.

Managed Instance를 만들려면 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.

## <a name="manage-an-existing-sql-server"></a>기존 SQL Server 관리

기존 서버를 관리하려면 특정 SQL Database 페이지, **SQL server** 페이지 또는 **모든 리소스** 페이지와 같은 다양한 방법을 사용하여 서버로 이동합니다.

기존 데이터베이스를 관리하려면 **SQL Database** 페이지로 이동하고 관리하려는 데이터베이스를 클릭합니다. 다음 스크린샷에서는 데이터베이스의 **개요** 페이지에서 데이터베이스의 서버 수준 방화벽을 설정하기 시작하는 방법을 보여줍니다.

   ![서버 방화벽 규칙](./media/sql-database-get-started-portal/server-firewall-rule.png)

> [!IMPORTANT]
> 데이터베이스의 성능 속성을 구성하려면 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 참조하세요.
> [!TIP]
> Azure Portal 빠른 시작은 [Azure Portal에서 Azure SQL Database 만들기](sql-database-get-started-portal.md)를 참조하세요.

## <a name="powershell-manage-logical-servers-and-databases"></a>PowerShell: 논리 서버 및 데이터베이스 관리

Azure PowerShell을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요.

> [!TIP]
> PowerShell 빠른 시작은 [PowerShell을 사용하여 단일 Azure SQL Database 만들기](sql-database-get-started-portal.md)를 참조하세요. PowerShell 예제 스크립트에 대해서는 [PowerShell을 사용하여 단일 Azure SQL Database 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-powershell.md) 및 [PowerShell을 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|데이터베이스 만들기 |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스 가져오기|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성 설정 또는 기존 데이터베이스를 탄력적 풀로 이동|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|데이터베이스 제거|
|[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup)|리소스 그룹 만들기|
|[New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver)|서버 만들기|
|[Get-AzureRmSqlServer](/powershell/module/azurerm.sql/get-azurermsqlserver)|서버에 대한 정보 반환|
|[Set-AzureRmSqlServer](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqlserver)|서버의 속성 수정|
|[Remove-AzureRmSqlServer](/powershell/module/azurerm.sql/remove-azurermsqlserver)|서버 제거|
|[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule)|서버 수준 방화벽 규칙 만들기 |
|[Get-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/get-azurermsqlserverfirewallrule)|서버의 방화벽 규칙 가져오기|
|[Set-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/set-azurermsqlserverfirewallrule)|서버에서 방화벽 규칙 수정|
|[Remove-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/remove-azurermsqlserverfirewallrule)|서버에서 방화벽 규칙 삭제|
| New-AzureRmSqlServerVirtualNetworkRule | Virtual Network 서비스 엔드포인트인 서브넷을 기반으로 [*가상 네트워크 규칙*](sql-database-vnet-service-endpoint-rule-overview.md)을 만듭니다. |

## <a name="azure-cli-manage-logical-servers-and-databases"></a>Azure CLI: 논리 서버 및 데이터베이스 관리

[Azure CLI](/cli/azure)를 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. 탄력적 풀 만들기 및 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

> [!TIP]
> Azure CLI 빠른 시작은 [Azure CLI를 사용하여 단일 Azure SQL Database 만들기](sql-database-cli-samples.md)를 참조하세요. Azure CLI 예제 스크립트에 대해서는 [CLI를 사용하여 단일 Azure SQL Database 만들기 및 방화벽 규칙 구성](scripts/sql-database-create-and-configure-database-cli.md) 및 [CLI를 사용하여 단일 SQL Database 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-cli.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |데이터베이스 만들기|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|서버의 모든 데이터베이스 및 데이터 웨어하우스 또는 탄력적 풀의 모든 데이터베이스 나열|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|사용 가능한 서비스 목표 및 저장소 용량 제한 나열|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|데이터베이스 사용 정보 반환|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|데이터베이스 또는 데이터 웨어하우스 가져오기|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|데이터베이스 업데이트|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|데이터베이스 제거|
|[az group create](/cli/azure/group#az-group-create)|리소스 그룹 만들기|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|서버 만들기|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|서버 나열|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|서버 사용 반환|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|서버 가져오기|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|서버 업데이트|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|서버를 삭제합니다.|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|서버 방화벽 규칙 만들기|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|서버의 방화벽 규칙 나열|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|방화벽 규칙의 세부 정보 표시|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|방화벽 규칙 업데이트|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|방화벽 규칙 삭제|

## <a name="transact-sql-manage-logical-servers-and-databases"></a>Transact-SQL: 논리 서버 및 데이터베이스 관리

Transact-SQL을 사용하여 Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 T-SQL cmdlet을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 탄력적 풀 관리에 대해서는 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.

> [!TIP]
> Microsoft Windows에서 SQL Server Management Studio를 사용하는 빠른 시작은 [Azure SQL Database: SQL Server Management Studio를 사용하여 데이터에 연결 및 쿼리](sql-database-connect-query-ssms.md)를 참조하세요. Windows, Linux 또는 macOS에서 Visual Studio Code를 사용하는 빠른 시작은 [Azure SQL Database: Visual Studio Code를 사용하여 연결 및 데이터 쿼리](sql-database-connect-query-vscode.md)를 참조하세요.
> [!IMPORTANT]
> Transact-SQL을 사용하여 서버를 만들거나 삭제할 수 없습니다.

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Azure SQL 데이터베이스를 수정합니다. |
|[ALTER DATABASE(Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)|Azure SQL Data Warehouse를 수정합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|
|[sys.dm_db_resource_stats(Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL Database 데이터베이스에 대한 CPU, IO 및 메모리 소비량을 반환합니다. 데이터베이스에 작업이 없는 경우에도 한 행은 15초 간격으로 존재합니다.|
|[sys.resource_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL Database에 대한 CPU 사용량 및 저장소 데이터를 반환합니다. 데이터는 5분 미만 간격으로 수집되고 집계됩니다.|
|[sys.database_connection_stats(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|SQL Database 데이터베이스 연결 이벤트에 대한 통계를 포함하며 데이터베이스 연결 성공 및 실패에 대한 개요를 제공합니다. |
|[sys.event_log(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|성공적인 Azure SQL Database 데이터베이스 연결, 연결 실패 및 교착 상태를 반환합니다. 이 정보를 사용하여 SQL Database의 데이터베이스 작업을 추적하거나 문제를 해결할 수 있습니다.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|SQL Database 서버에 서버 수준 방화벽 설정을 만들거나 업데이트합니다. 이 저장 프로시저는 마스터 데이터베이스에서 서버 수준 보안 주체 로그인에만 사용할 수 있습니다. Azure 수준 사용 권한 가진 사용자가 첫 번째 서버 수준 방화벽 규칙을 만든 후에만 Transact-SQL을 사용하여 서버 수준 방화벽 규칙을 다시 만들 수 있습니다.|
|[sys.firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Microsoft Azure SQL Database와 연결된 서버 수준 방화벽 설정에 대한 정보를 반환합니다.|
|[sp_delete_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|SQL Database 서버에서 서버 수준 방화벽 설정을 제거합니다. 이 저장 프로시저는 마스터 데이터베이스에서 서버 수준 보안 주체 로그인에만 사용할 수 있습니다.|
|[sp_set_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL Database 또는 SQL Data Warehouse에서 데이터베이스 수준 방화벽 규칙을 만들거나 업데이트합니다. 마스터 데이터베이스와 SQL Database에서 사용자 데이터베이스에서 데이터베이스 방화벽 규칙을 구성할 수 있습니다. 데이터베이스 방화벽 규칙은 포함된 데이터베이스 사용자를 사용하는 경우에 유용합니다. |
|[sys.database_firewall_rules(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Microsoft Azure SQL Database와 연결된 데이터베이스 수준 방화벽 설정에 대한 정보를 반환합니다. |
|[sp_delete_database_firewall_rule(Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Azure SQL Database 또는 SQL Data Warehouse에서 데이터베이스 수준 방화벽 설정을 제거합니다. |

## <a name="rest-api-manage-logical-servers-and-databases"></a>REST API: 논리 서버 및 데이터베이스 관리

Azure SQL Server, 데이터베이스 및 방화벽을 만들고 관리하려면 다음 REST API 요청을 사용하세요.

| 명령 | 설명 |
| --- | --- |
|[Servers - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|새 서버를 만들거나 업데이트합니다.|
|[Servers - Delete](https://docs.microsoft.com/rest/api/sql/servers/delete)|SQL 서버를 삭제합니다.|
|[Servers - Get](https://docs.microsoft.com/rest/api/sql/servers/get)|서버를 가져옵니다.|
|[Servers - List](https://docs.microsoft.com/rest/api/sql/servers/list)|서버 목록을 반환합니다.|
|[Servers - List By Resource Group](https://docs.microsoft.com/rest/api/sql/servers/listbyresourcegroup)|리소스 그룹의 서버 목록을 반환합니다.|
|[Servers - Update](https://docs.microsoft.com/rest/api/sql/servers/update)|기존 서버를 업데이트합니다.|
|[데이터베이스 - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[Databases - Delete](https://docs.microsoft.com/rest/api/sql/databases/delete)|데이터베이스를 삭제합니다.|
|[데이터베이스 - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - List By Elastic Pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List By Server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](https://docs.microsoft.com/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|
|[Firewall Rules - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)|방화벽 규칙을 만들거나 업데이트합니다.|
|[Firewall Rules - Delete](https://docs.microsoft.com/rest/api/sql/firewallrules/delete)|방화벽 규칙을 삭제합니다.|
|[Firewall Rules - Get](https://docs.microsoft.com/rest/api/sql/firewallrules/get)|방화벽 규칙을 가져옵니다.|
|[Firewall Rules - List By Server](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver)|방화벽 규칙 목록을 반환합니다.|

## <a name="next-steps"></a>다음 단계

- SQL Server 데이터베이스를 Azure로 마이그레이션하는 방법에 대한 자세한 내용은 [Azure SQL Database로 마이그레이션](sql-database-cloud-migrate.md)을 참조하세요.
- 지원되는 기능에 대한 자세한 내용은 [기능](sql-database-features.md)을 참조하세요.
