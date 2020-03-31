---
title: IP 방화벽 규칙
description: SQL 데이터베이스 또는 SQL 데이터 웨어하우스 방화벽에 대한 서버 수준 IP 방화벽 규칙을 구성합니다. 액세스를 관리하고 단일 또는 풀로 구성된 데이터베이스에 대한 데이터베이스 수준 IP 방화벽 규칙을 구성합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531280"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스 IP 방화벽 규칙

> [!NOTE]
> 이 문서는 Azure SQL 서버와 Azure SQL 서버의 Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스 데이터베이스에 모두 적용됩니다. 간단히 하기 위해 *SQL 데이터베이스는* SQL 데이터베이스와 SQL 데이터 웨어하우스를 모두 참조하는 데 사용됩니다.

> [!IMPORTANT]
> 이 문서는 *Azure SQL Database Managed Instance*에 적용되지 *않습니다*. 네트워크 구성에 대한 자세한 내용은 [응용 프로그램을 Azure SQL Database 관리 인스턴스에 연결](sql-database-managed-instance-connect-app.md)을 참조하십시오.

예를 들어 *mysqlserver라는*새 Azure SQL 서버를 만들 때 SQL Database 방화벽은 *서버의*공용 끝점에 대한 모든 액세스를 차단합니다(mysqlserver.database.windows.net 액세스할 수 있음).

> [!IMPORTANT]
> SQL 데이터 웨어하우스는 서버 수준 IP 방화벽 규칙만 지원합니다. 데이터베이스 수준 IP 방화벽 규칙을 지원하지 않습니다.

## <a name="how-the-firewall-works"></a>방화벽 작동 방식
다음 다이어그램과 같이 인터넷 및 Azure의 연결 시도는 SQL 서버 또는 SQL 데이터베이스에 도달하기 전에 방화벽을 통과해야 합니다.

   ![방화벽 구성 다이어그램][1]

### <a name="server-level-ip-firewall-rules"></a>서버 수준 IP 방화벽 규칙

  클라이언트는 이 규칙에 따라 전체 Azure SQL Server, 즉 동일한 SQL Database 서버 내의 모든 데이터베이스에 액세스할 수 있습니다. 규칙은 *마스터* 데이터베이스에 저장됩니다. Azure SQL Server에 대해 최대 128개의 서버 수준 IP 방화벽 규칙을 가질 수 있습니다. Azure 서비스 **및 리소스가 이 서버 설정에 액세스할 수 있도록** 허용하는 경우 Azure SQL Server에 대한 단일 방화벽 규칙으로 계산됩니다.
  
  Azure 포털, PowerShell 또는 Transact-SQL 문을 사용하여 서버 수준 IP 방화벽 규칙을 구성할 수 있습니다.
  - 포털 또는 PowerShell을 사용하려면 구독 소유자 또는 구독 기여자여야 합니다.
  - Transact-SQL을 사용하려면 SQL Database 인스턴스를 서버 수준 주 로그인 또는 Azure Active Directory 관리자로 연결해야 합니다. 서버 수준 IP 방화벽 규칙은 Azure 수준 권한이 있는 사용자가 먼저 만들어야 합니다.)

### <a name="database-level-ip-firewall-rules"></a>데이터베이스 수준 IP 방화벽 규칙

  클라이언트는 이 규칙에 따라 동일한 SQL Database 서버 내의 특정(보안) 데이터베이스에 액세스할 수 있습니다. 마스터 데이터베이스를 포함하여 각 데이터베이스에 *master* 대한 규칙을 만들고 개별 데이터베이스에 저장됩니다.
  
  Transact-SQL 문을 사용 하 여 첫 번째 서버 수준 방화벽을 구성 한 후에만 마스터 및 사용자 데이터베이스에 대 한 데이터베이스 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.
  
  서버 수준 IP 방화벽 규칙의 범위를 벗어난 데이터베이스 수준 IP 방화벽 규칙에 IP 주소 범위를 지정하면 데이터베이스 수준 범위에 IP 주소가 있는 클라이언트만 데이터베이스에 액세스할 수 있습니다.
  
  데이터베이스에 대해 최대 128개의 데이터베이스 수준 IP 방화벽 규칙을 가질 수 있습니다. 데이터베이스 수준 IP 방화벽 규칙 구성에 대한 자세한 내용은 이 문서의 다음 예제를 참조하고 [sp_set_database_firewall_rule(Azure SQL Database)을](https://msdn.microsoft.com/library/dn270010.aspx)참조하십시오.

### <a name="recommendations-for-how-to-set-firewall-rules"></a>방화벽 규칙을 설정하는 방법에 대한 권장 사항

가능하면 데이터베이스 수준 IP 방화벽 규칙을 사용하는 것이 좋습니다. 이렇게 하면 보안이 향상되고 데이터베이스의 이식성이 향상됩니다. 관리자를 위해 서버 수준 IP 방화벽 규칙을 사용합니다. 또한 동일한 액세스 요구 사항이 있는 데이터베이스가 많고 각 데이터베이스를 개별적으로 구성하지 않으려는 경우에도 이러한 데이터베이스를 사용합니다.

> [!NOTE]
> 비즈니스 연속성의 맥락에서 휴대용 데이터베이스에 대한 자세한 내용은 [재해 복구를 위한 인증 요구 사항](sql-database-geo-replication-security-config.md)을 참조하세요.

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>서버 수준 및 데이터베이스 수준 IP 방화벽 규칙 비교

*한 데이터베이스의 사용자가 다른 데이터베이스에서 완전히 분리되어야 하나요?*

*그렇다면*데이터베이스 수준 IP 방화벽 규칙을 사용하여 액세스 권한을 부여합니다. 이 메서드는 모든 데이터베이스에 방화벽을 통해 액세스할 수 있는 서버 수준 IP 방화벽 규칙을 사용하지 않습니다. 그렇게 하면 방어 의 깊이가 줄어듭니다.

*IP 주소의 사용자가 모든 데이터베이스에 액세스해야 합니까?*

*그렇다면*서버 수준 IP 방화벽 규칙을 사용하여 IP 방화벽 규칙을 구성해야 하는 횟수를 줄입니다.

*IP 방화벽 규칙을 구성하는 사용자 또는 팀은 Azure 포털, PowerShell 또는 REST API를 통해서만 액세스할 수 있습니까?*

이 경우 서버 수준 IP 방화벽 규칙을 사용해야 합니다. 데이터베이스 수준 IP 방화벽 규칙은 Transact-SQL을 통해서만 구성할 수 있습니다.  

*IP 방화벽 규칙을 구성하는 사람이나 팀이 데이터베이스 수준에서 높은 수준의 권한을 갖는 것이 금지되어 있습니까?*

그렇다면 서버 수준 IP 방화벽 규칙을 사용합니다. Transact-SQL을 통해 데이터베이스 수준 IP 방화벽 규칙을 구성하려면 데이터베이스 수준에서 데이터베이스 권한 *제어가* 필요합니다.  

*IP 방화벽 규칙을 구성하거나 감사하는 사람이나 팀이 많은 데이터베이스(수백 개)에 대한 IP 방화벽 규칙을 중앙에서 관리합니까?*

이 시나리오에서 모범 사례는 사용자의 요구와 환경에 따라 결정됩니다. 서버 수준 IP 방화벽 규칙이 구성하기 더 쉬울 수 있지만 스크립팅은 데이터베이스 수준에서 규칙을 구성할 수 있습니다. 또한 서버 수준 IP 방화벽 규칙을 사용하는 경우에도 데이터베이스에서 *CONTROL* 권한이 있는 사용자가 데이터베이스 수준 IP 방화벽 규칙을 만드는지 확인하기 위해 데이터베이스 수준 IP 방화벽 규칙을 감사해야 할 수 있습니다.

*서버 수준 및 데이터베이스 수준 IP 방화벽 규칙을 혼합하여 사용할 수 있습니까?*

예. 관리자와 같은 일부 사용자는 서버 수준 IP 방화벽 규칙이 필요할 수 있습니다. 데이터베이스 애플리케이션 사용자와 같은 경우는 데이터베이스 수준 IP 방화벽 규칙이 필요할 수 있습니다.

### <a name="connections-from-the-internet"></a>인터넷에서 연결

컴퓨터가 인터넷에서 데이터베이스 서버에 연결하려고 하면 방화벽은 먼저 요청의 원래 IP 주소를 연결이 요청하는 데이터베이스 의 데이터베이스 수준 IP 방화벽 규칙에 대해 확인합니다.

- 주소가 데이터베이스 수준 IP 방화벽 규칙에 지정된 범위 내에 있으면 규칙을 포함하는 SQL 데이터베이스에 대한 연결이 부여됩니다.
- 주소가 데이터베이스 수준 IP 방화벽 규칙의 범위 내에 있지 않으면 방화벽은 서버 수준 IP 방화벽 규칙을 확인합니다. 주소가 서버 수준 IP 방화벽 규칙에 있는 범위 내에 있으면 연결이 부여됩니다. 서버 수준 IP 방화벽 규칙은 Azure SQL Server에 있는 모든 SQL Database에 적용됩니다.  
- 주소가 데이터베이스 수준 또는 서버 수준 IP 방화벽 규칙에 속한 범위 내에 있지 않으면 연결 요청이 실패합니다.

> [!NOTE]
> 로컬 컴퓨터에서 SQL Database에 액세스하려면 네트워크 및 로컬 컴퓨터의 방화벽이 TCP 포트 1433에서 나가는 통신을 허용해야 합니다.

### <a name="connections-from-inside-azure"></a>Azure 내부의 연결

Azure 내에서 호스팅되는 응용 프로그램이 SQL 서버에 연결되도록 하려면 Azure 연결을 사용하도록 설정해야 합니다. Azure의 응용 프로그램이 데이터베이스 서버에 연결하려고 하면 방화벽은 Azure 연결이 허용되는지 확인합니다. IP 주소를 시작하고 종료하는 방화벽 설정은 *0.0.0.0.0과* 같으며 Azure 연결이 허용되었음을 나타냅니다. 방화벽 규칙을 설정하고 **Azure 서비스 및 리소스를 전환하여** 방화벽 및 가상 네트워크 설정에서 이 서버에 **ON으로** 액세스하도록 전환하여 Azure Portal 블레이드에서 직접 설정할 수 **있습니다.** 연결이 허용되지 않으면 요청이 SQL Database 서버에 연결되지 않습니다.

> [!IMPORTANT]
> 이 옵션은 다른 고객의 구독에서 연결을 포함하여 Azure의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택하는 경우 로그인 및 사용자 권한이 권한 있는 사용자에게만 대한 액세스를 제한해야 합니다.

## <a name="create-and-manage-ip-firewall-rules"></a>IP 방화벽 규칙 생성 및 관리

[Azure Portal을](https://portal.azure.com/) 사용하거나 Azure [PowerShell,](https://docs.microsoft.com/powershell/module/az.sql) [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)또는 Azure [REST API를](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)사용하여 프로그래밍 방식으로 첫 번째 서버 수준 방화벽 설정을 만듭니다. 이러한 메서드 또는 Transact-SQL을 사용하여 추가 서버 수준 IP 방화벽 규칙을 만들고 관리할 수 있습니다.

> [!IMPORTANT]
> 데이터베이스 수준 IP 방화벽 규칙은 Transact-SQL을 사용하여만 만들고 관리할 수 있습니다.

성능 향상을 위해 서버 수준 IP 방화벽 규칙이 데이터베이스 수준에서 일시적으로 캐시됩니다. 캐시를 새로 고치려면 [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)를 참조하세요.

> [!TIP]
> [SQL Database 감사](sql-database-auditing.md)를 사용하여 서버 수준 및 데이터베이스 수준의 방화벽 변경 내용을 감사할 수 있습니다.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure 포털을 사용하여 서버 수준 IP 방화벽 규칙 관리

Azure 포털에서 서버 수준 IP 방화벽 규칙을 설정하려면 Azure SQL 데이터베이스 또는 SQL Database 서버의 개요 페이지로 이동합니다.

> [!TIP]
> 자습서는 [Azure Portal을 사용하여 DB 만들기](sql-database-single-database-get-started.md)를 참조하세요.

#### <a name="from-the-database-overview-page"></a>데이터베이스 개요 페이지에서

1. 데이터베이스 개요 페이지에서 서버 수준 IP 방화벽 규칙을 설정하려면 다음 이미지와 같이 도구 모음에서 **서버 방화벽 설정을** 선택합니다. 

    ![서버 IP 방화벽 규칙](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    SQL Database 서버에 대한 **방화벽 설정** 페이지가 열립니다.

2. 도구 모음에서 **클라이언트 IP 추가를** 선택하여 사용 중이던 컴퓨터의 IP 주소를 추가한 다음 **저장을**선택합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

    ![서버 수준 IP 방화벽 규칙 설정](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>서버 개요 페이지에서

서버의 개요 페이지가 열립니다. 정규화된 서버 이름(예: *mynewserver20170403.database.windows.net)을*표시하고 추가 구성에 대한 옵션을 제공합니다.

1. 이 페이지에서 서버 수준 규칙을 설정하려면 왼쪽의 **설정** 메뉴에서 **방화벽을** 선택합니다.

2. 도구 모음에서 **클라이언트 IP 추가를** 선택하여 사용 중이던 컴퓨터의 IP 주소를 추가한 다음 **저장을**선택합니다. 현재 IP 주소에 대한 서버 수준 IP 방화벽 규칙이 생성됩니다.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>거래-SQL을 사용하여 IP 방화벽 규칙 관리

| 카탈로그 보기 또는 저장 프로시저 | Level | 설명 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |데이터베이스 |현재 데이터베이스 수준 IP 방화벽 규칙을 표시합니다. |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |데이터베이스 |데이터베이스 수준 IP 방화벽 규칙을 제거합니다. |

다음 예제에서는 기존 규칙을 *검토하고, Contoso*서버의 IP 주소 범위를 활성화하고, IP 방화벽 규칙을 삭제합니다.

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

다음으로 서버 수준 IP 방화벽 규칙을 추가합니다.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

서버 수준 IP 방화벽 규칙을 삭제하려면 *sp_delete_firewall_rule* 저장 프로시저를 실행합니다. 다음 예제는 *ContosoFirewallRule*규칙을 삭제합니다.

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell을 사용하여 서버 수준 IP 방화벽 규칙 관리 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 모든 개발은 이제 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다.

| Cmdlet | Level | 설명 |
| --- | --- | --- |
| [겟-아즈Sql서버방화벽규칙](/powershell/module/az.sql/get-azsqlserverfirewallrule) |서버 |현재 서버 수준 방화벽 규칙 반환 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |서버 |새 서버 수준 방화벽 규칙 만들기 |
| [세트-아즈Sql서버방화벽규칙](/powershell/module/az.sql/set-azsqlserverfirewallrule) |서버 |기존 서버 수준 방화벽 규칙 속성 업데이트 |
| [제거-아즈Sql서버방화벽규칙](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |서버 |서버 수준 방화벽 규칙 제거 |

다음 예제에서는 PowerShell을 사용하여 서버 수준 IP 방화벽 규칙을 설정합니다.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> $servername 서버 이름을 지정하고 정규화된 DNS 이름이 아닌 경우(예: **mysqldbserver.database.windows.net** 대신 **mysqldbserver 지정)**

> [!TIP]
> 빠른 시작의 컨텍스트에서 PowerShell 예제의 경우 [DB 만들기 - PowerShell](sql-database-powershell-samples.md) 및 [단일 데이터베이스 만들기를 참조하고 PowerShell을 사용하여 SQL Database 서버 수준 IP 방화벽 규칙을 구성합니다.](scripts/sql-database-create-and-configure-database-powershell.md)

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI를 사용하여 서버 수준 IP 방화벽 규칙 관리

| Cmdlet | Level | 설명 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|서버|서버 IP 방화벽 규칙을 만듭니다.|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|서버|서버의 IP 방화벽 규칙을 나열합니다.|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|서버|IP 방화벽 규칙의 세부 정보 표시|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|서버|IP 방화벽 규칙 업데이트|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|서버|IP 방화벽 규칙 삭제|

다음 예제에서는 CLI를 사용하여 서버 수준 IP 방화벽 규칙을 설정합니다.

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> $servername 서버 이름을 지정하고 정규화된 DNS 이름이 아닌 경우(예: **mysqldbserver.database.windows.net** 대신 **mysqldbserver 지정)**

> [!TIP]
> 빠른 시작의 컨텍스트에서 CLI 예제의 경우 [DB 만들기 - Azure CLI](sql-database-cli-samples.md) 및 단일 데이터베이스 [만들기를 참조하고 Azure CLI를 사용하여 SQL Database IP 방화벽 규칙을 구성합니다.](scripts/sql-database-create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API를 사용하여 서버 수준 IP 방화벽 규칙 관리

| API | Level | 설명 |
| --- | --- | --- |
| [방화벽 규칙 목록](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |서버 |현재 서버 수준 IP 방화벽 규칙을 표시합니다. |
| [방화벽 규칙 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |서버 |서버 수준 IP 방화벽 규칙을 생성 및 업데이트합니다. |
| [방화벽 규칙 삭제](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |서버 |서버 수준 IP 방화벽 규칙을 제거합니다. |
| [방화벽 규칙 받기](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | 서버 | 서버 수준 IP 방화벽 규칙을 가져옵니다. |

## <a name="troubleshoot-the-database-firewall"></a>데이터베이스 방화벽 문제 해결

SQL Database 서비스에 대한 액세스가 예상대로 작동하지 않는 경우 다음 사항을 고려하십시오.

- **로컬 방화벽 구성:**

  컴퓨터가 SQL Database에 액세스하려면 TCP 포트 1433에 대한 방화벽 예외를 컴퓨터에 만들어야 할 수 있습니다. Azure 클라우드 경계 내에서 연결을 만들려면 추가 포트를 열어야 할 수 있습니다. 자세한 내용은 [ADO.NET 4.5 및 SQL Database에 대한 포트의](sql-database-develop-direct-route-ports-adonet-v12.md)"SQL 데이터베이스: 외부 대 내부" 섹션을 참조하십시오.

- **네트워크 주소 번역:**

  NAT(네트워크 주소 변환)으로 인해 컴퓨터에서 SQL 데이터베이스에 연결하는 데 사용하는 IP 주소가 컴퓨터의 IP 구성 설정의 IP 주소와 다를 수 있습니다. 컴퓨터가 Azure에 연결하는 데 사용하는 IP 주소를 보려면 다음을 수행하십시오.
    1. 포털에 로그인합니다.
    1. 데이터베이스를 호스팅하는 서버의 **구성** 탭으로 이동합니다.
    1. **현재 클라이언트 IP 주소는** **허용된 IP 주소** 섹션에 표시됩니다. 이 컴퓨터가 서버에 액세스할 수 있도록 **허용된 IP 주소에** 대한 **추가를** 선택합니다.

- **허용 목록에 대한 변경 사항은 아직 적용되지 않았습니다.**

  SQL Database 방화벽 구성변경이 적용되려면 최대 5분 정도 지연될 수 있습니다.

- **로그인이 승인되지 않았거나 잘못된 암호가 사용되었습니다.**

  로그인에 SQL Database 서버에 대한 사용 권한이 없거나 암호가 올바르지 않으면 서버에 대한 연결이 거부됩니다. 방화벽 설정을 만들면 클라이언트가 서버에 연결할 수 있는 *기회만* 있습니다. 클라이언트는 여전히 필요한 보안 자격 증명을 제공해야 합니다. 로그인 준비에 대한 자세한 내용은 [SQL Database 및 SQL 데이터 웨어하우스에 대한 데이터베이스 액세스 제어 및 권한 부여를](sql-database-manage-logins.md)참조하십시오.

- **동적 IP 주소:**

  동적 IP 주소 지정을 사용하는 인터넷 연결이 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 해결 방법 중 하나를 시도해 보십시오.
  
  - SQL Database 서버에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위에 대해 인터넷 서비스 공급자에게 문의합니다. IP 주소 범위를 IP 방화벽 규칙으로 추가합니다.
  - 대신 클라이언트 컴퓨터에 대한 정적 IP 주소를 가져옵니다. IP 주소를 IP 방화벽 규칙으로 추가합니다.

## <a name="next-steps"></a>다음 단계

- 회사 네트워크 환경에서 Azure 데이터 센터에서 사용되는 계산 IP 주소 범위(SQL 범위 포함)에서 인바운드 통신을 허용하는지 확인합니다. 허용 목록에 해당 IP 주소를 추가해야 할 수 있습니다. [마이크로소프트 Azure 데이터 센터 IP 범위를](https://www.microsoft.com/download/details.aspx?id=41653)참조하십시오.  
- 서버 수준 IP 방화벽 규칙을 만드는 방법에 대한 빠른 시작은 [Azure SQL 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.
- 오픈 소스 또는 타사 응용 프로그램에서 Azure SQL 데이터베이스에 연결하는 데 도움이 되는 경우 [클라이언트 빠른 시작 코드 샘플을](https://msdn.microsoft.com/library/azure/ee336282.aspx)SQL Database 에 참조하세요.
- 열어야 할 수 있는 추가 포트에 대한 자세한 내용은 [ADO.NET 4.5 및 SQL Database의 경우 1433을 초과하는 포트의](sql-database-develop-direct-route-ports-adonet-v12.md) "SQL Database: 외부 및 내부" 섹션을 참조하십시오.
- Azure SQL Database 보안에 대한 개요는 [데이터베이스 보안을](sql-database-security-overview.md)참조하십시오.

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
