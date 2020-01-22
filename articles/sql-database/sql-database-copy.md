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
ms.openlocfilehash: e1df345fb9a89972ad1857a937c22d6e10ad1fba
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289410"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Azure SQL 데이터베이스에 대한 트랜잭션 일치 복사본 복사

Azure SQL Database는 동일한 서버나 다른 서버에 기존 Azure SQL database ([단일 데이터베이스](sql-database-single-database.md))의 트랜잭션 측면에서 일관 된 복사본을 만드는 여러 가지 방법을 제공 합니다. Azure Portal, PowerShell 또는 T-SQL을 사용하여 SQL Database를 복사할 수 있습니다.

## <a name="overview"></a>개요

데이터베이스 복사본은 복사 요청 당시의 원본 데이터베이스의 스냅샷입니다. 동일한 서버 또는 다른 서버를 선택할 수 있습니다. 또한 서비스 계층과 계산 크기를 유지 하거나 동일한 서비스 계층 (버전) 내에서 다른 계산 크기를 사용 하도록 선택할 수 있습니다. 복사가 완료되면 완전히 작동하는 독립 데이터베이스가 됩니다. 이 시점에서 모든 버전으로 업그레이드하거나 다운그레이드할 수 있습니다. 로그인, 사용자 및 사용 권한은 독립적으로 관리됩니다. 복사본은 지역에서 복제 기술을 사용 하 여 만들어지며 시드가 완료 되 면 지역에서 복제 링크가 자동으로 종료 됩니다. 지역에서 복제를 사용 하기 위한 모든 요구 사항은 데이터베이스 복사 작업에 적용 됩니다. 자세한 내용은 [활성 지역 복제 개요](sql-database-active-geo-replication.md) 를 참조 하세요.

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

데이터베이스를 복사 하려면 다음 예제를 사용 합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell의 경우 [AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) cmdlet을 사용 합니다.

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

데이터베이스 복사는 비동기 작업 이지만 요청이 수락 된 후 즉시 대상 데이터베이스가 생성 됩니다. 진행 중인 동안 복사 작업을 취소 해야 하는 경우 [AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet을 사용 하 여 대상 데이터베이스를 삭제 합니다.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

데이터베이스 복사는 비동기 작업 이지만 요청이 수락 된 후 즉시 대상 데이터베이스가 생성 됩니다. 진행 중인 동안 복사 작업을 취소 해야 하는 경우 [az sql db delete](/cli/azure/sql/db#az-sql-db-delete) 명령을 사용 하 여 대상 데이터베이스를 삭제 합니다.

* * *

전체 샘플 스크립트는 [새 서버에 데이터베이스 복사](scripts/sql-database-copy-database-to-new-server-powershell.md)를 참조하세요.

## <a name="rbac-roles-to-manage-database-copy"></a>데이터베이스 복사본을 관리 하는 RBAC 역할

데이터베이스 복사본을 만들려면 다음 역할을 수행 해야 합니다.

- 구독 소유자 또는
- SQL Server 참가자 역할 또는
- 다음 권한이 있는 원본 및 대상 데이터베이스에 대 한 사용자 지정 역할:

   Microsoft .Sql/servers/databases/Microsoft .Sql/servers/databases/write 읽기

데이터베이스 복사본을 취소 하려면 다음 역할을 수행 해야 합니다.

- 구독 소유자 또는
- SQL Server 참가자 역할 또는
- 다음 권한이 있는 원본 및 대상 데이터베이스에 대 한 사용자 지정 역할:

   Microsoft .Sql/servers/databases/Microsoft .Sql/servers/databases/write 읽기

Azure Portal를 사용 하 여 데이터베이스 복사를 관리 하려면 다음 사용 권한도 필요 합니다.

   Microsoft .Resources/subscription/resources/microsoft .resources/subscription/resources를 읽고, microsoft .resources/배포를 읽고, Microsoft .resources/배포를 읽고

포털에서 리소스 그룹의 배포 아래에 있는 작업, SQL 작업을 비롯 한 여러 리소스 공급자의 작업을 확인 하려면 다음과 같은 추가 RBAC 역할이 필요 합니다.

   Microsoft .Resources/subscription/resourcegroups/배포/작업/읽기 Microsoft .Resources/subscription/resourcegroups/배포/operationstatuses/읽기

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
> T-sql COPY 명령을 실행 하는 클라이언트의 IP에서 인바운드 연결을 허용 하도록 두 서버의 방화벽을 모두 구성 해야 합니다.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>다른 구독에 SQL 데이터베이스 복사

이전 섹션에 설명 된 단계를 사용 하 여 데이터베이스를 다른 구독의 SQL Database 서버로 복사할 수 있습니다. 원본 데이터베이스의 데이터베이스 소유자와 동일한 이름 및 암호를 사용 하는 로그인을 사용 하 고 dbmanager 역할의 구성원 이거나 서버 수준 보안 주체 로그인을 사용 해야 합니다. 

> [!NOTE]
> 포털에서 ARM API를 호출 하 고 구독 인증서를 사용 하 여 지역에서 복제에 포함 된 두 서버에 액세스 하기 때문에 [Azure Portal](https://portal.azure.com) 은 다른 구독으로의 복사를 지원 하지 않습니다.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>복사 작업 진행률 모니터링

sys.databases 및 sys.dm_database_copies 뷰 쿼리를 통해 복사 프로세스를 모니터링합니다. 복사가 진행 중인 동안 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **COPYING**으로 설정됩니다.

* 복사가 실패하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **SUSPECT**로 설정됩니다. 새 데이터베이스에서 DROP 문을 실행하고 나중에 다시 시도합니다.
* 복사에 성공하면 새 데이터베이스의 sys.databases 뷰에 대한 **state_desc** 열은 **ONLINE**으로 설정됩니다. 복사가 완료되었으며 새 데이터베이스가 원본 데이터베이스와는 독립적으로 변경 가능한 일반 데이터베이스가 됩니다.

> [!NOTE]
> 진행 중인 복사를 취소하려면 새 데이터베이스에서 [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) 문을 실행합니다. 또는 원본 데이터베이스에서 DROP DATABASE 문을 실행해도 복사 프로세스가 취소됩니다.

> [!IMPORTANT]
> 원본 보다 훨씬 작은 SLO로 복사본을 만들어야 하는 경우 대상 데이터베이스에 시드 프로세스를 완료 하는 데 충분 한 리소스가 없을 수 있으며 복사 작업이 실패할 수 있습니다. 이 시나리오에서는 지역 복원 요청을 사용 하 여 다른 서버 및/또는 다른 지역에 복사본을 만듭니다. 자세한 내용은 [데이터베이스 백업을 사용 하 여 AZURE SQL 데이터베이스 복구](sql-database-recovery-using-backups.md#geo-restore) 를 참조 하세요.

## <a name="resolve-logins"></a>로그인 확인

대상 서버에서 새 데이터베이스가 온라인 상태가 되면 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) 문을 사용하여 새 데이터베이스의 사용자를 대상 서버의 로그인과 다시 매핑합니다. 분리된 사용자를 확인하려면 [분리된 사용자 문제 해결](https://msdn.microsoft.com/library/ms175475.aspx)을 참조하세요. [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)도 참조하세요.

새 데이터베이스의 모든 사용자가 원본 데이터베이스에서 가졌던 사용 권한을 그대로 유지합니다. 데이터베이스 복사를 실행한 사용자가 새 데이터베이스의 소유자가 되며 새 보안 식별자(SID)를 할당 받습니다. 복사 성공 후 다른 사용자를 다시 매핑하기 전에는 데이터베이스 소유자인 복사를 시작한 로그인만 새 데이터베이스에 로그인할 수 있습니다.

다른 SQL Database 서버로 데이터베이스를 복사할 때 사용자 및 로그인을 관리하는 방법을 알아보려면 [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md)을 참조하세요.

## <a name="database-copy-errors"></a>데이터베이스 복사 오류

Azure SQL Database에서 데이터베이스를 복사하는 동안 다음 오류가 발생할 수 있습니다. 자세한 내용은 [Azure SQL Database 복사](sql-database-copy.md)를 참조하세요.

| 오류 코드 | 심각도 | Description |
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
