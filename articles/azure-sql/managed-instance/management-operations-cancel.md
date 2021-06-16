---
title: 관리 작업 취소
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 작업을 취소하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: devx-track-azurepowershell
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, bonova, MashaMSFT
ms.date: 09/03/2020
ms.openlocfilehash: 13e0190b330d1cae0d819ee28c9c8fabe98e8dcb
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110689740"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance 관리 작업 취소
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 새로운 관리되는 인스턴스를 배포하거나 인스턴스 속성을 업데이트하는 경우와 같은 일부 [관리 작업](management-operations-overview.md)을 취소하는 기능을 제공합니다. 

## <a name="overview"></a>개요

 모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포(새 인스턴스 생성)
- 인스턴스 업데이트(vCore, 예약된 스토리지 등의 인스턴스 속성 변경)
- 인스턴스 삭제

[관리 작업의 진행률과 상태를 모니터링](management-operations-monitor.md)하고 필요한 경우 일부를 취소할 수 있습니다. 

다음 표에는 관리 작업, 취소 가능 여부 및 일반적인 전체 기간이 간략하게 나와 있습니다.

범주  |작업  |취소 가능  |예상 취소 기간  |
|---------|---------|---------|---------|
|배포 |인스턴스 만들기 |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 스토리지 확장/축소(범용) |예 |  |
|업데이트 |인스턴스 스토리지 확장/축소(중요 비즈니스용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 컴퓨팅(vCore) 확장 및 축소(범용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 컴퓨팅(vCore) 확장 및 축소(중요 비즈니스용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 서비스 계층 변경(범용에서 중요 비즈니스용으로 및 그 반대로) |예 |작업의 90%가 5분 후에 완료됩니다. |
|DELETE |인스턴스 삭제 |예 |  |
|DELETE |가상 클러스터 삭제(사용자 시작 작업) |예 |  |

## <a name="cancel-management-operation"></a>관리 작업 취소

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 사용하여 관리 작업을 취소하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. SQL Managed Instance의 **개요** 블레이드로 이동합니다. 
1. 진행 중인 작업 옆에 있는 **알림** 상자를 선택하여 **진행 중인 작업** 페이지를 엽니다. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="진행 중인 작업 상자를 선택하여 진행 중인 작업 페이지를 엽니다.":::

1. 페이지 하단에 있는 **작업 취소** 를 선택합니다. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="작업을 취소하려면 취소를 선택합니다.":::

1. 작업을 취소할 것인지 확인합니다. 


취소 요청이 성공하면 관리 작업이 취소되고 오류가 발생합니다. 그리고 취소가 성공하거나 실패한다는 알림이 표시됩니다.

![작업 취소 결과](./media/management-operations-cancel/canceling-operation-result.png)


취소 요청이 실패하거나 취소 버튼이 활성화되지 않은 경우 관리 작업이 취소할 수 없는 상태가 되었으며 곧 완료될 것임을 의미합니다.  관리 작업은 완료될 때까지 실행을 계속합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell을 아직 설치하지 않은 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

관리 작업을 취소하려면 관리 작업 이름을 지정해야 합니다. 따라서 먼저 get 명령을 사용하여 작업 목록을 검색한 후 특정 작업을 취소합니다.

```powershell-interactive
$managedInstance = "<Your-instance-name>"
$resourceGroup = "<Your-resource-group-name>"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

자세한 명령 설명은 [AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation) 및 [AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)를 참조하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 아직 설치하지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

관리 작업을 취소하려면 관리 작업 이름을 지정해야 합니다. 따라서 먼저 get 명령을 사용하여 작업 목록을 검색한 후 특정 작업을 취소합니다.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

자세한 명령 설명은 [az sql mi op](/cli/azure/sql/mi/op)를 참조하세요.

---

## <a name="canceled-deployment-request"></a>취소된 배포 요청

API 버전 2020-02-02에서는 인스턴스 생성 요청이 수락되는 즉시 배포 프로세스의 진행 상황에 관계없이 인스턴스가 리소스로 존재하기 시작합니다(관리되는 인스턴스 상태는 **프로비전** 임). 인스턴스 배포 요청(새 인스턴스 만들기)을 취소하면 관리되는 인스턴스가 **프로비전** 상태에서 **FailedToCreate** 로 이동합니다.

만들지 못한 인스턴스는 여전히 리소스로 존재하며 다음과 같습니다. 

- 요금이 부과되지 않음
- 리소스 제한(서브넷 또는 vCore 할당량)을 계산하지 않음


> [!NOTE]
> 리소스 또는 관리되는 인스턴스 목록의 노이즈를 최소화하려면 배포에 실패한 인스턴스 또는 배포가 취소된 인스턴스를 삭제하세요. 


## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
