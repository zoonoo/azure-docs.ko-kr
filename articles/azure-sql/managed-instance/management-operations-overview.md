---
title: 관리 작업
titleSuffix: Azure SQL Managed Instance
description: Azure SQL Managed Instance 관리 작업 기간 및 모범 사례에 대해 알아봅니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531487"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Azure SQL Managed Instance 관리 작업 개요
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>관리 작업 이란?
Azure SQL Managed Instance은 새로운 관리 되는 인스턴스를 자동으로 배포 하 고, 인스턴스 속성을 업데이트 하 고, 더 이상 필요 하지 않은 경우 인스턴스를 삭제 하는 데 사용할 수 있는 관리 작업을

[Azure virtual network 내에서 배포](../../virtual-network/virtual-network-for-azure-services.md) 를 지원 하 고 고객에 대 한 격리 및 보안을 제공 하기 위해 SQL Managed Instance는 [가상 클러스터](connectivity-architecture-overview.md#high-level-connectivity-architecture)에 의존 합니다. 가상 클러스터는 고객의 가상 네트워크 서브넷 내에 배포 된 격리 된 가상 머신의 전용 집합을 나타냅니다. 기본적으로 빈 서브넷에 있는 모든 관리 되는 인스턴스 배포는 새 가상 클러스터가 buildout을 생성 합니다.

배포 된 관리 되는 인스턴스의 후속 작업은 기본 가상 클러스터에도 영향을 미칠 수 있습니다. 이러한 작업은 관리 작업 기간에 영향을 줍니다. 추가 가상 컴퓨터를 배포 하는 경우 새 배포 또는 기존 관리 되는 인스턴스에 대 한 업데이트를 계획 하는 경우 고려해 야 하는 오버 헤드가 발생 합니다.

모든 관리 작업은 다음과 같이 분류될 수 있습니다.

- 인스턴스 배포 (새 인스턴스 생성).
- 인스턴스 업데이트 (vCores 또는 예약 된 저장소와 같은 인스턴스 속성 변경)
- 인스턴스를 삭제 합니다.

## <a name="management-operations-duration"></a>관리 작업 기간
일반적으로 가상 클러스터에 대 한 작업은 가장 긴 시간이 걸립니다. 가상 클러스터에 대 한 작업 기간-다음은 기존 서비스 원격 분석 데이터에 따라 일반적으로 예측할 수 있는 값입니다.

- **가상 클러스터 만들기**: 생성은 인스턴스 관리 작업의 동기 단계입니다. **작업의 90%가 4 시간 내에 완료**되었습니다.
- **가상 클러스터 크기 조정 (확장 또는 축소)**: 확장은 동기식 단계 이지만 축소는 비동기적으로 수행 됩니다 (인스턴스 관리 작업 기간에 영향을 주지 않음). **클러스터 확장의 90%가 2.5 시간 이내에 완료**되었습니다.
- **가상 클러스터 삭제**: 삭제는 비동기 단계 이지만, 빈 가상 클러스터에서 [수동으로 시작할](virtual-cluster-delete.md) 수도 있습니다 .이 경우에는 동기적으로 실행 됩니다. **가상 클러스터 삭제의 90%가 1.5 시간 내에 완료**되었습니다.

또한 인스턴스 관리에는 호스트 된 데이터베이스에 대 한 작업 중 하나가 포함 될 수 있으며,이로 인해 기간이 길어집니다.

- **Azure Storage에서 데이터베이스 파일 연결**: 계산 (vcores) 또는 범용 서비스 계층의 저장소 확장/축소와 같은 동기 단계입니다. **이러한 작업의 90%는 5 분 안에 완료**됩니다.
- **Always On 가용성 그룹 시드**: 계산 (vcores), 중요 비즈니스용 서비스 계층의 저장소 크기 조정 등의 동기 단계 뿐만 아니라 서비스 계층을 일반적인 용도에서 중요 비즈니스용 (또는 그 반대로)로 변경 합니다. 이 작업의 기간은 총 데이터베이스 크기 및 현재 데이터베이스 작업 (활성 트랜잭션 수)에 비례 합니다. 데이터베이스 작업 인스턴스를 업데이트할 때 전체 기간에 상당한 차이를 일으킬 수 있습니다. **이러한 작업의 90%는 220 g b/시간 이상에서 실행**됩니다.

다음 표에는 작업과 일반적인 전체 기간이 요약 되어 있습니다.

|범주  |작업(Operation)  |장기 실행 세그먼트  |예상 소요 시간  |
|---------|---------|---------|---------|
|**배포** |빈 서브넷의 첫 번째 인스턴스|가상 클러스터 만들기|작업의 90%가 4 시간 내에 완료 되었습니다.|
|배포 |비어 있지 않은 서브넷에 있는 다른 하드웨어 생성의 첫 번째 인스턴스 (예: Gen 4 인스턴스가 있는 서브넷의 첫 번째 Gen 5 인스턴스)|가상 클러스터 만들기 *|작업의 90%가 4 시간 내에 완료 되었습니다.|
|배포 |비어 있거나 비어 있지 않은 서브넷에 4 개의 vCores를 만든 첫 번째 인스턴스|가상 클러스터 만들기 * *|작업의 90%가 4 시간 내에 완료 되었습니다.|
|배포 |비어 있지 않은 서브넷 (두 번째, 세 번째 등) 내에서 후속 인스턴스 만들기|가상 클러스터 크기 조정|90%의 작업이 2.5 시간 내에 완료 되었습니다.|
|**Update** |인스턴스 속성 변경 (관리자 암호, Azure AD 로그인, Azure 하이브리드 혜택 플래그)|해당 없음|최대 1 분입니다.|
|업데이트 |인스턴스 저장소 확장/축소 (범용 서비스 계층)|데이터베이스 파일 연결|90%의 작업은 5 분 안에 완료 됩니다.|
|업데이트 |인스턴스 저장소 확장/축소 (중요 비즈니스용 서비스 계층)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|90%의 작업 완료 (2.5 시간) + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (범용)|-가상 클러스터 크기 조정<br>-데이터베이스 파일 연결|90%의 작업이 2.5 시간 내에 완료 되었습니다.|
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (중요 비즈니스용)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|90%의 작업 완료 (2.5 시간) + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|업데이트 |인스턴스 서비스 계층 변경 (일반적인 중요 비즈니스용 용도 및 그 반대로)|-가상 클러스터 크기 조정<br>-Always On 가용성 그룹 시드|90%의 작업 완료 (2.5 시간) + 모든 데이터베이스를 시드 하는 시간 (220 g b/시간)|
|**삭제할지**|인스턴스 삭제|모든 데이터베이스에 대 한 비상 백업 로그|90% 작업이 최대 1 분 내에 완료 되었습니다.<br>참고: 서브넷의 마지막 인스턴스가 삭제 된 경우이 작업은 12 시간 후에 가상 클러스터 삭제를 예약 합니다. * *|
|삭제|가상 클러스터 삭제 (사용자 시작 작업)|가상 클러스터 삭제|90%의 작업이 최대 1.5 시간 내에 완료 되었습니다.|

\*가상 클러스터는 하드웨어 생성 별로 빌드됩니다.

\*\*12 시간은 현재 구성 이지만 나중에 변경 될 수 있으므로이에 대 한 하드 종속성을 주지 않습니다. 이전에 가상 클러스터를 삭제 해야 하는 경우 (예: 서브넷을 해제 하려면) [관리 되는 인스턴스를 삭제 한 후에 서브넷 삭제](virtual-cluster-delete.md)를 참조 하세요.

## <a name="instance-availability-during-management-operations"></a>관리 작업 중 인스턴스 가용성

업데이트를 마칠 때 발생 하는 장애 조치 (failover)로 인 한 짧은 가동 중지 시간을 제외 하 고는 **업데이트 작업 동안 SQL Managed Instance를 사용할 수**있습니다. 가속화 된 장기 실행 트랜잭션이 중단 되는 경우에도 [가속화 된 데이터베이스 복구](../accelerated-database-recovery.md)덕분에 일반적으로 최대 10 초 정도 지속 됩니다.

> [!IMPORTANT]
> Azure SQL Managed Instance의 계산 또는 저장소를 확장 하거나 장기 실행 트랜잭션 (데이터 가져오기, 데이터 처리 작업, 인덱스 다시 작성 등)을 사용 하 여 서비스 계층을 동시에 변경 하는 것은 권장 되지 않습니다. 작업이 끝날 때 수행 되는 데이터베이스 장애 조치 (failover)는 진행 중인 모든 트랜잭션을 취소 합니다.

SQL Managed Instance는 배포 및 삭제 작업 중에 클라이언트 응용 프로그램에서 사용할 수 없습니다.

## <a name="management-operations-cross-impact"></a>관리 작업 간 영향

관리 되는 인스턴스의 관리 작업은 동일한 가상 클러스터 내에 배치 된 인스턴스의 다른 관리 작업에 영향을 줄 수 있습니다.

- 가상 클러스터의 **장기 실행 복원 작업** 은 동일한 서브넷에서 다른 인스턴스 만들기 또는 크기 조정 작업을 유지 합니다.<br/>**예:** 장기 실행 복원 작업이 있고 동일한 서브넷에 만들기 또는 크기 조정 요청이 있는 경우 계속 하기 전에 복원 작업이 완료 될 때까지 대기 하므로이 요청을 완료 하는 데 시간이 더 오래 걸립니다.
    
- **이후 인스턴스 만들기 또는 크기** 조정 작업은 이전에 시작 된 인스턴스 만들기 또는 가상 클러스터 크기 조정을 시작한 인스턴스 배율에 의해 보류 중으로 설정 됩니다.<br/>**예:** 동일한 가상 클러스터에 있는 동일한 서브넷에 여러 개의 만들기 및/또는 크기 조정 요청이 있는 경우 그 중 하나는 가상 클러스터 크기 조정을 시작 하 고, 가상 클러스터 크기를 조정 하는 데 필요한 모든 요청은 다시 시작 하기 전에 크기 조정 작업이 완료 될 때까지 대기 해야 하므로 가상 클러스터 크기를 조정 하는 데 필요한 모든 요청은 예상 보다 오래 지속 됩니다.

- **5 분 창에서 제출 된 만들기/크기 조정 작업** 은 일괄 처리 되 고 병렬로 실행 됩니다.<br/>**예:** 5 분 내에 제출 된 모든 작업에 대해 하나의 가상 클러스터 크기 조정이 수행 됩니다 (첫 번째 작업 요청을 실행 하는 순간부터 측정). 첫 번째 요청을 제출한 후 5 분 넘게 다른 요청을 제출 하는 경우 실행이 시작 되기 전에 가상 클러스터 크기 조정이 완료 될 때까지 기다립니다.

> [!IMPORTANT]
> 진행 중인 다른 작업으로 인해 유지 되는 관리 작업은 계속 되는 조건이 충족 되 면 자동으로 다시 시작 됩니다. 일시 중지 된 관리 작업을 다시 시작 하는 데 필요한 사용자 작업은 없습니다.

## <a name="canceling-management-operations"></a>관리 작업 취소

다음 표에는 특정 관리 작업을 취소 하는 기능과 일반적인 전체 기간이 요약 되어 있습니다.

범주  |작업(Operation)  |취소  |예상 취소 기간  |
|---------|---------|---------|---------|
|배포 |인스턴스 만들기 |아니요 |  |
|업데이트 |인스턴스 저장소 확장/축소 (범용) |아니요 |  |
|업데이트 |인스턴스 저장소 확장/축소 (중요 비즈니스용) |예 |90%의 작업은 5 분 안에 완료 됩니다. |
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (범용) |예 |90%의 작업은 5 분 안에 완료 됩니다. |
|업데이트 |인스턴스 계산 (vCores) 확장 및 축소 (중요 비즈니스용) |예 |90%의 작업은 5 분 안에 완료 됩니다. |
|업데이트 |인스턴스 서비스 계층 변경 (일반적인 중요 비즈니스용 용도 및 그 반대로) |예 |90%의 작업은 5 분 안에 완료 됩니다. |
|DELETE |인스턴스 삭제 |아니요 |  |
|DELETE |가상 클러스터 삭제 (사용자 시작 작업) |아니요 |  |

# <a name="portal"></a>[포털](#tab/azure-portal)

관리 작업을 취소 하려면 개요 블레이드로 이동 하 고 진행 중인 작업의 알림 상자를 클릭 합니다. 오른쪽에서 진행 중인 작업을 포함 하는 화면이 표시 되 고 작업 취소 단추가 표시 됩니다. 첫 번째 클릭 후 다시 클릭 하 라는 메시지가 표시 되 고 작업 취소를 확인 합니다.

[![작업 취소](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

취소 요청을 제출 하 고 처리 한 후에는 제출 취소에 성공 하거나 실패 한 경우 알림을 받게 됩니다.

제출 된 취소 요청이 성공적으로 완료 되 면 몇 분 후에 관리 작업이 취소 되 고 오류가 발생 합니다.

![작업 결과 취소](./media/management-operations-overview/canceling-operation-result.png)

취소 요청이 실패 하거나 취소 단추가 활성화 되지 않은 경우 관리 작업에서 취소할 수 없음 상태가 되 고 몇 분 안에 완료 됩니다. 관리 작업은 완료 될 때까지 실행을 계속 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell 아직 설치 하지 않은 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조 하세요.

관리 작업을 취소 하려면 관리 작업 이름을 지정 해야 합니다. 따라서 먼저 get 명령을 사용 하 여 작업 목록을 검색 한 다음 특정 작업을 취소 합니다.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

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

Azure CLI 아직 설치 되지 않은 경우 [Azure CLI 설치](/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조 하세요.

관리 작업을 취소 하려면 관리 작업 이름을 지정 해야 합니다. 따라서 먼저 get 명령을 사용 하 여 작업 목록을 검색 한 다음 특정 작업을 취소 합니다.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

자세한 명령 설명은 [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest)를 참조 하세요.

---

## <a name="next-steps"></a>다음 단계
- 첫 번째 관리 되는 인스턴스를 만드는 방법을 알아보려면 [빠른 시작 가이드](instance-create-quickstart.md)를 참조 하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](../database/features-comparison.md)을 참조하세요.
- VNet 구성에 대 한 자세한 내용은 [SQL Managed Instance vnet 구성](connectivity-architecture-overview.md)을 참조 하세요.
- 관리 되는 인스턴스를 만들고 백업 파일에서 데이터베이스를 복원 하는 빠른 시작은 [관리 되는 인스턴스 만들기](instance-create-quickstart.md)를 참조 하세요.
- 마이그레이션을 위해 Azure Database Migration Service를 사용 하는 방법에 대 한 자습서는 [Database Migration Service를 사용 하 여 SQL Managed Instance 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md)을 참조 하세요.
