---
title: 관리 작업 모니터링
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 작업을 모니터링하는 다양한 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 32b27eefb3cf8fe46b932f6e0e39274e67cadb7f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537070"
---
# <a name="monitoring-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance 관리 작업 모니터링
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance에서는 새로운 Managed Instance를 배포하고, 인스턴스 속성을 업데이트하고, 더 이상 필요 없는 인스턴스를 삭제하는 데 사용한 [관리 작업](management-operations-overview.md)의 모니터링을 제공합니다. 

## <a name="overview"></a>개요

모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포(새 인스턴스 생성)
- 인스턴스 업데이트(vCore, 예약된 스토리지 등의 인스턴스 속성 변경)
- 인스턴스 삭제

대부분의 관리 작업은 [장기 실행 작업](management-operations-overview.md#duration)입니다. 따라서 상태를 모니터링하거나 작업 단계를 진행률을 따라야 합니다. 

관리되는 인스턴스 관리 작업을 모니터링하는 방법에는 여러 가지가 있습니다.

- [리소스 그룹 배포](../../azure-resource-manager/templates/deployment-history.md)
- [활동 로그](../../azure-monitor/essentials/activity-log.md)
- [Managed Instance 작업 API](#managed-instance-operations-api)


다음 표에서는 관리 작업 모니터링 옵션을 비교합니다. 

| 옵션 | 보존 | 취소 지원 | 생성 | 업데이트 | DELETE | 취소 | 단계 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 리소스 그룹 배포 | 무한<sup>1</sup> | 아니요<sup>2</sup> | 표시 | 표시 | 표시되지 않음 | 표시 | 표시되지 않음 |
| 활동 로그 | 90일 | 예 | 표시 | 표시 | 표시 | 표시 |  표시되지 않음 |
| Managed Instance 작업 API | 24시간 | [예](management-operations-cancel.md) | 표시 | 표시 | 표시 | 표시 | 표시 |
|  |  |  |  |  |  |  | |

<sup>1</sup> 리소스 그룹의 배포 기록은 800회 배포로 제한됩니다.

<sup>2</sup> 리소스 그룹 배포는 취소 작업을 지원합니다. 그러나 취소 논리로 인해 취소 작업을 수행한 후에 배포하도록 예약된 작업만 취소됩니다. 리소스 그룹 배포가 취소되면 진행 중인 배포가 취소되지 않습니다. Managed Instance 배포는 Azure Resource Manger 관점에서 한 장기 실행 단계로 구성되기 때문에 리소스 그룹 배포를 취소해도 Managed Instance 배포는 취소되지 않으며 작업이 완료됩니다. 

## <a name="managed-instance-operations-api"></a>Managed Instance 작업 API

관리 작업 API는 작업을 모니터링하도록 특별히 설계되었습니다. Managed Instance 작업을 모니터링하면 작업 매개 변수 및 작업 단계에 대한 인사이트를 제공할 수 있을 뿐만 아니라 [특정 작업을 취소](management-operations-cancel.md)할 수 있습니다. 작업 세부 정보 및 취소 명령 외에도 이 API는 다중 리소스 배포를 사용하는 자동화 스크립트에서 사용할 수 있습니다. 즉, 진행 단계에 따라 일부 종속 리소스 배포를 시작할 수 있습니다.

API는 다음과 같습니다. 

| 명령 | 설명 |
| --- | --- |
|[Managed Instance 작업 - 가져오기](/rest/api/sql/managedinstanceoperations/get)|Managed Instance의 관리 작업을 가져옵니다.|
|[Managed Instance 작업 - 취소](/rest/api/sql/managedinstanceoperations/cancel)|Managed Instance의 비동기 작업을 취소합니다.|
|[Managed Instance 작업 - Managed Instance별 목록](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Managed Instance에서 수행한 작업 목록을 가져옵니다.|

> [!NOTE]
> API 버전 2020-02-02를 사용하여 작업 목록에서 Managed Instance 만들기 작업을 확인할 수 있습니다. Azure Portal과 최신 PowerShell 및 Azure CLI 패키지에 사용되는 기본 버전입니다.

## <a name="monitor-operations"></a>작업 모니터링

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal에서 Managed Instance **개요** 페이지를 사용하여 Managed Instance 작업을 모니터링합니다. 

예를 들어 **만들기 작업** 이 **개요** 페이지의 만들기 프로세스를 시작할 때 표시됩니다. 

![Managed Instance 만들기 진행률](./media/management-operations-monitor/monitoring-create-operation.png)

**진행 중인 작업** 을 선택하여 **진행 중인 작업** 페이지를 열고 **만들기** 또는 **업데이트** 작업을 표시합니다. 이 페이지에서 작업을 [취소](management-operations-cancel.md)할 수도 있습니다.  

![Managed Instance 작업 세부 정보](./media/management-operations-monitor/monitoring-operation-details.png)

> [!NOTE]
> REST API 버전 2020-02-02를 사용하여 Azure Portal, PowerShell, Azure CLI 또는 기타 도구를 통해 제출된 만들기 작업을 [취소할 수 있습니다](management-operations-cancel.md). 만들기 작업을 제출하는 데 사용된 2020-02-02 이전의 REST API 버전은 인스턴스 배포를 시작하지만, 배포는 Operations API에 나열되지 않으며 취소할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Get-AzSqlInstanceOperation cmdlet은 Managed Instance의 작업에 대한 정보를 가져옵니다. Managed Instance의 모든 작업을 보거나 작업 이름을 제공하여 특정 작업을 볼 수 있습니다.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation `
    -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup
```

자세한 명령 설명은 [Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)을 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

az sql mi op list는 Managed Instance에서 수행한 작업 목록을 가져옵니다. Azure CLI를 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName 
```

자세한 명령 설명은 [az sql mi op](/cli/azure/sql/mi/op)를 참조하세요.

---

## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
