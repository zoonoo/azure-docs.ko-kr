---
title: 관리 작업 모니터링
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 작업을 모니터링 하는 다양 한 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: bdb021bc0247972fa29975c62bc9214e3b474e2c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996843"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance 관리 작업 모니터링
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 새 관리 되는 인스턴스를 배포 하거나, 인스턴스 속성을 업데이트 하거나, 더 이상 필요 하지 않은 경우 인스턴스를 삭제 하는 데 사용 하는 [관리 작업](management-operations-overview.md) 을 모니터링 합니다. 

## <a name="overview"></a>개요

모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포 (새 인스턴스 생성).
- 인스턴스 업데이트 (vCores 또는 예약 된 저장소와 같은 인스턴스 속성 변경)
- 인스턴스를 삭제 합니다.

대부분의 관리 작업은 [장기 실행 작업](management-operations-overview.md#duration)입니다. 따라서 상태를 모니터링 하거나 작업 단계를 진행 해야 합니다. 

관리 되는 인스턴스 관리 작업을 모니터링 하는 방법에는 여러 가지가 있습니다.

- [리소스 그룹 배포](../../azure-resource-manager/templates/deployment-history.md)
- [활동 로그](../../azure-monitor/platform/activity-log.md)
- [관리 되는 인스턴스 작업 API](#managed-instance-operations-api)


다음 표에서는 관리 작업 모니터링 옵션을 비교 합니다. 

| 옵션 | 보존 | 취소 지원 | 만들기 | 업데이트 | 삭제 | 취소 | 단계 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 리소스 그룹 배포 | 무한<sup>1</sup> | 아니요<sup>2</sup> | 표시 | 표시 | 표시되지 않음 | 표시 | 표시되지 않음 |
| 활동 로그 | 90일 | 예 | 표시 | 표시 | 표시 | 표시 |  표시되지 않음 |
| 관리 되는 인스턴스 작업 API | 24시간 | [예](management-operations-cancel.md) | 표시 | 표시 | 표시 | 표시 | 표시 |
|  |  |  |  |  |  |  | |

<sup>1</sup> 리소스 그룹의 배포 기록은 800 배포로 제한 됩니다.

<sup>2</sup> 리소스 그룹 배포는 취소 작업을 지원 합니다. 그러나 취소 논리로 인해 취소 작업을 수행한 후에 배포 하도록 예약 된 작업만 취소 됩니다. 리소스 그룹 배포가 취소 되 면 진행 중인 배포가 취소 되지 않습니다. 관리 되는 인스턴스 배포는 Azure Resource manager 관점에서 한 장기 실행 단계로 구성 되기 때문에 리소스 그룹 배포를 취소 해도 관리 되는 인스턴스 배포는 취소 되지 않으며 작업이 완료 됩니다. 

## <a name="managed-instance-operations-api"></a>관리 되는 인스턴스 작업 API

관리 작업 Api는 특별히 작업을 모니터링 하도록 설계 되었습니다. 관리 되는 인스턴스 작업을 모니터링 하면 작업 매개 변수 및 작업 단계에 대 한 정보를 제공할 수 있을 뿐만 아니라 [특정 작업을 취소할](management-operations-cancel.md)수 있습니다. 작업 정보 및 취소 명령 외에도이 API는 다중 리소스 배포를 사용 하는 자동화 스크립트에서 사용할 수 있습니다. 즉, 진행 단계에 따라 일부 종속 리소스 배포를 시작할 수 있습니다.

Api는 다음과 같습니다. 

| 명령 | 설명 |
| --- | --- |
|[Managed Instance 작업-가져오기](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/get)|관리 되는 인스턴스의 관리 작업을 가져옵니다.|
|[Managed Instance 작업-취소](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/cancel)|관리 되는 인스턴스에서 비동기 작업을 취소 합니다.|
|[Managed Instance 작업-Managed Instance 별로 나열](https://docs.microsoft.com/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|관리 되는 인스턴스에서 수행 된 작업 목록을 가져옵니다.|

> [!NOTE]
> API 버전 2020-02-02을 사용 하 여 작업 목록에서 관리 되는 인스턴스 만들기 작업을 확인할 수 있습니다. Azure Portal와 최신 PowerShell 및 Azure CLI 패키지에 사용 되는 기본 버전입니다.

## <a name="monitor-operations"></a>작업 모니터링

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 관리 되는 인스턴스 **개요** 페이지를 사용 하 여 관리 되는 인스턴스 작업을 모니터링할 수 있습니다. 

예를 들어 **만들기 작업이** **개요** 페이지의 만들기 프로세스를 시작할 때 표시 됩니다. 

![관리 되는 인스턴스 만들기 진행률](./media/management-operations-monitor/monitoring-create-operation.png)

진행 **중인 작업을 선택 하** 여 **진행 중인 작업** 페이지를 열고 **만들기** 또는 **업데이트** 작업을 봅니다. 이 페이지 에서도 작업을 [취소할](management-operations-cancel.md) 수 있습니다.  

![관리 되는 인스턴스 작업 세부 정보](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> REST API 버전 2020-02-02을 사용 하 여 Azure Portal, PowerShell Azure CLI 또는 기타 도구를 통해 제출 된 작업을 [취소할 수 있습니다](management-operations-cancel.md). 만들기 작업을 제출 하는 데 사용 된 2020-02-02 보다 오래 된 REST API 버전은 인스턴스 배포를 시작 하지만 배포는 Operations API에 나열 되지 않으며 취소할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

AzSqlInstanceOperation cmdlet은 관리 되는 인스턴스의 작업에 대 한 정보를 가져옵니다. 관리 되는 인스턴스의 모든 작업을 보거나 작업 이름을 제공 하 여 특정 작업을 볼 수 있습니다.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

자세한 명령 설명은 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation)를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az sql mi op 목록은 관리 되는 인스턴스에서 수행 된 작업 목록을 가져옵니다. Azure CLI 아직 설치 되지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

자세한 명령 설명은 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)를 참조 하세요.

---

## <a name="next-steps"></a>다음 단계

- 첫 번째 관리 되는 인스턴스를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조 하세요.
- 기능 및 비교 목록은 [일반적인 SQL 기능](../database/features-comparison.md)을 참조 하세요.
- VNet 구성에 대 한 자세한 내용은 [SQL Managed Instance vnet 구성](connectivity-architecture-overview.md)을 참조 하세요.
- 관리 되는 인스턴스를 만들고 백업 파일에서 데이터베이스를 복원 하는 빠른 시작은 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
- 마이그레이션을 위해 Azure Database Migration Service를 사용 하는 방법에 대 한 자습서는 [Database Migration Service를 사용 하 여 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조 하세요.
