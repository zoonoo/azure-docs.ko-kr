---
title: 탄력적 풀 만들기 및 관리 - Azure SQL database | Microsoft Docs
description: Azure SQL 탄력적 풀을 만들고 관리합니다.
keywords: 여러 데이터베이스, 데이터베이스 리소스, 데이터베이스 성능
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.reviewer: carlrab
ms.openlocfilehash: 3cdc82d71c05298aa5822b87c22edcc5d8e73385
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36311357"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Azure SQL Database에서 탄력적 풀 만들기 및 관리

탄력적 풀에서는 탄력적 풀이 해당 데이터베이스의 워크로드를 다루는 데 필요한 리소스 양 및 각 풀링된 데이터베이스의 리소스 양을 결정합니다. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure Portal: 탄력적 풀 및 풀링된 데이터베이스 관리

모든 풀 설정을 한 곳, **풀 구성** 블레이드에서 확인할 수 있습니다. 이 블레이드를 가져오려면 포털에서 탄력적 풀을 찾고 블레이드 상단 또는 왼쪽의 리소스 메뉴에서 **풀 구성**을 클릭합니다.

여기에서 다음 변경 내용의 조합을 만들고 하나의 일괄 처리에 모두를 저장할 수 있습니다.
1. 풀의 서비스 계층 변경
2. 성능(DTU 또는 vCore) 및 저장소 확장 또는 축소
3. 풀에/에서 데이터베이스 추가 또는 제거
4. 풀의 데이터베이스에 대한 최소(보장) 및 최대 성능 제한 설정
5. 새 선택의 결과로 청구서에 대한 변경 내용을 보기 위해 비용 요약 검토

![탄력적 풀 구성 블레이드](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: 탄력적 풀 및 풀링된 데이터베이스 관리 

Azure PowerShell을 사용하여 SQL Database 탄력적 풀 및 풀링된 데이터베이스를 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 데이터베이스, 서버 및 방화벽 규칙을 만들고 관리하려면 [PowerShell을 사용하여 Azure SQL Database 서버 및 데이터베이스 만들기 및 관리](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell)를 참조하세요.

> [!TIP]
> PowerShell 예제 스크립트의 경우 [PowerShell을 사용하여 탄력적 풀 만들기 및 풀 간에 데이터베이스 이동](scripts/sql-database-move-database-between-pools-powershell.md) 및 [PowerShell을 사용하여 Azure SQL Database에서 SQL 탄력적 풀 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀을 만듭니다.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|논리 SQL Server에서 탄력적 풀과 해당 속성 값을 가져옵니다.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀의 속성을 수정합니다. 예를 들어 **StorageMB** 속성을 사용하여 탄력적 풀의 최대 저장소를 수정합니다.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀을 삭제합니다.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|논리 SQL Server에서 탄력적 풀에 대한 작업 상태를 가져옵니다.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스를 가져옵니다.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성을 설정하거나 기존 데이터베이스와 탄력적 풀 사이를 이동합니다.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|데이터베이스를 제거합니다.|


> [!TIP]
> 탄력적 풀에 많은 수의 데이터베이스를 만드는 작업은 한 번에 단일 데이터베이스만을 만들 수 있는 포털 또는 PowerShell cmdlet을 사용하는 경우와 같은 시간이 걸릴 수 있습니다. 탄력적 풀로 만들기를 자동화하려면 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)를 참조하세요.
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: 탄력적 풀 및 풀링된 데이터베이스 관리

[Azure CLI](/cli/azure)를 사용하여 SQL Database 탄력적 풀을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다.

> [!TIP]
> Azure CLI 예제 스크립트의 경우 [CLI를 사용하여 SQL 탄력적 풀에서 Azure SQL Database 이동](scripts/sql-database-move-database-between-pools-cli.md) 및 [Azure CLI를 사용하여 Azure SQL Database에서 SQL 탄력적 풀 크기 조정](scripts/sql-database-scale-pool-cli.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|탄력적 풀을 만듭니다.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|사용 가능한 풀 DTU 설정, 저장소 용량 한도 및 데이터베이스별 설정이 포함됩니다. 세부 정보 표시를 줄이기 위해 추가 저장소 용량 한도 및 데이터베이스별 설정은 기본적으로 숨겨져 있습니다.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|탄력적 풀을 업데이트합니다.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|탄력적 풀을 삭제합니다.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: 풀링된 데이터베이스 관리

기존 탄력적 풀 내에서 데이터베이스를 만들고 이동하거나, Transact-SQL을 사용하여 SQL Database 탄력적 풀에 대한 정보를 반환하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 데이터베이스, 서버 및 방화벽 규칙을 만들고 관리하려면 [Transact-SQL을 사용하여 Azure SQL Database 서버 및 데이터베이스 만들기 및 관리](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql)를 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 Azure SQL Database 탄력적 풀을 만들거나, 업데이트하거나 삭제할 수는 없습니다. 탄력적 풀에서 데이터베이스를 추가하거나 제거할 수는 있으며, DMV를 사용하여 기존 탄력적 풀에 대한 정보를 반환할 수 있습니다.
>

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |탄력적 풀 간에 데이터베이스를 이동합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|논리 서버에서 모든 Elastic Database 풀에 대한 리소스 사용 통계를 반환합니다. 각 Elastic Database 풀의 경우 매 15초 보고 창에 대해 한 행이 있습니다(분당 4개 행). 여기에는 풀의 모든 데이터베이스에 의한 CPU, IO, 로그, 저장소 계산 및 동시 요청/세션 사용률이 포함됩니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: 탄력적 풀 및 풀링된 데이터베이스 관리

SQL Database 탄력적 풀 및 풀링된 데이터베이스를 만들고 관리하려면 다음 REST API 요청을 사용합니다.

| 명령 | 설명 |
| --- | --- |
|[탄력적 풀 - Create 또는 Update](/rest/api/sql/elasticpools/createorupdate)|새 탄력적 풀을 만들거나 기존 탄력적 풀을 업데이트합니다.|
|[탄력적 풀 - Delete](/rest/api/sql/elasticpools/delete)|탄력적 풀을 삭제합니다.|
|[탄력적 풀 - Get](/rest/api/sql/elasticpools/get)|탄력적 풀을 가져옵니다.|
|[탄력적 풀 - List By Server](/rest/api/sql/elasticpools/listbyserver)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[탄력적 풀 - Update](/rest/api/sql/elasticpools/update)|기준 탄력적 풀을 업데이트합니다.|
|[권장되는 탄력적 풀 - Get](/rest/api/sql/recommendedelasticpools/get)|권장되는 탄력적 풀을 가져옵니다.|
|[권장되는 탄력적 풀 - List By Server](/rest/api/sql/recommendedelasticpools/listbyserver)|권장되는 탄력적 풀을 반환합니다.|
|[권장되는 탄력적 풀 - List Metrics](/rest/api/sql/recommendedelasticpools/listmetrics)|권장되는 탄력적 풀 메트릭을 반환합니다.|
|[탄력적 풀 활동](/rest/api/sql/elasticpoolactivities)|탄력적 풀 활동을 반환합니다.|
|[탄력적 풀 데이터베이스 활동](/rest/api/sql/elasticpooldatabaseactivities)|탄력적 풀 내에서 데이터베이스에 대한 활동을 반환합니다.|
|[데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[데이터베이스 - Get](/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스 목록을 반환합니다.|
|[데이터베이스 - List By Server](/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

* 비디오는 [Azure SQL Database 탄력적 기능에 대한 Microsoft Virtual Academy 비디오 과정](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)을 참조하세요.
* 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.
* 탄력적 풀을 사용하는 SaaS 자습서의 경우 [Wingtip SaaS 응용 프로그램 소개](sql-database-wtp-overview.md)를 참조하세요.
