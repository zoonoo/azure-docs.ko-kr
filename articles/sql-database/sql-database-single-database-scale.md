---
title: 단일 데이터베이스 리소스 크기 조정
description: 이 문서에서는 Azure SQL Database에서 단일 데이터베이스에 사용할 수 있는 컴퓨팅 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 84846e642fa102045b89eb12dbc85b0995867a3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061591"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정

이 문서에서는 프로비저닝된 계산 계층에서 Azure SQL Database에 사용할 수 있는 계산 및 저장소 리소스를 확장하는 방법을 설명합니다. 또는 [서버리스 계산 계층은](sql-database-serverless.md) 사용되는 계산에 대해 초당 계산 자동 크기 조정 및 청구를 제공합니다.

처음에 vCores 또는 DTUS 수를 선택한 후 [Azure 포털,](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)또는 [REST API를](https://docs.microsoft.com/rest/api/sql/databases/update)사용하여 실제 환경을 기반으로 단일 데이터베이스를 동적으로 확장하거나 축소할 수 있습니다.

다음 비디오에서는 서비스 계층 및 컴퓨팅 크기를 동적으로 변경하여 단일 데이터베이스에 대해 사용 가능한 DTU를 늘리는 방법을 보여줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 파일 공간 관리를](sql-database-file-space-management.md)참조하십시오.

## <a name="impact"></a>영향

주로 서비스 계층 또는 계산 크기를 변경하려면 다음 단계를 수행하는 서비스가 포함됩니다.

1. 데이터베이스에 대한 새 계산 인스턴스 만들기  

    요청된 서비스 계층 및 계산 크기로 새 계산 인스턴스가 만들어집니다. 서비스 계층 및 계산 크기 변경의 일부 조합의 경우 데이터 복사와 관련된 새 계산 인스턴스에서 데이터베이스 복제본을 만들어야 하며 전체 대기 시간에 큰 영향을 줄 수 있습니다. 이 단계에서는 데이터베이스가 온라인 상태로 유지되고 원래 계산 인스턴스의 데이터베이스로 연결이 계속 연결됩니다.

2. 연결 라우팅을 새 계산 인스턴스로 전환

    원래 계산 인스턴스의 데이터베이스에 대한 기존 연결이 삭제됩니다. 새 계산 인스턴스의 데이터베이스에 새 연결이 설정됩니다. 서비스 계층 및 계산 크기 변경의 일부 조합의 경우 스위치 중에 데이터베이스 파일이 분리되고 다시 연결됩니다.  그럼에도 불구하고 스위치는 일반적으로 30초 미만, 종종 몇 초 동안 데이터베이스를 사용할 수 없는 경우 일시적인 서비스 중단이 발생할 수 있습니다. 연결이 끊어질 때 실행 중인 트랜잭션이 오래 실행되는 경우 중단된 트랜잭션을 복구하는 데 이 단계의 기간이 더 오래 걸릴 수 있습니다. [데이터베이스 복구가속화를](sql-database-accelerated-database-recovery.md) 통해 장기 실행 트랜잭션을 중단하는 데 미치는 영향을 줄일 수 있습니다.

> [!IMPORTANT]
> 워크플로의 모든 단계에서 데이터가 손실되지 않습니다. 서비스 계층이 변경되는 동안 Azure SQL Database를 사용하는 응용 프로그램 및 구성 요소에서 일부 [재시도 논리를](sql-database-connectivity-issues.md) 구현했는지 확인합니다.

## <a name="latency"></a>대기 시간 

서비스 계층을 변경하거나 단일 데이터베이스 또는 탄력적 풀의 계산 크기를 다시 조정하는 예상 대기 시간은 다음과 같이 매개변수화됩니다.

|서비스 계층|기본 단일 데이터베이스,</br>표준 (S0-S1)|기본 탄성 풀,</br>표준 (S2-S12), </br>하이퍼스케일, </br>범용 단일 데이터베이스 또는 탄력적 풀|프리미엄 또는 비즈니스 크리티컬 단일 데이터베이스 또는 탄력적 풀|
|:---|:---|:---|:---|
|**기본 단일</br> 데이터베이스, 표준(S0-S1)**|&bull;&nbsp;사용 공간에 관계없이 일정한 시간 대기 시간</br>&bull;&nbsp;일반적으로 5분 미만|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|
|**기본 탄성 </br>풀, 표준(S2-S12), </br>하이퍼스케일, </br>범용 단일 데이터베이스 또는 탄성 풀**|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|&bull;&nbsp;사용 공간에 관계없이 일정한 시간 대기 시간</br>&bull;&nbsp;일반적으로 5분 미만|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|
|**프리미엄 또는 비즈니스 크리티컬 단일 데이터베이스 또는 탄력적 풀**|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|&bull;&nbsp;데이터 복사로 인해 사용되는 데이터베이스 공간에 비례하는 대기 시간</br>&bull;&nbsp;일반적으로 사용된 공간의 GB당 1분 미만|

> [!TIP]
> 진행 중인 작업을 모니터링하려면 다음을 참조하십시오: [SQL REST API를 사용하여 작업 관리,](https://docs.microsoft.com/rest/api/sql/operations/list) [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL및](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) 이 두 PowerShell 명령을 사용하여 작업을 모니터링합니다: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 및 [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>변경 취소

서비스 계층 변경 또는 계산 크기 조정 작업을 취소할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

데이터베이스 개요 블레이드에서 **알림으로** 이동하여 진행 중인 작업이 있음을 나타내는 타일을 클릭합니다.

![진행 중인 작업](media/sql-database-single-database-scale/ongoing-operations.png)

다음으로 **이 작업 취소라는**레이블이 붙은 단추를 클릭합니다.

![진행 중인 작업 취소](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

PowerShell 명령 프롬프트에서 `$resourceGroupName` `$serverName`을 `$databaseName`설정하고 다음 명령을 실행합니다.

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>기타 고려 사항

- 상위 서비스 계층이나 컴퓨팅 크기로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한 최대 데이터베이스 크기는 증가하지 않습니다.
- 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 컴퓨팅 크기의 최대 허용 크기보다 작아야 합니다.
- **프리미엄**에서 **표준** 계층으로 다운그레이드하는 경우 (1) 데이터베이스의 최대 크기가 대상 컴퓨팅 크기에서 지원되고 (2) 최대 크기가 대상 컴퓨팅 크기의 포함된 스토리지 용량을 초과하는 경우 추가 스토리지 비용이 적용됩니다. 예를 들어 최대 크기가 500GB인 P1 데이터베이스가 S3로 축소된 경우 S3가 최대 크기 1TB를 지원하고 포함된 저장소 용량은 250GB에 불과하므로 추가 저장소 비용이 적용됩니다. 따라서 추가 스토리지 용량은 500GB – 250GB = 250GB입니다. 추가 스토리지 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 스토리지 용량보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 컴퓨팅 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 컴퓨팅 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드할 때는 기본 데이터베이스를 먼저 다운그레이드해야 합니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. **기본** 계층으로 다운그레이드하는 경우 백업 보존 기간이 더 짧아집니다. [Azure SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

## <a name="billing"></a>결제 

사용량 또는 데이터베이스가 한 시간 미만 동안 활성 상태였는지 여부와 관계없이, 해당 시간에 적용된 최고 서비스 계층 + 컴퓨팅 크기를 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다.

## <a name="change-storage-size"></a>스토리지 크기 변경

### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

- 스토리지는 1GB 증분을 사용하여 데이터 저장소 최대 크기 제한까지 프로비전할 수 있습니다. 최소 구성 가능한 데이터 저장소는 1GB입니다. 단일 [데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [탄력적 풀에](sql-database-vcore-resource-limits-elastic-pools.md) 대한 리소스 제한 설명서 페이지를 참조하여 각 서비스 목표의 데이터 저장소 최대 크기 제한을 확인합니다.
- 단일 데이터베이스에 대한 데이터 저장소는 [Azure 포털,](https://portal.azure.com) [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)또는 REST [API를](https://docs.microsoft.com/rest/api/sql/databases/update)사용하여 최대 크기를 늘리거나 줄임으로써 프로비저닝할 수 있습니다. 최대 크기 값이 바이트로 지정된 경우 1GB(1073741824 바이트)의 배수여야 합니다.
- 데이터베이스의 데이터 파일에 저장할 수 있는 데이터의 양은 구성된 데이터 저장소 최대 크기에 의해 제한됩니다. SQL Database는 해당 저장소 외에도 트랜잭션 로그에 사용할 저장소를 30% 더 할당합니다.
- SQL Database는 데이터베이스에 vCore당 32GB를 `tempdb` 자동으로 할당합니다. `tempdb`은 모든 서비스 계층의 로컬 SSD 저장소에 있습니다.
- 단일 데이터베이스 또는 탄력적 풀의 저장소 가격은 데이터 저장소 및 트랜잭션 로그 저장소 금액에 서비스 계층의 저장소 단가를 곱한 값입니다. 비용은 `tempdb` 가격에 포함되어 있습니다. 저장소 가격에 대한 자세한 내용은 [SQL 데이터베이스 가격을](https://azure.microsoft.com/pricing/details/sql-database/)참조하십시오.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 파일 공간 관리를](sql-database-file-space-management.md)참조하십시오.

### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

- 단일 데이터베이스에 대한 DTU 가격에는 특정 크기의 스토리지가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 스토리지는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 스토리지 크기 및 최대 크기 제한에 대한 자세한 내용은 [단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)를 참조하세요.
- 단일 데이터베이스에 대한 추가 저장소는 Azure 포털, [Transact-SQL,](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az-sql-db-update)또는 REST [API를](https://docs.microsoft.com/rest/api/sql/databases/update)사용하여 최대 크기를 늘려 프로비저닝할 수 있습니다.
- 단일 데이터베이스에 대한 추가 스토리지 가격은 추가 스토리지 용량에 해당 서비스 계층의 추가 스토리지 단가를 곱한 것입니다. 추가 스토리지 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL 데이터베이스의 파일 공간 관리를](sql-database-file-space-management.md)참조하십시오.

### <a name="geo-replicated-database"></a>지리적 복제 데이터베이스

복제된 보조 데이터베이스의 데이터베이스 크기를 변경하려면 기본 데이터베이스의 크기를 변경합니다. 그런 다음 이 변경 사항은 보조 데이터베이스에서도 복제되고 구현됩니다. 

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>최대 크기가 1TB보다 큰 경우 P11 및 P15 제약 조건

프리미엄 계층의 1TB 이상의 스토리지는 현재 중국 동부, 중국 북부, 독일 중부, 독일 북동부, 미국 중서부, 미국 DoD 지역 및 미국 정부 중부를 제외한 모든 지역에서 사용할 수 있습니다. 이러한 지역에서 프리미엄 계층 스토리지 최대 크기는 1TB로 제한됩니다. 다음 고려 사항 및 제한 사항은 최대 크기가 1TB보다 큰 P11 및 P15 데이터베이스에 적용됩니다.

- P11 또는 P15 데이터베이스의 최대 크기가 1TB보다 큰 값으로 설정된 경우 P11 또는 P15 데이터베이스로만 복원하거나 복사할 수 있습니다.  이어서, 데이터베이스는 새 계산 크기의 최대 크기 제한을 초과하지 않는 재스케일링 작업 시 할당된 공간의 양을 제공하면 다른 계산 크기로 재조정될 수 있다.
- 활성 지역 복제 시나리오의 경우:
  - 지역에서 복제 관계 설정: 주 데이터베이스가 P11 또는 P15인 경우 보조 데이터베이스는 P11 또는 P15이어야 합니다. 낮은 컴퓨팅 크기는 1TB 초과를 지원하지 않으므로 보조 데이터베이스로 거부됩니다.
  - 지역에서 복제 관계에서 주 데이터베이스 업그레이드: 주 데이터베이스에서 최대 크기를 1TB보다 크게 변경하면 보조 데이터베이스에서도 동일한 변경 사항이 트리거됩니다. 변경 내용을 적용하려면 주 데이터베이스에서 두 가지 업그레이드에 성공해야 합니다. 1TB보다 큰 옵션에 대한 지역 제한 사항이 적용됩니다. 1TB보다 큰 크기를 지원하지 않는 지역에 보조 데이터베이스가 있는 경우 주 데이터베이스는 업그레이드되지 않습니다.
- 1TB보다 큰 크기의 P11/P15 데이터베이스 로드에 대한 Import/Export 서비스 사용은 지원되지 않습니다. SqlPackage.exe를 사용하여 데이터를 [가져오기](sql-database-import.md) 및 [내보내기](sql-database-export.md)합니다.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-single-databases.md)을 참조하세요.
