---
title: 탄력적 풀 리소스 크기 조정
description: 이 페이지에서는 Azure SQL Database에서 탄력적 풀의 리소스 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 09/16/2020
ms.openlocfilehash: 947d842860452425f8b30fbdaf9558c2a94a89a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92781212"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Azure SQL Database에서 탄력적 풀 리소스 크기 조정
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

이 문서에서는 Azure SQL Database에서 탄력적 풀 및 풀링된 데이터베이스에 사용할 수 있는 컴퓨팅 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다.

## <a name="change-compute-resources-vcores-or-dtus"></a>컴퓨팅 리소스 변경(vCores or DTU)

vCore 또는 eDTU 수를 처음 선택한 후에는 [Azure portal](elastic-pool-manage.md#azure-portal), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](/rest/api/sql/elasticpools/update)를 사용하여 실제 환경에 따라 탄력적 풀을 동적으로 늘리거나 줄일 수 있습니다.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 컴퓨팅 크기 변경의 영향

탄력적 풀의 서비스 계층 또는 컴퓨팅 크기를 변경하는 것은 단일 데이터베이스의 경우와 유사한 패턴을 따르며 주로 다음 단계를 수행하는 서비스와 관련이 있습니다.

1. 탄력적 풀에 대한 새 컴퓨팅 인스턴스 만들기  

    요청된 서비스 계층 및 컴퓨팅 크기로 탄력적 풀에 대한 새 컴퓨팅 인스턴스를 만듭니다. 일부 서비스 계층 및 컴퓨팅 크기 조합 변경의 경우 새 컴퓨팅 인스턴스에 각 데이터베이스의 복제본을 만들어야 하며, 여기에는 데이터 복사가 수반되고 전체 대기 시간에 큰 영향을 미칠 수 있습니다. 그럼에도 이 단계 동안 데이터베이스는 온라인 상태로 유지되며, 원래 컴퓨팅 인스턴스의 데이터베이스에 대한 연결은 계속 전달됩니다.

2. 새 컴퓨팅 인스턴스로 연결 라우팅 전환

    원래 컴퓨팅 인스턴스의 데이터베이스에 대한 기존 연결이 삭제됩니다. 새 컴퓨팅 인스턴스의 데이터베이스에 대한 모든 새 연결이 설정됩니다. 일부 서비스 계층 및 컴퓨팅 크기 조합 변경의 경우 데이터베이스 파일은 전환 도중 분리된 후 다시 연결됩니다.  그럼에도 데이터베이스를 일반적으로 30초 미만 동안, 또 몇 초 동안 사용할 수 없을 때 전환으로 인해 잠깐의 서비스 중단이 발생할 수 있습니다. 연결이 끊어질 때 실행되는 장기 실행 트랜잭션이 있는 경우 중단된 트랜잭션을 복구하기 위해 이 단계가 오래 걸릴 수 있습니다. [가속 데이터베이스 복구](../accelerated-database-recovery.md)를 통해 장기 실행 트랜잭션의 중단으로 인한 영향을 줄일 수 있습니다.

> [!IMPORTANT]
> 워크플로의 단계 중에는 데이터가 손실되지 않습니다.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 컴퓨팅 크기 변경의 대기 시간

서비스 계층 변경, 단일 데이터베이스 또는 탄력적 풀의 컴퓨팅 크기 조정, 탄력적 풀에서 데이터베이스의 이동, 탄력적 풀 사이의 데이터베이스 이동에 따른 예상 대기 시간은 다음과 같이 매개 변수화됩니다.

|서비스 계층|기본 단일 데이터베이스,</br>표준(S0-S1)|기본 탄력적 풀,</br>표준(S2-S12), </br>범용 단일 데이터베이스 또는 탄력적 풀|프리미엄 또는 중요 비즈니스용 단일 데이터베이스 또는 탄력적 풀|하이퍼스케일
|:---|:---|:---|:---|:---|
|**기본 단일 데이터베이스,</br> 표준(S0-S1)**|&bull; &nbsp;사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 5분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**기본 탄력적 풀, </br>표준(S2-S12), </br>범용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;단일 데이터베이스의 경우, 사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 단일 데이터베이스의 경우 5분 미만</br>&bull; &nbsp;탄력적 풀의 경우 데이터베이스 수에 비례|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**프리미엄 또는 중요 비즈니스용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|&bull; &nbsp;데이터 복사로 인한 사용된 데이터베이스 공간의 비례 대기 시간</br>&bull; &nbsp;일반적으로 사용되는 공간(GB)당 1분 미만|
|**하이퍼스케일**|해당 없음|해당 없음|해당 없음|&bull; &nbsp;사용된 공간에 독립적인 일정한 대기 시간</br>&bull; &nbsp;일반적으로 2분 미만|

> [!NOTE]
>
> - 서비스 계층을 변경하거나 탄력적 풀에 대한 컴퓨팅 크기를 재조정하는 경우 풀의 모든 데이터베이스에서 사용된 공간의 합계를 사용하여 추정을 계산해야 합니다.
> - 탄력적 풀에서/풀로 데이터베이스를 이동하는 경우 탄력적 풀에서 사용하는 공간이 아니라 데이터베이스에 사용되는 공간만 대기 시간에 영향을 줍니다.
> - 표준 및 범용 탄력적 풀에서 또는 탄력적 풀 사이에 데이터베이스를 이동하는 데 따른 대기 시간은 프리미엄 파일 공유([PFS](../../storage/files/storage-files-introduction.md)) 스토리지 사용 시 데이터베이스 크기에 비례합니다. 풀이 PFS 스토리지를 사용하는지 여부를 확인하려면 풀에 있는 데이터베이스의 컨텍스트에서 다음 쿼리를 실행합니다. AccountType 열의 값이 `PremiumFileStorage` 또는 `PremiumFileStorage-ZRS`인 경우 풀에서 PFS 스토리지를 사용 중인 것입니다.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [SQL REST API를 사용하여 작업 관리](/rest/api/sql/operations/list), [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL을 사용하여 작업 모니터링](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)과 두 가지 PowerShell 명령, 즉 [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 및 [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)를 참조하세요.

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 컴퓨팅 크기 변경 시 추가 고려 사항

- 탄력적 풀에 대한 vCores 또는 eDTU 크기를 줄이는 경우 풀 사용 공간이 대상 서비스 계층 및 풀 eDTU에 허용되는 최대 크기보다 작아야 합니다.
- 탄력적 풀에 대한 eDTU 크기를 다시 조정하는 경우 (1) 풀의 최대 스토리지 크기가 대상 풀에 지원되고, (2) 최대 스토리지 크기가 대상 풀에 포함된 스토리지 용량을 초과하면 추가 스토리지 비용이 적용됩니다. 예를 들어 최대 크기가 100GB인 100 eDTU 표준 풀 크기를 50 eDTU 표준 풀로 줄이는 경우 대상 풀이 100GB의 최대 크기를 지원하고 포함된 스토리지 용량은 50GB에 불과하므로 추가 스토리지 비용이 적용됩니다. 따라서 추가 스토리지 용량은 100GB – 50GB = 50GB입니다. 추가 스토리지 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 스토리지 용량보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.

### <a name="billing-during-rescaling"></a>크기 조정 중 청구

사용량 또는 데이터베이스가 한 시간 미만 동안 활성 상태였는지 여부와 관계없이, 해당 시간에 적용된 최고 서비스 계층 + 컴퓨팅 크기를 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다.

## <a name="change-elastic-pool-storage-size"></a>탄력적 풀 스토리지 크기 변경

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

- 스토리지는 최대 크기 제한까지 프로비전할 수 있습니다.

  - 표준 또는 범용 서비스 계층의 스토리지 크기는 10GB 단위로 늘리거나 줄이세요.
  - 프리미엄 또는 중요 비즈니스용 서비스 계층의 스토리지 크기는 250GB 단위로 늘리거나 줄이세요.
- 최대 크기를 늘리거나 줄여서 탄력적 풀의 스토리지를 프로비전할 수 있습니다.
- 탄력적 풀에 대한 스토리지의 가격은 스토리지 용량에 해당 서비스 계층의 스토리지 단가를 곱한 값입니다. 추가 스토리지 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

- 탄력적 풀에 대한 eDTU 가격에는 특정 크기의 스토리지가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 스토리지는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 스토리지 용량 및 최대 크기 제한에 대한 자세한 내용은 [탄력적 풀: 스토리지 크기 및 컴퓨팅 크기](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)를 참조하세요.
- 탄력적 풀에 대한 추가 스토리지는 [Azure portal](elastic-pool-manage.md#azure-portal), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), [Azure CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) 또는 [REST API](/rest/api/sql/elasticpools/update)를 통해 해당 최대 크기를 늘려 프로비전할 수 있습니다.
- 탄력적 풀에 대한 추가 스토리지 가격은 추가 스토리지 용량에 해당 서비스 계층의 추가 스토리지 단가를 곱한 것입니다. 추가 스토리지 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](file-space-manage.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 탄력적 풀](resource-limits-vcore-elastic-pools.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](resource-limits-dtu-elastic-pools.md)을 참조하세요.