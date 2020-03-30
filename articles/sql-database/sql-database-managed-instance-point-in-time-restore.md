---
title: 관리되는 인스턴스 - 시간 내에 복원(PITR)
description: 관리되는 인스턴스의 SQL 데이터베이스를 이전 시점으로 복원합니다.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268809"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>관리되는 인스턴스의 SQL 데이터베이스를 이전 시점으로 복원

PITR(시점 복원)을 사용하여 과거의 다른 데이터베이스의 복사본으로 데이터베이스를 만듭니다. 이 문서에서는 Azure SQL Database 관리 인스턴스에서 데이터베이스의 시점 복원을 수행하는 방법을 설명합니다.

시간 내 복원은 오류로 인한 인시던트, 잘못 로드된 데이터 또는 중요한 데이터의 삭제와 같은 복구 시나리오에서 유용합니다. 테스트 또는 감사에 사용하기에만 사용할 수도 있습니다. 백업 파일은 데이터베이스 설정에 따라 7~35일 동안 보관됩니다.

시간 별 복원은 데이터베이스를 복원할 수 있습니다.

- 기존 데이터베이스에서 볼 수 있습니다.
- 삭제된 데이터베이스에서 삭제됩니다.
- 동일한 관리 인스턴스 또는 다른 관리되는 인스턴스에. 

## <a name="limitations"></a>제한 사항

관리되는 인스턴스에 대한 시간 복원에는 다음과 같은 제한 사항이 있습니다.

- 관리되는 인스턴스에서 다른 인스턴스로 복원하는 경우 두 인스턴스가 동일한 구독 및 지역에 있어야 합니다. 지역 간 및 구독 간 복원은 현재 지원되지 않습니다.
- 전체 관리인스턴스의 시점을 복원할 수 없습니다. 이 문서에서는 관리되는 인스턴스에서 호스팅되는 데이터베이스의 시점 복원에 대해 설명합니다.

> [!WARNING]
> 관리되는 인스턴스의 저장소 크기를 알고 있어야 합니다. 복원할 데이터의 크기에 따라 인스턴스 저장소가 부족할 수 있습니다. 복원된 데이터에 대한 공간이 충분하지 않은 경우 다른 방법을 사용합니다.

다음 표에서는 관리되는 인스턴스에 대한 시점 복원 시나리오를 보여 주며 있습니다.

|           |기존 DB를 동일한 관리인스턴스로 복원| 기존 DB를 다른 관리되는 인스턴스로 복원|삭제된 DB를 동일한 관리 인스턴스로 복원|삭제된 DB를 다른 관리 인스턴스로 복원|
|:----------|:----------|:----------|:----------|:----------|
|**Azure 포털**| yes|예 |yes|예|
|**Azure CLI**|yes |yes |예|예|
|**Powershell**| yes|yes |yes|yes|

## <a name="restore-an-existing-database"></a>기존 데이터베이스 복원

Azure 포털, PowerShell 또는 Azure CLI를 사용하여 기존 데이터베이스를 동일한 인스턴스로 복원합니다. 데이터베이스를 다른 인스턴스로 복원하려면 PowerShell 또는 Azure CLI를 사용하여 대상 관리 인스턴스 및 리소스 그룹에 대한 속성을 지정할 수 있습니다. 이러한 매개 변수를 지정하지 않으면 데이터베이스가 기본적으로 기존 인스턴스로 복원됩니다. Azure 포털은 현재 다른 인스턴스로 복원하는 것을 지원하지 않습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure 포털에](https://portal.azure.com)로그인합니다. 
2. 관리되는 인스턴스로 이동하여 복원할 데이터베이스를 선택합니다.
3. 데이터베이스 페이지에서 **복원을** 선택합니다.

    ![Azure 포털을 사용하여 데이터베이스 복원](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. **복원** 페이지에서 데이터베이스를 복원할 날짜 및 시간에 대한 지점을 선택합니다.
5. 데이터베이스를 복원하려면 **확인을** 선택합니다. 이 작업은 새 데이터베이스를 만들고 지정된 시점에 원래 데이터베이스의 데이터로 채우는 복원 프로세스를 시작합니다. 복구 프로세스에 대한 자세한 내용은 [복구 시간을](sql-database-recovery-using-backups.md#recovery-time)참조하십시오.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Azure PowerShell이 아직 설치되어 있지 않은 경우 [Azure PowerShell 설치 모듈 을](https://docs.microsoft.com/powershell/azure/install-az-ps)참조하십시오.

PowerShell을 사용하여 데이터베이스를 복원하려면 다음 명령에서 매개 변수에 대한 값을 지정합니다. 그런 다음 명령을 실행합니다.

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

데이터베이스를 다른 관리 되는 인스턴스로 복원 하려면 대상 리소스 그룹 및 대상 관리 되는 인스턴스의 이름을 지정 합니다.  

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

자세한 내용은 [복원-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)를 참조하십시오.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI가 아직 설치되어 있지 않은 경우 [Azure CLI 설치를](/cli/azure/install-azure-cli?view=azure-cli-latest)참조하십시오.

Azure CLI를 사용하여 데이터베이스를 복원하려면 다음 명령에서 매개 변수에 대한 값을 지정합니다. 그런 다음 명령을 실행합니다.

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

데이터베이스를 다른 관리 되는 인스턴스로 복원 하려면 대상 리소스 그룹 및 관리 되는 인스턴스의 이름을 지정 합니다.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

사용 가능한 매개 변수에 대한 자세한 설명은 [관리되는 인스턴스에서 데이터베이스를 복원하기 위한 CLI 설명서를](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)참조하십시오.

---

## <a name="restore-a-deleted-database"></a>삭제된 데이터베이스 복원

삭제된 데이터베이스 복원은 PowerShell 또는 Azure 포털을 사용하여 수행할 수 있습니다. 삭제된 데이터베이스를 동일한 인스턴스로 복원하려면 Azure 포털 또는 PowerShell을 사용합니다. 삭제된 데이터베이스를 다른 인스턴스로 복원하려면 PowerShell을 사용합니다. 

### <a name="portal"></a>포털 


Azure 포털을 사용하여 관리되는 데이터베이스를 복구하려면 관리되는 인스턴스 개요 페이지를 열고 **삭제된 데이터베이스를 선택합니다.** 복원할 삭제된 데이터베이스를 선택하고 백업에서 복원된 데이터로 만들 새 데이터베이스의 이름을 입력합니다.

  ![삭제된 Azure SQL 인스턴스 데이터베이스 복원 스크린샷](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

데이터베이스를 동일한 인스턴스로 복원하려면 매개 변수 값을 업데이트한 다음 다음 PowerShell 명령을 실행합니다. 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

데이터베이스를 다른 관리 되는 인스턴스로 복원 하려면 대상 리소스 그룹 및 대상 관리 되는 인스턴스의 이름을 지정 합니다.

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
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

1. 덮어쓰려는 기존 데이터베이스를 삭제합니다.
2. 복원된 시점에 복원된 데이터베이스의 이름을 삭제한 데이터베이스의 이름으로 바꿉니다.

### <a name="drop-the-original-database"></a>원본 데이터베이스 삭제

Azure 포털, PowerShell 또는 Azure CLI를 사용하여 데이터베이스를 삭제할 수 있습니다.

관리되는 인스턴스에 직접 연결하고, SSMS(SQL Server 관리 스튜디오)를 시작한 다음 다음 T-SQL(Transact-SQL) 명령을 실행하여 데이터베이스를 삭제할 수도 있습니다.

```sql
DROP DATABASE WorldWideImporters;
```

다음 방법 중 하나를 사용하여 관리되는 인스턴스에서 데이터베이스에 연결합니다.

- [Azure 가상 컴퓨터를 통한 SSMS/Azure 데이터 스튜디오](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 포털에서 관리되는 인스턴스에서 데이터베이스를 선택한 다음 **삭제를**선택합니다.

   ![Azure 포털을 사용하여 데이터베이스 삭제](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

다음 PowerShell 명령을 사용하여 관리되는 인스턴스에서 기존 데이터베이스를 삭제합니다.

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 Azure CLI 명령을 사용하여 관리되는 인스턴스에서 기존 데이터베이스를 삭제합니다.

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>원래 데이터베이스 이름과 일치하도록 새 데이터베이스 이름을 변경합니다.

관리되는 인스턴스에 직접 연결하고 SQL Server 관리 스튜디오를 시작합니다. 그런 다음 다음 T-SQL(거래-SQL) 쿼리를 실행합니다. 쿼리는 복원된 데이터베이스의 이름을 덮어쓰려는 삭제된 데이터베이스의 이름으로 변경합니다.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

다음 방법 중 하나를 사용하여 관리되는 인스턴스에서 데이터베이스에 연결합니다.

- [Azure 가상 머신](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [지점 간](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [공용 엔드포인트](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>다음 단계

[자동화된 백업에](sql-database-automated-backups.md)대해 자세히 알아봅니다.
