---
title: PITR(특정 시점 복원)
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance의 데이터베이스를 이전의 특정 시점으로 복원합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: 4c116b378c72d87641157fc453d65e46be9f43ec
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787169"
---
# <a name="restore-a-database-in-azure-sql-managed-instance-to-a-previous-point-in-time"></a>Azure SQL Managed Instance의 데이터베이스를 이전의 특정 시점으로 복원
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

PITR(특정 시점 복원)을 사용하여 과거의 특정 시점에서 다른 데이터베이스의 복사본으로 데이터베이스를 만듭니다. 이 문서에서는 Azure SQL Managed Instance에서 데이터베이스의 특정 시점 복원을 수행하는 방법을 설명합니다.

특정 시점 복원은 오류로 인한 인시던트, 잘못 로드된 데이터 또는 중요한 데이터 삭제와 같은 복구 시나리오에서 유용합니다. 테스트 또는 감사 목적으로만 사용할 수도 있습니다. 백업 파일은 데이터베이스 설정에 따라 7~35일 동안 유지됩니다.

특정 시점 복원은 다음과 같이 데이터베이스를 복원할 수 있습니다.

- 기존 데이터베이스에서
- 삭제된 데이터베이스에서
- 동일한 SQL Managed Instance로 또는 다른 SQL Managed Instance로 

## <a name="limitations"></a>제한 사항

SQL Managed Instance에 대한 특정 시점 복원에는 다음과 같은 제한 사항이 있습니다.

- SQL Managed Instance의 한 인스턴스에서 다른 인스턴스로 복원하는 경우 두 인스턴스 모두 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원되지 않습니다.
- 전체 SQL Managed Instance의 특정 시점 복원은 수행할 수 없습니다. 이 문서에서는 SQL Managed Instance에서 호스트되는 데이터베이스의 특정 시점 복원과 같이 가능한 작업만 설명합니다.

> [!WARNING]
> SQL Managed Instance의 스토리지 크기를 알고 있어야 합니다. 복원할 데이터의 크기에 따라 인스턴스 스토리지가 부족할 수 있습니다. 복원된 데이터를 저장할 공간이 충분하지 않은 경우에는 다른 방법을 사용합니다.

다음 표에서는 SQL Managed Instance에 대한 특정 시점 복원 시나리오를 보여 줍니다.

|           |기존 DB를 SQL Managed Instance의 동일한 인스턴스로 복원| 기존 DB를 다른 SQL Managed Instance로 복원|삭제된 DB를 동일한 SQL Managed Instance로 복원|삭제된 DB를 다른 SQL Managed Instance로 복원|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portal**| 예|아니요 |예|아니요|
|**Azure CLI**|예 |예 |아니요|아니요|
|**PowerShell**| 예|예 |예|예|

## <a name="restore-an-existing-database"></a>기존 데이터베이스 복원

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 기존 데이터베이스를 동일한 SQL Managed Instance로 복원합니다. 다른 SQL Managed Instance로 데이터베이스를 복원하려면 PowerShell 또는 Azure CLI를 사용하여 대상 SQL Managed Instance 및 리소스 그룹에 대한 속성을 지정할 수 있습니다. 이러한 매개 변수를 지정하지 않으면 기본적으로 데이터베이스가 기존 SQL Managed Instance로 복원됩니다. Azure Portal은 현재 다른 SQL Managed Instance로의 복원을 지원하지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. SQL Managed Instance로 이동하고 복원하려는 데이터베이스를 선택합니다.
3. 데이터베이스 페이지에서 **복원** 을 선택합니다.

    ![Azure Portal을 사용하여 데이터베이스 복원](./media/point-in-time-restore/restore-database-to-mi.png)

4. **복원** 페이지에서 데이터베이스를 복원할 시점(날짜 및 시간)을 선택합니다.
5. **확인** 을 선택하여 데이터베이스를 복원합니다. 이 작업은 복원 프로세스를 시작합니다. 그러면 새 데이터베이스가 만들어지고 특정 시점의 원래 데이터베이스의 데이터로 채워집니다. 복구 프로세스에 대한 자세한 내용은 [복구 시간](../database/recovery-using-backups.md#recovery-time)을 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell을 아직 설치하지 않은 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

PowerShell을 사용하여 데이터베이스를 복원하려면 다음 명령에서 매개 변수의 값을 지정합니다. 그런 다음 명령을 실행합니다.

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

다른 SQL Managed Instance로 데이터베이스를 복원하려면 대상 리소스 그룹 및 대상 SQL Managed Instance의 이름도 지정합니다.  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

자세한 내용은 [AzSqlInstanceDatabase 복원](/powershell/module/az.sql/restore-azsqlinstancedatabase)을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure CLI를 사용하여 데이터베이스를 복원하려면 다음 명령에서 매개 변수의 값을 지정합니다. 그런 다음 명령을 실행합니다.

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

다른 SQL Managed Instance로 데이터베이스를 복원하려면 대상 리소스 그룹 및 SQL Managed Instance의 이름도 지정합니다.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

사용 가능한 매개 변수에 대한 자세한 내용은 [SQL Managed Instance에서 데이터베이스 복원에 대한 CLI 설명서](/cli/azure/sql/midb#az_sql_midb_restore)를 참조하세요.

---

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원

PowerShell 또는 Azure Portal을 사용하여 삭제된 데이터베이스를 복원할 수 있습니다. 삭제된 데이터베이스를 동일한 인스턴스로 복원하려면 Azure Portal 또는 PowerShell 중 하나를 사용합니다. 삭제된 데이터베이스를 다른 인스턴스로 복원하려면 PowerShell을 사용합니다. 

### <a name="portal"></a>포털 


Azure Portal을 사용하여 관리형 데이터베이스를 복구하려면 SQL Managed Instance 개요 페이지를 열고 **삭제된 데이터베이스** 를 선택합니다. 복원할 삭제된 데이터베이스를 선택하고 백업에서 복원된 데이터로 만들어질 새 데이터베이스의 이름을 입력합니다.

  ![삭제된 Azure SQL 인스턴스 데이터베이스 복원 스크린샷](./media/point-in-time-restore/restore-deleted-sql-managed-instance-annotated.png)

../../sql-database

### <a name="powershell"></a>PowerShell

동일한 인스턴스에 데이터베이스를 복원하려면 매개 변수 값을 업데이트한 후 다음 PowerShell 명령을 실행합니다. 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

다른 SQL Managed Instance로 데이터베이스를 복원하려면 대상 리소스 그룹 및 대상 SQL Managed Instance의 이름도 지정합니다.

```powershell-interactive
$targetResourceGroupName = "<Resource group of target SQL Managed Instance>"
$targetInstanceName = "<Target SQL Managed Instance name>"

Restore-AzSqlinstanceDatabase -FromPointInTimeBackup -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>기존 데이터베이스 덮어쓰기

기존 데이터베이스를 덮어쓰려면 다음을 수행해야 합니다.

1. 덮어쓸 기존 데이터베이스를 삭제합니다.
2. 특정 시점 복원 데이터베이스의 이름을 삭제한 데이터베이스의 이름으로 바꿉니다.

### <a name="drop-the-original-database"></a>원본 데이터베이스 삭제

Azure Portal, PowerShell 또는 Azure CLI를 사용하여 데이터베이스를 삭제할 수 있습니다.

SQL Managed Instance에 직접 연결하고 SSMS(SQL Server Management Studio)를 시작한 후 다음 T-SQL(Transact-SQL) 명령을 실행하여 데이터베이스를 삭제할 수도 있습니다.

```sql
DROP DATABASE WorldWideImporters;
```

다음 방법 중 하나를 사용하여 SQL Managed Instance의 데이터베이스에 연결합니다.

- [Azure 가상 머신을 통해 SSMS/Azure Data Studio](./connect-vm-instance-configure.md)
- [지점 및 사이트 간](./point-to-site-p2s-configure.md)
- [공용 엔드포인트](./public-endpoint-configure.md)

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 SQL Managed Instance의 데이터베이스를 선택하고 **삭제** 를 선택합니다.

   ![Azure Portal을 사용하여 데이터베이스 삭제](./media/point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 사용하여 SQL Managed Instance에서 기존 데이터베이스를 삭제합니다.

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<SQL Managed Instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 Azure CLI 명령을 사용하여 SQL Managed Instance에서 기존 데이터베이스를 삭제합니다.

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>새 데이터베이스 이름을 원래 데이터베이스 이름과 일치하도록 변경

SQL Managed Instance에 직접 연결하고 SQL Server Management Studio를 시작합니다. 그런 다음 T-SQL(Transact-SQL) 쿼리를 실행합니다. 이 쿼리는 복원된 데이터베이스의 이름을 덮어쓰려는 삭제된 데이터베이스의 이름으로 변경합니다.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

다음 방법 중 하나를 사용하여 SQL Managed Instance의 데이터베이스에 연결합니다.

- [Azure 가상 머신](./connect-vm-instance-configure.md)
- [지점 및 사이트 간](./point-to-site-p2s-configure.md)
- [공용 엔드포인트](./public-endpoint-configure.md)

## <a name="next-steps"></a>다음 단계

[자동화된 백업](../database/automated-backups-overview.md)에 대해 알아봅니다.
