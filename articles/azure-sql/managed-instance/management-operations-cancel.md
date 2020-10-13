---
title: 관리 작업 취소
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 작업을 취소 하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 4ec999cc35e7d18287679c74c6d45a5aa2ecb9e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997240"
---
# <a name="canceling-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance 관리 작업 취소
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance는 새 관리 되는 인스턴스를 배포 하거나 인스턴스 속성을 업데이트 하는 경우와 같은 일부 [관리 작업](management-operations-overview.md)을 취소 하는 기능을 제공 합니다. 

## <a name="overview"></a>개요

 모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포 (새 인스턴스 생성).
- 인스턴스 업데이트 (vCores 또는 예약 된 저장소와 같은 인스턴스 속성 변경)
- 인스턴스를 삭제 합니다.

[관리 작업의 진행률과 상태를 모니터링](management-operations-monitor.md) 하 고 필요한 경우 일부를 취소할 수 있습니다. 

다음 표에서는 관리 작업을 취소할 수 있는지 여부와 일반적인 전체 지속 시간을 요약 하 여 보여 줍니다.

범주  |작업(Operation)  |취소  |예상 취소 기간  |
|---------|---------|---------|---------|
|배포 |인스턴스 만들기 |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 저장소 확장/축소 (범용) |아니요 |  |
|업데이트 |인스턴스 저장소 확장/축소 (중요 비즈니스용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 컴퓨팅(vCore) 확장 및 축소(범용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 컴퓨팅(vCore) 확장 및 축소(중요 비즈니스용) |예 |작업의 90%가 5분 후에 완료됩니다. |
|업데이트 |인스턴스 서비스 계층 변경(범용에서 중요 비즈니스용으로 및 그 반대로) |예 |작업의 90%가 5분 후에 완료됩니다. |
|DELETE |인스턴스 삭제 |아니요 |  |
|DELETE |가상 클러스터 삭제(사용자 시작 작업) |아니요 |  |

## <a name="cancel-management-operation"></a>관리 작업 취소

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 관리 작업을 취소 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 로 이동 합니다.
1. SQL Managed Instance의 **개요** 블레이드로 이동 합니다. 
1. 진행 중인 작업 옆의 **알림** 상자를 선택 하 여 **진행 중인 작업** 페이지를 엽니다. 

   :::image type="content" source="media/management-operations-cancel/open-ongoing-operation.png" alt-text="진행 중인 작업 상자를 선택 하 여 진행 중인 작업 페이지를 엽니다.":::

1. 페이지 맨 아래에 있는 **작업 취소** 를 선택 합니다. 

   :::image type="content" source="media/management-operations-cancel/cancel-operation.png" alt-text="진행 중인 작업 상자를 선택 하 여 진행 중인 작업 페이지를 엽니다.":::

1. 작업을 취소할 것인지 확인 합니다. 


취소 요청이 성공 하면 관리 작업이 취소 되 고 오류가 발생 합니다. 취소가 성공 하거나 실패 한다는 알림을 받게 됩니다.

![작업 결과 취소](./media/management-operations-cancel/canceling-operation-result.png)


취소 요청이 실패 하거나 취소 단추가 활성화 되어 있지 않으면 관리 작업에서 취소할 수 없음 상태가 되어 곧 완료 됩니다.  관리 작업은 완료 될 때까지 실행을 계속 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 아직 설치 하지 않은 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조 하세요.

관리 작업을 취소 하려면 관리 작업 이름을 지정 해야 합니다. 따라서 먼저 get 명령을 사용 하 여 작업 목록을 검색 한 다음 특정 작업을 취소 합니다.

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

자세한 명령 설명은 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) 및 [AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation)를 참조 하세요.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 아직 설치 되지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조 하세요.

관리 작업을 취소 하려면 관리 작업 이름을 지정 해야 합니다. 따라서 먼저 get 명령을 사용 하 여 작업 목록을 검색 한 다음 특정 작업을 취소 합니다.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName |
   --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do

az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

자세한 명령 설명은 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op)를 참조 하세요.

---

## <a name="canceled-deployment-request"></a>취소 된 배포 요청

API 버전 2020-02-02을 사용 하는 경우 인스턴스 생성 요청이 수락 되는 즉시 인스턴스는 배포 프로세스의 진행 상황에 관계 없이 리소스로 존재 하기 시작 합니다 (관리 되는 인스턴스 상태는 **프로 비전**됨). 인스턴스 배포 요청 (새 인스턴스 만들기)을 취소 하면 관리 되는 인스턴스가 **프로 비전** 상태에서 **failedtocreate**로 이동 합니다.

만들지 못한 인스턴스는 여전히 리소스로 존재 하 고 다음을 수행 합니다. 

- 요금이 부과 되지 않음
- 리소스 제한 (서브넷 또는 vCore 할당량)을 계산 하지 않습니다.
- 인스턴스 이름을 예약 된 상태로 유지-동일한 이름의 인스턴스를 배포 하려면 실패 한 인스턴스를 삭제 하 여 이름을 해제 합니다.


> [!NOTE]
> 리소스 또는 관리 되는 인스턴스 목록에서 노이즈를 최소화 하려면 배포에 실패 한 인스턴스나 취소 된 배포가 포함 된 인스턴스를 삭제 합니다. 


## <a name="next-steps"></a>다음 단계

- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대한 자세한 내용은 [SQL Managed Instance VNet 구성](connectivity-architecture-overview.md)을 참조하세요.
- 백업 파일에서 관리형 인스턴스를 만들고 데이터베이스를 복원하는 방법에 대한 빠른 시작은 [관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.
- Azure Database Migration Service를 사용하여 마이그레이션하는 방법에 대한 자습서는 [Database Migration Service를 사용한 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조하세요.
