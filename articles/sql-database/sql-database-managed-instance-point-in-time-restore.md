---
title: 관리 되는 인스턴스-특정 시점 복원
description: 관리 되는 인스턴스의 SQL 데이터베이스를 이전 시점으로 복원 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707659"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>관리 되는 인스턴스의 SQL 데이터베이스를 이전 시점으로 복원

PITR (지정 시간 복원)을 사용 하 여 과거의 특정 시점에서 다른 데이터베이스의 복사본으로 데이터베이스를 만듭니다. 이 문서에서는 Azure SQL Database 관리 되는 인스턴스에서 데이터베이스의 지정 시간 복원을 수행 하는 방법을 설명 합니다.

특정 시점 복원은 오류로 인 한 인시던트, 잘못 로드 된 데이터 또는 중요 한 데이터 삭제와 같은 복구 시나리오에서 유용 합니다. 테스트 또는 감사에만 사용할 수도 있습니다. 백업 파일은 데이터베이스 설정에 따라 7 일에서 35 일 동안 유지 됩니다.

지정 시간 복원은 다음을 수행할 수 있습니다.

- 기존 데이터베이스에서 데이터베이스를 복원 합니다.
- 삭제 된 데이터베이스에서 데이터베이스를 복원 합니다.

관리 되는 인스턴스의 경우 지정 시간 복원도 다음을 수행할 수 있습니다.

- 동일한 관리 되는 인스턴스로 데이터베이스를 복원 합니다.
- 다른 관리 되는 인스턴스로 데이터베이스를 복원 합니다.

> [!NOTE]
> 전체 관리 되는 인스턴스의 지정 시간 복원은 가능 하지 않습니다. 이 문서에서는 관리 되는 인스턴스에서 호스팅되는 데이터베이스의 지정 시간 복원과 같은 작업을 설명 합니다.

## <a name="limitations"></a>제한 사항

하나의 관리 되는 인스턴스에서 다른 인스턴스로 복원 하는 경우 두 인스턴스는 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원 되지 않습니다.

> [!WARNING]
> 관리 되는 인스턴스의 저장소 크기를 알고 있어야 합니다. 복원할 데이터의 크기에 따라 인스턴스 저장소가 부족 한 것일 수 있습니다. 복원 된 데이터를 저장할 공간이 충분 하지 않은 경우에는 다른 방법을 사용 합니다.

다음 표에서는 관리 되는 인스턴스의 지정 시간 복원 시나리오를 보여 줍니다.

|           |기존 DB를 동일한 관리 되는 인스턴스로 복원| 기존 DB를 다른 관리 되는 인스턴스로 복원|삭제 된 DB를 동일한 관리 되는 인스턴스로 복원|삭제 된 DB를 다른 관리 되는 인스턴스로 복원|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 포털**| yes|아닙니다. |아닙니다.|아닙니다.|
|**Azure CLI**|yes |yes |아닙니다.|아닙니다.|
|**PowerShell**| yes|yes |yes|yes|

## <a name="restore-an-existing-database"></a>기존 데이터베이스 복원

Azure Portal, Powershell 또는 Azure CLI를 사용 하 여 기존 데이터베이스를 동일한 인스턴스로 복원 합니다. 다른 인스턴스로 데이터베이스를 복원 하려면 대상 관리 되는 인스턴스 및 리소스 그룹에 대 한 속성을 지정할 수 있도록 Powershell 또는 Azure CLI를 사용 합니다. 이러한 매개 변수를 지정 하지 않으면 기본적으로 데이터베이스가 기존 인스턴스로 복원 됩니다. Azure Portal는 현재 다른 인스턴스로의 복원을 지원 하지 않습니다.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com)에 로그인합니다. 
2. 관리 되는 인스턴스로 이동 하 여 복원 하려는 데이터베이스를 선택 합니다.
3. 데이터베이스 페이지에서 **복원** 을 선택 합니다.

    ![Azure Portal를 사용 하 여 데이터베이스 복원](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간에 대 한 지점을 선택 합니다.
5. **확인** 을 선택 하 여 데이터베이스를 복원 합니다. 이 작업은 복원 프로세스를 시작 합니다. 그러면 새 데이터베이스가 만들어지고 지정 된 시점의 원래 데이터베이스의 데이터로 채워집니다. 복구 프로세스에 대 한 자세한 내용은 [복구 시간](sql-database-recovery-using-backups.md#recovery-time)을 참조 하세요.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 아직 설치 하지 않은 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조 하세요.

PowerShell을 사용 하 여 데이터베이스를 복원 하려면 다음 명령에서 매개 변수의 값을 지정 합니다. 그런 다음 명령을 실행 합니다.

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
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

다른 관리 되는 인스턴스로 데이터베이스를 복원 하려면 대상 리소스 그룹 및 관리 되는 인스턴스의 이름도 지정 해야 합니다.  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

자세한 내용은 [AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)를 참조 하세요.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 아직 설치 되지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조 하세요.

Azure CLI를 사용 하 여 데이터베이스를 복원 하려면 다음 명령에서 매개 변수의 값을 지정 합니다. 그런 다음 명령을 실행 합니다.

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

다른 관리 되는 인스턴스로 데이터베이스를 복원 하려면 대상 리소스 그룹 및 관리 되는 인스턴스의 이름도 지정 해야 합니다.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

사용 가능한 매개 변수에 대 한 자세한 설명은 CLI 설명서를 참조 하 여 [관리 되는 인스턴스에서 데이터베이스를 복원](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)하십시오.

---

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원

삭제 된 데이터베이스 복원은 PowerShell을 사용해 서만 수행할 수 있습니다. 동일한 인스턴스 또는 다른 인스턴스로 데이터베이스를 복원할 수 있습니다.

PowerShell을 사용 하 여 삭제 된 데이터베이스를 복원 하려면 다음 명령에서 매개 변수의 값을 지정 합니다. 그런 다음 명령을 실행 합니다.

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

삭제 된 데이터베이스를 다른 인스턴스로 복원 하려면 리소스 그룹 및 관리 되는 인스턴스의 이름을 변경 합니다. 또한 location 매개 변수가 리소스 그룹 및 관리 되는 인스턴스의 위치와 일치 하는지 확인 합니다.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>기존 데이터베이스 덮어쓰기

기존 데이터베이스를 덮어쓰려면 다음을 수행 해야 합니다.

1. 덮어쓸 기존 데이터베이스를 삭제 합니다.
2. 지정 시간 복원 데이터베이스의 이름을 삭제 한 데이터베이스의 이름으로 바꿉니다.

### <a name="drop-the-original-database"></a>원본 데이터베이스를 삭제 합니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 데이터베이스를 삭제할 수 있습니다.

관리 되는 인스턴스에 직접 연결 하 여 SSMS (SQL Server Management Studio 시작)를 실행 한 후 다음 Transact-sql (T-sql) 명령을 실행 하 여 데이터베이스를 삭제할 수도 있습니다.

```sql
DROP DATABASE WorldWideImporters;
```

다음 방법 중 하나를 사용 하 여 관리 되는 인스턴스의 데이터베이스에 연결 합니다.

- [Azure 가상 머신을 통한 SSMS/Azure Data Studio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 및 사이트 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure Portal에서 관리 되는 인스턴스에서 데이터베이스를 선택 하 고 **삭제**를 선택 합니다.

   ![Azure Portal를 사용 하 여 데이터베이스 삭제](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

다음 PowerShell 명령을 사용 하 여 관리 되는 인스턴스에서 기존 데이터베이스를 삭제 합니다.

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 Azure CLI 명령을 사용 하 여 관리 되는 인스턴스에서 기존 데이터베이스를 삭제할 수 있습니다.

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>새 데이터베이스 이름을 원래 데이터베이스 이름과 일치 하도록 변경 합니다.

관리 되는 인스턴스에 직접 연결 하 고 SQL Server Management Studio를 시작 합니다. 그런 다음 Transact-sql (T-sql) 쿼리를 실행 합니다. 이 쿼리는 복원 된 데이터베이스의 이름을 덮어쓰려고 하는 삭제 된 데이터베이스의 이름으로 변경 합니다.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

다음 방법 중 하나를 사용 하 여 관리 되는 인스턴스의 데이터베이스에 연결 합니다.

- [Azure 가상 머신](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 및 사이트 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>다음 단계

자동화 된 [백업](sql-database-automated-backups.md)에 대해 알아봅니다.
