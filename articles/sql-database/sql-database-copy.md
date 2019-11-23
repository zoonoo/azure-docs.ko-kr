---
title: 데이터베이스 복사
description: 기존 Azure SQL 데이터베이스에 대한 트랜잭션 일치 복사본을 같거나 다른 서버에 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: b3bc99d0fbdb551af0fb3711d74db537d3f9b1a5
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421335"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 트랜잭션 일치 복사본 복사

Azure SQL Database provides several methods for creating a transactionally consistent copy of an existing Azure SQL database ([single database](sql-database-single-database.md)) on either the same server or a different server. Azure Portal, PowerShell 또는 T-SQL을 사용하여 SQL Database를 복사할 수 있습니다.

## <a name="overview"></a>개요

데이터베이스 복사본은 복사 요청 당시의 원본 데이터베이스의 스냅샷입니다. You can select the same server or a different server. Also you can choose to keep its service tier and compute size, or use a different compute size within the same service tier (edition). 복사가 완료되면 완전히 작동하는 독립 데이터베이스가 됩니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다. The copy is created using the geo-replication technology and once seeding is completed the geo-replication link is automatically terminated. All the requirements for using geo-replication apply to the database copy operation. See [Active geo-replication overview](sql-database-active-geo-replication.md) for details.

> [!NOTE]
> 데이터베이스 복사본을 만들 때 [자동화된 데이터베이스 백업](sql-database-automated-backups.md)이 사용됩니다.

## <a name="logins-in-the-database-copy"></a>데이터베이스 복사본에서 로그인

데이터베이스를 동일한 SQL Database 서버에 복사할 때는 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하는 보안 주체는 새 데이터베이스의 데이터베이스 소유자가 됩니다. 모든 데이터베이스 사용자, 권한 및 보안 식별자(SID)가 데이터베이스 사본에 복사됩니다.  

다른 SQL Database 서버로 데이터베이스를 복사하면 새 서버의 보안 주체가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 데이터 액세스에 [포함된 데이터베이스 사용자](sql-database-manage-logins.md)를 사용하는 경우 복사가 완료된 후 동일한 자격 증명으로 액세스할 수 있도록 주 데이터베이스와 보조 데이터베이스에서 항상 동일한 사용자 자격 증명을 사용해야 합니다.

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)를 사용하는 경우 복사본에서 자격 증명을 관리할 필요가 없습니다. 그러나 데이터베이스를 새 서버로 복사할 때 새 서버에 로그인이 존재하지 않으므로 로그인 기반 액세스가 작동하지 않을 수 있습니다. 다른 SQL Database 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대해 자세히 알아보려면 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.

복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자인 복사를 시작한 로그인만 새 데이터베이스에 로그인할 수 있습니다. 복사 작업이 완료된 후 로그인을 확인하려면 [로그인 확인](#resolve-logins)을 참조하세요.

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure Portal을 사용하여 데이터베이스 복사

Azure Portal을 사용하여 데이터베이스를 복사하려면 데이터베이스에 대한 페이지를 열고 **복사**를 클릭합니다.

   ![데이터베이스 복사](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>PowerShell을 사용하여 데이터베이스 복사

To copy a database, use the following examples.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

For PowerShell, use the [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet.

> [!IMPORTANT]
> The PowerShell Azure Resource Manager (RM) module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. The AzureRM module will continue to receive bug fixes until at least December 2020.  The arguments for the commands in the Az module and in the AzureRm modules are substantially identical. For more about their compatibility, see [Introducing the new Azure PowerShell Az module](/powershell/azure/new-azureps-module-az).

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

The database copy is a asynchronous operation but the target database is created immediately after the request is accepted. If you need to cancel the copy operation while still in progress, drop the the target database using the [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) command.

* * *

전체 샘플 스크립트는 [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-powershell.md)를 참조하세요.

## <a name="rbac-roles-to-manage-database-copy"></a>RBAC roles to manage database copy

To create a database copy, you will need to be in the following roles

- 구독 소유자 또는
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To cancel a database copy, you will need to be in the following roles

- 구독 소유자 또는
- SQL Server Contributor role or
- Custom role on the source and target databases with following permission:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

To manage database copy using Azure portal, you will also need the following permissions:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

If you want to see the operations under deployments in the resource group on the portal, operations across multiple resource providers including SQL operations, you will need these additional RBAC roles:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL을 사용하여 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 서버 수준 보안 주체가 아닌 로그인이 dbmanager 역할의 구성원이어야 합니다. 서버에 로그인 및 연결하는 방법에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) 문으로 원본 데이터베이스 복사를 시작합니다. 이 문을 실행하면 데이터베이스 복사 프로세스가 시작됩니다. 데이터베이스 복사는 비동기 프로세스이므로 CREATE DATABASE 문은 데이터베이스가 복사가 완료되기 전에 반환됩니다.

### <a name="copy-a-sql-database-to-the-same-server"></a>동일한 서버에 SQL 데이터베이스 복사

서버 수준 보안 주체 로그인이나 복사하려는 데이터베이스에서 만든 로그인을 사용하여 master 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 서버 수준 보안 주체가 아닌 로그인이 dbmanager 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL 데이터베이스를 다른 서버에 복사

새 데이터베이스를 만들 SQL Database 서버인 대상 서버의 master 데이터베이스에 로그인합니다. 원본 SQL Database 서버에서 원본 데이터베이스의 데이터베이스 소유자와 이름 및 암호가 같은 로그인을 사용합니다. 대상 서버의 로그인도 dbmanager 역할의 구성원이거나 서버 수준 보안 주체 로그인이어야 합니다.

이 명령은 server1의 Database1을 server2의 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> Both servers' firewalls must be configured to allow inbound connection from the IP of the client issuing the T-SQL COPY command.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Copy a SQL database to a different subscription

You can use the steps described in the previous section to copy your database to a SQL Database server in a different subscription. Make sure you use a login that has the same name and password as the database owner of the source database and it is a member of the dbmanager role or is the server-level principal login. 

> [!NOTE]
> The [Azure portal](https://portal.azure.com) does not support copy to a different subscription because Portal calls the ARM API and it uses the subscription certificates to access both servers involved in geo-replication.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>복사 작업 진행률 모니터링

sys.databases 및 sys.dm_database_copies 뷰 쿼리를 통해 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **COPYING**으로 설정됩니다.

* 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **SUSPECT**로 설정됩니다. 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
* 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **ONLINE**으로 설정됩니다. 복사가 완료되었으며 새 데이터베이스가 원본 데이터베이스와는 독립적으로 변경 가능한 일반 데이터베이스가 됩니다.

> [!NOTE]
> 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 문을 실행합니다. 또는 원본 데이터베이스에서 DROP DATABASE 문을 실행해도 복사 프로세스가 취소됩니다.

> [!IMPORTANT]
> If you need to create a copy with a substantially smaller SLO than the source, the target database may not have sufficient resources to complete the seeding process and it can cause the copy operaion to fail. In this scenario use a geo-restore request to create a copy in a different server and/or a different region. See [Recover an Azure SQL database using database backups](sql-database-recovery-using-backups.md#geo-restore) for more informaion.

## <a name="resolve-logins"></a>로그인 확인

대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://msdn.microsoft.com/library/ms175475.aspx)을 참조하세요. [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 되며 새 보안 식별자(SID)를 할당 받습니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자인 복사를 시작한 로그인만 새 데이터베이스에 로그인할 수 있습니다.

다른 SQL Database 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법을 알아보려면 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.

## <a name="database-copy-errors"></a>데이터베이스 복사 오류

Azure SQL Database에서 데이터베이스를 복사하는 동안 다음 오류가 발생할 수 있습니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md)를 참조하세요.

| 오류 코드 | 심각도 | 설명 |
| ---:| ---:|:--- |
| 40635 |16 |IP 주소 '%.&#x2a;ls'을(를) 사용하는 클라이언트가 일시적으로 비활성화되었습니다. |
| 40637 |16 |데이터베이스 복사본 만들기를 현재 사용할 수 없습니다. |
| 40561 |16 |데이터베이스를 복사하지 못했습니다. 원본 또는 대상 데이터베이스가 존재하지 않습니다. |
| 40562 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스가 삭제되었습니다. |
| 40563 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스가 삭제되었습니다. |
| 40564 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40565 |16 |데이터베이스를 복사하지 못했습니다. 동일한 소스에서 1개의 동시 데이터베이스 복사본이 허용됩니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40566 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40567 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40568 |16 |데이터베이스를 복사하지 못했습니다. 원본 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40569 |16 |데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 사용할 수 없습니다. 대상 데이터베이스를 삭제하고 다시 시도하십시오. |
| 40570 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |
| 40571 |16 |내부 오류로 인해 데이터베이스를 복사하지 못했습니다. 대상 데이터베이스를 삭제하고 나중에 다시 시도하십시오. |

## <a name="next-steps"></a>다음 단계

- 로그인에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md) 및 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.
- 데이터베이스를 내보내려면 [데이터베이스를 BACPAC로 내보내기](sql-database-export.md)를 참조하세요.
