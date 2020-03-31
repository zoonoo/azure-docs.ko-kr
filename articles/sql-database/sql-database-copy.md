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
ms.date: 02/24/2020
ms.openlocfilehash: 7e4744627cfd08874e07bb126df048ea3e644f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473747"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 트랜잭션 일치 복사본 복사

Azure SQL Database는 동일한 서버 또는 다른 서버에서 기존 Azure SQL[데이터베이스(단일 데이터베이스)의](sql-database-single-database.md)트랜잭션 일관된 복사본을 만드는 몇 가지 방법을 제공합니다. Azure Portal, PowerShell 또는 T-SQL을 사용하여 SQL Database를 복사할 수 있습니다.

## <a name="overview"></a>개요

데이터베이스 복사본은 복사 요청 당시의 원본 데이터베이스의 스냅샷입니다. 동일한 서버 또는 다른 서버를 선택할 수 있습니다. 또한 서비스 계층 및 계산 크기를 유지하거나 동일한 서비스 계층(에디션) 내에서 다른 계산 크기를 사용하도록 선택할 수 있습니다. 복사가 완료되면 완전히 작동하는 독립 데이터베이스가 됩니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다. 복사본은 지역 복제 기술을 사용하여 만들어지며 시드가 완료되면 지역 복제 링크가 자동으로 종료됩니다. 지역 복제 사용에 대한 모든 요구 사항은 데이터베이스 복사 작업에 적용됩니다. 자세한 내용은 [활성 지역 복제 개요를](sql-database-active-geo-replication.md) 참조하십시오.

> [!NOTE]
> 데이터베이스 복사본을 만들 때 [자동화된 데이터베이스 백업](sql-database-automated-backups.md)이 사용됩니다.

## <a name="logins-in-the-database-copy"></a>데이터베이스 복사본에서 로그인

데이터베이스를 동일한 SQL Database 서버에 복사할 때는 두 데이터베이스에서 동일한 로그인을 사용할 수 있습니다. 데이터베이스를 복사하는 데 사용하는 보안 주체는 새 데이터베이스의 데이터베이스 소유자가 됩니다. 

데이터베이스를 다른 SQL Database 서버에 복사하면 대상 서버에서 복사 작업을 시작한 보안 주체가 새 데이터베이스의 소유자가 됩니다. 

대상 서버에 관계없이 모든 데이터베이스 사용자, 해당 사용 권한 및 해당 보안 식별자(SID)가 데이터베이스 복사본에 복사됩니다. 포함된 [데이터베이스 사용자를](sql-database-manage-logins.md) 데이터 액세스에 사용하면 복사된 데이터베이스에 동일한 사용자 자격 증명이 있으므로 복사본이 완료된 후 동일한 자격 증명으로 즉시 액세스할 수 있습니다.

데이터 액세스를 위해 서버 수준 로그인을 사용하고 데이터베이스를 다른 서버에 복사하는 경우 로그인 기반 액세스가 작동하지 않을 수 있습니다. 이는 로그인이 대상 서버에 없거나 암호 및 보안 식별자(ID)가 다르기 때문에 발생할 수 있습니다. 다른 SQL Database 서버로 데이터베이스를 복사할 때 로그인을 관리하는 방법에 대해 자세히 알아보려면 [재해 복구 후에 Azure SQL Database 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요. 다른 서버에 대한 복사 작업이 성공한 후 다른 사용자가 다시 매핑되기 전에 데이터베이스 소유자 또는 서버 관리자와 연결된 로그인만 복사된 데이터베이스에 로그인할 수 있습니다. 복사 작업이 완료된 후 로그인을 해결하고 데이터 액세스를 설정하려면 [로그인 확인](#resolve-logins)을 참조하십시오.

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure Portal을 사용하여 데이터베이스 복사

Azure Portal을 사용하여 데이터베이스를 복사하려면 데이터베이스에 대한 페이지를 열고 **복사**를 클릭합니다.

   ![데이터베이스 복사](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell-or-azure-cli"></a>PowerShell 또는 Azure CLI를 사용하여 데이터베이스 복사

데이터베이스를 복사하려면 다음 예제를 사용합니다.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

PowerShell의 경우 [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet을 사용합니다.

> [!IMPORTANT]
> PowerShell Azure 리소스 관리자(RM) 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. AzureRM 모듈은 적어도 2020년 12월까지 버그 수정을 계속 받을 것입니다.  Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 거의 동일합니다. 호환성에 대한 자세한 내용은 [새 Azure PowerShell Az 모듈 소개를](/powershell/azure/new-azureps-module-az)참조하십시오.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

데이터베이스 복사본은 비동기 작업이지만 요청이 수락된 직후 대상 데이터베이스가 만들어집니다. 아직 진행 중인 동안 복사 작업을 취소해야 하는 경우 [제거-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet을 사용하여 대상 데이터베이스를 삭제합니다.

전체 샘플 PowerShell 스크립트는 [데이터베이스를 새 서버로 복사를](scripts/sql-database-copy-database-to-new-server-powershell.md)참조하십시오.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

데이터베이스 복사본은 비동기 작업이지만 요청이 수락된 직후 대상 데이터베이스가 만들어집니다. 아직 진행 중인 동안 복사 작업을 취소해야 하는 경우 [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) 명령을 사용하여 대상 데이터베이스를 삭제합니다.

* * *

## <a name="rbac-roles-to-manage-database-copy"></a>데이터베이스 복사본을 관리하는 RBAC 역할

데이터베이스 복사본을 만들려면 다음 역할에 있어야 합니다.

- 구독 소유자 또는
- SQL 서버 기여자 역할 또는
- 다음과 같은 권한을 가진 원본 및 대상 데이터베이스에 대한 사용자 지정 역할:

   Microsoft.Sql/서버/데이터베이스/읽기 Microsoft.Sql/서버/데이터베이스/쓰기

데이터베이스 복사본을 취소하려면 다음 역할에 있어야 합니다.

- 구독 소유자 또는
- SQL 서버 기여자 역할 또는
- 다음과 같은 권한을 가진 원본 및 대상 데이터베이스에 대한 사용자 지정 역할:

   Microsoft.Sql/서버/데이터베이스/읽기 Microsoft.Sql/서버/데이터베이스/쓰기

Azure 포털을 사용하여 데이터베이스 복사본을 관리하려면 다음 권한도 필요합니다.

   Microsoft.Resources/구독/리소스/읽기 Microsoft.Resources/구독/리소스/쓰기 Microsoft.Resources/배포/읽기 Microsoft.Resources/배포/쓰기 Microsoft.Resources/배포/쓰기 Microsoft.Resources/배포/작업 상태/읽기

포털의 리소스 그룹에서 배포 중인 작업을 SQL 작업을 비롯한 여러 리소스 공급자의 작업을 보려면 다음과 같은 추가 RBAC 역할이 필요합니다.

   Microsoft.Resources/구독/리소스 그룹/배포/작업/읽기 Microsoft.Resources/구독/리소스 그룹/배포/작업 상태/읽기

## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL을 사용하여 데이터베이스 복사

서버 관리자 로그인 또는 복사할 데이터베이스를 만든 로그인을 사용하여 마스터 데이터베이스에 로그인합니다. 데이터베이스 복사본이 성공하려면 서버 관리자가 아닌 로그인이 `dbmanager` 역할의 구성원이어야 합니다. 서버에 로그인 및 연결하는 방법에 대한 자세한 내용은 [로그인 관리](sql-database-manage-logins.md)를 참조하세요.

CREATE 데이터베이스로 원본 데이터베이스 복사 시작 [... 명령문의 사본으로.](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current#copy-a-database) 데이터베이스 복사 작업이 완료될 때까지 T-SQL 문이 계속 실행됩니다.

> [!NOTE]
> T-SQL 문을 종료해도 데이터베이스 복사 작업이 종료되지 않습니다. 작업을 종료하려면 대상 데이터베이스를 삭제합니다.
>

### <a name="copy-a-sql-database-to-the-same-server"></a>동일한 서버에 SQL 데이터베이스 복사

서버 관리자 로그인 또는 복사할 데이터베이스를 만든 로그인을 사용하여 마스터 데이터베이스에 로그인합니다. 데이터베이스 복사가 성공하려면 서버 관리자가 아닌 로그인이 `dbmanager` 역할의 구성원이어야 합니다.

이 명령은 Database1을 동일한 서버에서 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>SQL 데이터베이스를 다른 서버에 복사

새 데이터베이스를 만들 대상 서버의 마스터 데이터베이스에 로그인합니다. 원본 서버의 원본 데이터베이스의 데이터베이스 소유자와 이름이 동일한 로그인을 사용합니다. 대상 서버의 로그인은 `dbmanager` 역할의 구성원이거나 서버 관리자 로그인이어야 합니다.

이 명령은 server1의 Database1을 server2의 이름이 Database2인 새 데이터베이스에 복사합니다. 데이터베이스 크기에 따라 복사 작업을 완료하는 데 다소 시간이 걸릴 수 있습니다.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> 두 서버의 방화벽은 T-SQL CREATE 데이터베이스를 발급하는 클라이언트의 IP에서 인바운드 연결을 허용하도록 구성해야 합니다... 명령의 복사본으로.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>SQL 데이터베이스를 다른 구독으로 복사

[SQL 데이터베이스 복사](#copy-a-sql-database-to-a-different-server) 섹션에서 다른 서버 섹션의 단계를 사용하여 T-SQL을 사용하여 다른 구독의 SQL Database 서버에 데이터베이스를 복사할 수 있습니다. 원본 데이터베이스의 데이터베이스 소유자와 이름이 같은 로그인을 사용해야 합니다. 또한 로그인은 원본 서버와 대상 `dbmanager` 서버 모두에서 역할의 구성원 또는 서버 관리자여야 합니다.

> [!NOTE]
> [Azure 포털,](https://portal.azure.com)PowerShell 및 Azure CLI는 다른 구독에 대한 데이터베이스 복사본을 지원하지 않습니다.

### <a name="monitor-the-progress-of-the-copying-operation"></a>복사 작업 진행률 모니터링

[sys.databases,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)및 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 보기를 쿼리하여 복사 프로세스를 모니터링합니다. 복사가 진행되는 동안 새 데이터베이스에 대한 sys.databases 보기의 **state_desc** 열이 **COPYING으로**설정됩니다.

* 복사에 실패하면 새 데이터베이스에 대한 sys.databases 뷰의 **state_desc** 열이 **SUSPECT로**설정됩니다. 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
* 복사가 성공하면 새 데이터베이스에 대한 sys.databases 보기의 **state_desc** 열이 **ONLINE로**설정됩니다. 복사가 완료되었으며 새 데이터베이스가 원본 데이터베이스와는 독립적으로 변경 가능한 일반 데이터베이스가 됩니다.

> [!NOTE]
> 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://docs.microsoft.com/sql/t-sql/statements/drop-database-transact-sql) 문을 실행합니다.

> [!IMPORTANT]
> 원본보다 훨씬 작은 서비스 목표를 가진 복사본을 만들어야 하는 경우 대상 데이터베이스에 시드 프로세스를 완료하기에 충분한 리소스가 없을 수 있으며 복사 작동이 실패할 수 있습니다. 이 시나리오에서는 지리적 복원 요청을 사용하여 다른 서버 및/또는 다른 지역에서 복사본을 만듭니다. 자세한 내용은 [데이터베이스 백업을 사용하여 Azure SQL 데이터베이스 복구를](sql-database-recovery-using-backups.md#geo-restore) 참조하십시오.

## <a name="resolve-logins"></a>로그인 확인

새 데이터베이스가 대상 서버에서 온라인 상태가 되면 [ALTER USER](https://docs.microsoft.com/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current) 문을 사용하여 새 데이터베이스에서 대상 서버에 로그인하도록 사용자를 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://docs.microsoft.com/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server)을 참조하세요. [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사본을 시작한 사용자가 새 데이터베이스의 데이터베이스 소유자가 됩니다. 복사가 성공하고 다른 사용자가 다시 매핑되기 전에 데이터베이스 소유자만 새 데이터베이스에 로그인할 수 있습니다.

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
- 데이터베이스를 내보내려면 [BACPAC로 데이터베이스 내보내기를](sql-database-export.md)참조하십시오.
