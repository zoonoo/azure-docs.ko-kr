---
title: SQL Database 관리 되는 인스턴스-지정 시간 복원 | Microsoft Docs
description: SQL 관리 되는 인스턴스의 데이터베이스를 이전 시점으로 복원 하는 방법
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862136"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>SQL 관리 되는 인스턴스 데이터베이스를 이전 시점으로 복원

지정 시간 복원 (PITR)을 사용 하면 과거의 특정 시점에 다른 데이터베이스의 복사본으로 데이터베이스를 만들 수 있습니다. 이 문서에서는 관리 되는 인스턴스에서 데이터베이스의 지정 시간 복원을 수행 하는 방법을 설명 합니다.

지정 시간 복원은 오류로 인 한 인시던트, 잘못 로드 된 데이터, 중요 한 데이터 삭제, 기타 문제 등의 복구 시나리오와 테스트 또는 감사 목적으로 사용할 수 있습니다. 데이터베이스 설정에 따라 백업 파일은 7 일에서 35 일 사이의 기간 동안 보관 됩니다.

지정 시간 복원을 사용 하 여 다음 작업을 수행할 수 있습니다.

- 기존 데이터베이스에서 데이터베이스를 복원 합니다.
- 삭제 된 데이터베이스에서 데이터베이스를 복원 합니다.

또한 관리 되는 인스턴스를 사용 하는 경우 지정 시간 복원을 사용 하 여 다음 작업을 수행할 수 있습니다. 

- 동일한 관리 되는 인스턴스로 데이터베이스를 복원 합니다.
- 다른 관리 되는 인스턴스로 데이터베이스를 복원 합니다.


> [!NOTE]
> 전체 관리 되는 인스턴스의 지정 시간 복원은 가능 하지 않습니다. 가능 하며,이 문서에서 설명 하는 것은 관리 되는 인스턴스에서 호스팅되는 데이터베이스의 지정 시간 복원입니다.


## <a name="limitations"></a>제한 사항

다른 관리 되는 인스턴스로 복원 하는 경우 두 인스턴스는 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원 되지 않습니다.

> [!WARNING]
> 관리 되는 인스턴스의 저장소 크기에 주의 해야 합니다. 데이터 복원의 크기에 따라 인스턴스 저장소가 부족 하 게 될 수 있습니다. 복원 된 데이터에 충분 한 공간이 없는 경우에는 대체 방법을 사용 합니다.

다음 표에서는 관리 되는 인스턴스의 지정 시간 복구 시나리오를 보여 줍니다.

|           |기존 DB 복원| 기존 DB 복원|삭제 된 DB 복원| 삭제 된 DB 복원|
|:----------|:----------|:----------|:----------|:----------|
|Destination| 동일한 MI|다른 MI |동일한 MI|다른 MI |
|Azure Portal| 예|아니오 |아니요|아니요|
|Azure CLI|예 |예 |아니오|아니요|
|PowerShell| 예|예 |예|예|


## <a name="restore-existing-database"></a>기존 데이터베이스 복원

Azure Portal, Powershell 또는 Azure CLI를 사용 하 여 기존 데이터베이스를 동일한 인스턴스로 복원 합니다. 대상 관리 되는 인스턴스 및 리소스 그룹 속성을 지정 하 여 Powershell 또는 Azure CLI를 사용 하 여 데이터베이스를 다른 인스턴스로 복원 합니다. 이러한 매개 변수를 지정 하지 않으면 기본적으로 데이터베이스가 기존 인스턴스로 복원 됩니다. 다른 인스턴스로의 복원은 현재 Azure Portal를 통해 지원 되지 않습니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

1. [Azure 포털](https://portal.azure.com)할 수 있습니다. 
1. 관리 되는 인스턴스로 이동 하 여 복원 하려는 데이터베이스를 선택 합니다. 
1. 데이터베이스 페이지에서 **복원** 을 선택 합니다. 

    ![기존 데이터베이스 복원](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간에 대 한 지점을 선택 합니다.
1. **확인** 을 선택 하 여 데이터베이스를 복원 합니다. 이렇게 하면 새 데이터베이스가 생성 되 고 원하는 시점에 원본 데이터베이스의 데이터로 채워지는 복원 프로세스가 시작 됩니다. 복구 프로세스에 대 한 자세한 내용은 [복구 시간](sql-database-recovery-using-backups.md#recovery-time)을 참조 하세요. 

1. 관리 되는 인스턴스 찾기
1. 복원 하려는 데이터베이스 선택
1. 데이터베이스 화면에서 복원 동작을 클릭 합니다.
1. 복원 화면에서 데이터베이스를 복원 하는 기록의 시점 날짜 및 시간을 선택 합니다.
1. 확인 후 복원 프로세스가 시작 되 고 데이터베이스 크기에 따라 새 데이터베이스가 만들어지고 원하는 시점의 원본 데이터베이스의 데이터로 채워집니다. 복원 프로세스를 사용 하 여 복구 백업 문서를 확인 합니다.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 아직 설치 하지 않은 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조 하세요.

PowerShell을 사용 하 여 데이터베이스를 복원 하려면 매개 변수를 값으로 업데이트 하 고 다음 명령을 실행 합니다.

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

다른 관리 되는 인스턴스로 데이터베이스를 복원 하려면 대상 리소스 그룹 이름 및 대상 관리 되는 인스턴스 이름을 설정 합니다.  

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

Azure CLI를 사용 하 여 데이터베이스를 복원 하려면 매개 변수를 값으로 업데이트 하 고 다음 명령을 실행 합니다.


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


다른 관리 되는 인스턴스로 데이터베이스를 복원 하려면 대상 리소스 그룹 이름 및 대상 관리 되는 인스턴스 이름을 설정 합니다.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

사용 가능한 매개 변수에 대 한 자세한 내용은 [관리 되는 인스턴스 CLI](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)를 참조 하세요. 

---

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원 
 
삭제 된 데이터베이스 복원은 PowerShell을 통해서만 수행할 수 있습니다. 동일한 인스턴스나 다른 인스턴스로 데이터베이스를 복원할 수 있습니다. 

PowerShell을 사용 하 여 삭제 된 데이터베이스를 복원 하려면 매개 변수를 값으로 업데이트 하 고 다음 명령을 실행 합니다.

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

삭제 된 데이터베이스를 다른 인스턴스로 복원 하려면 리소스 그룹 이름 및 관리 되는 인스턴스 이름을 변경 합니다.

Location 매개 변수는 리소스 그룹 및 관리 되는 인스턴스의 위치와 일치 해야 합니다.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>기존 데이터베이스 덮어쓰기 
 
기존 데이터베이스를 덮어쓰려면 다음 작업도 수행 해야 합니다.

1. 덮어쓸 기존 데이터베이스를 삭제 합니다.
1. 지정 시간 복원 데이터베이스의 이름을 삭제 된 데이터베이스의 이름으로 바꿉니다. 


### <a name="drop-original-database"></a>원본 데이터베이스 삭제 
 
Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 데이터베이스를 삭제할 수 있습니다. 

관리 되는 인스턴스에 직접 연결 하 고 SSMS (SQL Server Management Studio 시작)를 실행 한 다음 Transact-sql (T-sql) 명령을 실행 하 여 데이터베이스를 삭제할 수도 있습니다.

```sql
DROP DATABASE WorldWideImporters;
```

다음 방법 중 하나를 사용 하 여 관리 되는 인스턴스 데이터베이스에 연결 합니다. 

- [SQL 가상 컴퓨터](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 및 사이트 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 관리 되는 인스턴스에서 데이터베이스를 선택 하 고 **삭제**를 선택 합니다.

   ![기존 데이터베이스 복원](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>새 데이터베이스 이름을 원래 대로 변경

관리 되는 인스턴스에 직접 연결 하 고, SQL Server Management Studio를 시작 하 고, 다음 Transact-sql (Transact-sql) 쿼리를 실행 하 여 복원 된 데이터베이스의 이름을 덮어쓰는 삭제 된 데이터베이스의 이름을 변경 합니다. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


다음 방법 중 하나를 사용 하 여 관리 되는 인스턴스 데이터베이스에 연결 합니다. 

- [SQL 가상 컴퓨터](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 및 사이트 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>다음 단계

[장기 보존](sql-database-long-term-retention.md) 및 [자동화 된 백업](sql-database-automated-backups.md)에 대해 알아봅니다. 
