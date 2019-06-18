---
title: 탄력적 풀-Azure SQL database 관리 | Microsoft Docs
description: Azure SQL 탄력적 풀을 만들고 관리합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: debf91f04cff3cb9705ebc5915e2e665679230a9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66143319"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Azure SQL Database의 탄력적 풀 관리

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

Azure PowerShell을 사용하여 SQL Database 탄력적 풀 및 풀링된 데이터베이스를 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 탄력적 풀용 SQL Database 서버를 만들고 관리하려면 [SQL Database 서버 만들기 및 관리](sql-database-servers.md)를 참조하세요. 방화벽 규칙을 만들고 관리하려면 [PowerShell을 사용하여 방화벽 규칙 만들기 및 관리](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell)를 참조하세요.

> [!TIP]
> PowerShell 예제 스크립트의 경우 [PowerShell을 사용하여 탄력적 풀 만들기 및 풀 간에 데이터베이스 이동](scripts/sql-database-move-database-between-pools-powershell.md) 및 [PowerShell을 사용하여 Azure SQL Database에서 SQL 탄력적 풀 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|탄력적 풀을 만듭니다.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|탄력적 풀과 해당 속성 값을 가져옵니다.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|탄력적 풀의 속성을 수정합니다. 예를 들어 **StorageMB** 속성을 사용하여 탄력적 풀의 최대 스토리지를 수정합니다.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|탄력적 풀을 삭제합니다.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|탄력적 풀에 대한 작업 상태를 가져옵니다.|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|하나 이상의 데이터베이스를 가져옵니다.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|데이터베이스의 속성을 설정하거나 기존 데이터베이스와 탄력적 풀 사이를 이동합니다.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|데이터베이스를 제거합니다.|

> [!TIP]
> 탄력적 풀에 많은 수의 데이터베이스를 만드는 작업은 한 번에 단일 데이터베이스만을 만들 수 있는 포털 또는 PowerShell cmdlet을 사용하는 경우와 같은 시간이 걸릴 수 있습니다. 탄력적 풀로 만들기를 자동화하려면 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)를 참조하세요.

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: 탄력적 풀 및 풀링된 데이터베이스 관리

[Azure CLI](/cli/azure)를 사용하여 SQL Database 탄력적 풀을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다.

> [!TIP]
> Azure CLI 예제 스크립트의 경우 [CLI를 사용하여 SQL 탄력적 풀에서 Azure SQL 데이터베이스 이동](scripts/sql-database-move-database-between-pools-cli.md) 및 [Azure CLI를 사용하여 Azure SQL Database에서 SQL 탄력적 풀 크기 조정](scripts/sql-database-scale-pool-cli.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|탄력적 풀을 만듭니다.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|사용 가능한 풀 DTU 설정, 저장소 용량 한도 및 데이터베이스별 설정이 포함됩니다. 세부 정보 표시를 줄이기 위해 추가 저장소 용량 한도 및 데이터베이스별 설정은 기본적으로 숨겨져 있습니다.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|탄력적 풀을 업데이트합니다.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|탄력적 풀을 삭제합니다.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: 풀링된 데이터베이스 관리

기존 탄력적 풀 내에서 데이터베이스를 만들고 이동하거나, Transact-SQL을 사용하여 SQL Database 탄력적 풀에 대한 정보를 반환하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . T-SQL을 사용하여 방화벽 규칙을 만들고 관리하려면 [T-SQL을 사용하여 방화벽 규칙 관리](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql)를 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 Azure SQL Database 탄력적 풀을 만들거나, 업데이트하거나 삭제할 수는 없습니다. 탄력적 풀에서 데이터베이스를 추가하거나 제거할 수는 있으며, DMV를 사용하여 기존 탄력적 풀에 대한 정보를 반환할 수 있습니다.
>

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE(Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |탄력적 풀 간에 데이터베이스를 이동합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|SQL Database 서버에서 모든 탄력적 풀에 대한 리소스 사용량 통계를 반환합니다. 각 탄력적 풀에는 15초의 보고 기간마다 행이 하나씩 있습니다(분당 행 4개) 여기에는 풀의 모든 데이터베이스에 의한 CPU, IO, 로그, 저장소 계산 및 동시 요청/세션 사용률이 포함됩니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL 데이터베이스 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: 탄력적 풀 및 풀링된 데이터베이스 관리

SQL Database 탄력적 풀 및 풀링된 데이터베이스를 만들고 관리하려면 다음 REST API 요청을 사용합니다.

| 명령 | 설명 |
| --- | --- |
|[탄력적 풀 - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|새 탄력적 풀을 만들거나 기존 탄력적 풀을 업데이트합니다.|
|[탄력적 풀 - Delete](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|탄력적 풀을 삭제합니다.|
|[탄력적 풀 - Get](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|탄력적 풀을 가져옵니다.|
|[탄력적 풀 - List By Server](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[탄력적 풀 - Update](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|기준 탄력적 풀을 업데이트합니다.|
|[탄력적 풀 활동](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|탄력적 풀 활동을 반환합니다.|
|[탄력적 풀 데이터베이스 활동](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|탄력적 풀 내에서 데이터베이스에 대한 활동을 반환합니다.|
|[데이터베이스 - Create 또는 Update](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[데이터베이스 - Get](https://docs.microsoft.com/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - List by elastic pool](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List by server](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](https://docs.microsoft.com/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

* 탄력적 풀을 사용한 SaaS 애플리케이션의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.
* 탄력적 풀을 사용하는 SaaS 자습서의 경우 [Wingtip SaaS 애플리케이션 소개](sql-database-wtp-overview.md)를 참조하세요.
