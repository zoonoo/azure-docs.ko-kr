---
title: 단일 데이터베이스 리소스 크기 조정 - Azure SQL Database | Microsoft Docs
description: 이 문서에서는 Azure SQL Database에서 단일 데이터베이스에 사용할 수 있는 계산 및 스토리지 리소스의 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 04/26/2019
ms.openlocfilehash: 1048b4e2ac3a8523d5539ddc1a1bdaca3ec2d912
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074252"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정

이 문서에서는 프로 비전 된 계산 계층에서 단일 데이터베이스에 대 한 사용 가능한 계산 및 저장소 리소스를 확장 하는 방법을 설명 합니다. 또는 합니다 [(미리 보기) 서버 리스 계산 계층](sql-database-serverless.md) 계산 자동 크기 조정 및 사용 된 계산에 대 한 초당 청구를 제공 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 Azure SQL 데이터베이스에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

## <a name="change-compute-size-vcores-or-dtus"></a>계산 크기를 변경 (Dtu 또는 vcore 수)

Dtu 또는 Vcore 수를 처음 선택한 후 있습니다 수 늘리거나 단일 데이터베이스를 사용 하 여 실제 환경에 따라 동적으로 [Azure portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server)를 [TRANSACT-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [ PowerShell](/powershell/module/az.sql/set-azsqldatabase)서 [Azure CLI](/cli/azure/sql/db#az-sql-db-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)합니다.

다음 비디오에서는 서비스 계층 및 컴퓨팅 크기를 동적으로 변경하여 단일 데이터베이스에 대해 사용 가능한 DTU를 늘리는 방법을 보여줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 계산 크기를 변경의 영향

서비스를 변경 하는 계층 또는 단일 데이터베이스의 크기에는 주로 다음 단계를 수행 하는 서비스가 포함 되어 계산:

1. 데이터베이스에 대 한 새 계산 인스턴스 만들기  

    요청 된 서비스 계층 및 계산 크기를 사용 하 여 데이터베이스에 대 한 새 계산 인스턴스가 만들어집니다. 조합에 대 한 몇 가지 서비스 계층 및 계산 크기 변경, 데이터베이스의 복제본 데이터를 복사 하는 새 계산 인스턴스에서 다시 만들어야 하 고 전체 대기 시간에 크게 영향을 미칠 수 있습니다. 그럼에도 불구 하 고 데이터베이스는이 단계 동안 온라인 상태로 유지 하 고 연결을 원래 계산 인스턴스에 있는 데이터베이스에 계속 합니다.

2. 새 계산 인스턴스에 대 한 연결의 라우팅을 전환합니다

    원래 계산 인스턴스의 데이터베이스에 기존 연결이 삭제 됩니다. 모든 새 연결은 새 계산 인스턴스에 있는 데이터베이스에 설정 됩니다. 조합에 대 한 몇 가지 서비스 계층 및 계산 크기 변경, 데이터베이스 파일이 분리 되어 전환 하는 동안 다시 연결 합니다.  하지만 일반적으로 30 초 보다 작은 한 대개 몇 초만에 대 한 데이터베이스를 사용할 수 없을 때 스위치 일시적인 서비스 중단 될 수 있습니다. 장기 실행 없으면 연결이 삭제 되 면 실행 트랜잭션의 경우이 단계 동안 중단 된 트랜잭션을 복구 하기 위해 오래 걸릴 수 있습니다. [데이터베이스 복구 accelerated](sql-database-accelerated-database-recovery.md) 장기 실행 트랜잭션 중단의 영향을 줄일 수 있습니다.

> [!IMPORTANT]
> 워크플로의 단계 중 데이터가 손실 됩니다.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>서비스 계층 또는 크기 조정 계산 크기를 변경 하는 대기 시간

서비스 계층을 변경 하거나 단일 데이터베이스 또는 탄력적 풀의 계산 크기 기간은 대기 시간은 다음과 같은 매개 변수화 됩니다.

|서비스 계층|기본 단일 데이터베이스</br>표준 (S0-S1)|기본 탄력적 풀</br>표준 (S2-S12) </br>하이퍼 스케일, </br>일반 목적 단일 데이터베이스 또는 탄력적 풀|단일 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀|
|:---|:---|:---|:---|
|**단일 기본 데이터베이스</br> 표준 (S0-S1)**|&bull; &nbsp;사용 된 공간 관계 없이 상수 시간 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 5 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|
|**기본 탄력적 풀 </br>표준 (S2-S12) </br>하이퍼 스케일, </br>범용 단일 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;사용 된 공간 관계 없이 상수 시간 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 5 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|
|**단일 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀**|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|&bull; &nbsp;데이터 복사로 인해 사용 가능한 데이터베이스 공간에 비례하여 대기 시간</br>&bull; &nbsp;일반적으로 보다 작은 사용 공간 GB 당 1 분|

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [Manage operations using the SQL REST API](https://docs.microsoft.com/rest/api/sql/operations/list)(SQL REST API를 사용하여 작업 관리), [Manage operations using CLI](/cli/azure/sql/db/op)(CLI를 사용하여 작업 관리), [Monitor operations using T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)(T-SQL을 사용하여 작업 관리) 및 다음 두 가지 PowerShell 명령, 즉 [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) 하 고 [중지 AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity)합니다.

### <a name="cancelling-service-tier-changes-or-compute-rescaling-operations"></a>서비스 계층 변경 또는 계산 작업의 크기를 조정할 취소

서비스 계층을 변경 하거나 계산 작업의 크기를 조정할 취소할 수 있습니다.

#### <a name="azure-portal"></a>Azure portal

데이터베이스 개요 블레이드로 이동 **알림을** 및 진행 중인 작업이 있는지를 나타내는 타일을 클릭 합니다.

![진행 중인 작업](media/sql-database-single-database-scale/ongoing-operations.png)

다음으로 레이블이 지정 된 단추를 클릭 **이 작업을 취소**합니다.

![진행 중인 작업 취소](media/sql-database-single-database-scale/cancel-ongoing-operation.png)

#### <a name="powershell"></a>PowerShell

PowerShell 명령 프롬프트에서 설정 된 `$ResourceGroupName`, `$ServerName`, 및 `$DatabaseName`를 넣은 후 다음 명령을 실행:

```PowerShell
$OperationName = (az sql db op list --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --query "[?state=='InProgress'].name" --out tsv)
if(-not [string]::IsNullOrEmpty($OperationName))
    {
        (az sql db op cancel --resource-group $ResourceGroupName --server $ServerName --database $DatabaseName --name $OperationName)
        "Operation " + $OperationName + " has been canceled"
    }
    else
    {
        "No service tier change or compute rescaling operation found"
    }
```

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>변경 시 추가 고려 사항 서비스 계층 또는 크기 조정 계산 크기

- 상위 서비스 계층이나 컴퓨팅 크기로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한 최대 데이터베이스 크기는 증가하지 않습니다.
- 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 컴퓨팅 크기의 최대 허용 크기보다 작아야 합니다.
- **프리미엄**에서 **표준** 계층으로 다운그레이드하는 경우 (1) 데이터베이스의 최대 크기가 대상 계산 크기에서 지원되고 (2) 최대 크기가 대상 계산 크기의 포함된 저장소 용량을 초과하는 경우 추가 저장소 비용이 적용됩니다. 예를 들어 최대 크기가 500GB인 P1 데이터베이스 크기를 S3으로 줄이는 경우 S3이 최대 크기인 500GB를 지원하고 포함된 스토리지 용량은 250GB에 불과하므로 추가 스토리지 비용이 적용됩니다. 따라서 추가 스토리지 용량은 500GB – 250GB = 250GB입니다. 추가 저장소 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 스토리지 용량보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드할 때 주 데이터베이스를 먼저 다운그레이드해야 합니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. **기본** 계층으로 다운그레이드하는 경우 백업 보존 기간이 더 짧아집니다. [Azure SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

### <a name="billing-during-compute-rescaling"></a>계산 크기 조정 중의 요금 청구

사용량 또는 데이터베이스가 한 시간 미만 동안 활성 상태였는지 여부와 관계없이, 해당 시간에 적용된 최고 서비스 계층 + 컴퓨팅 크기를 사용하여 데이터베이스가 있었던 각 시간에 대해 요금이 청구됩니다. 예를 들어 단일 데이터베이스를 만들고 5분 후 삭제하더라도 청구서에는 데이터베이스 1시간 사용에 대한 요금이 반영됩니다.

## <a name="change-storage-size"></a>스토리지 크기 변경

### <a name="vcore-based-purchasing-model"></a>vCore 기반 구매 모델

- 저장소는 1GB 증분 단위로 최대 크기 제한까지 프로비전할 수 있습니다. 구성 가능한 최소 데이터 저장소는 5GB입니다.
- 단일 데이터베이스에 대한 저장소는 [Azure Portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 사용하여 해당 최대 크기를 늘리거나 줄여서 프로비전할 수 있습니다.
- SQL Database는 로그 파일에 대해 추가 저장소의 30% 및 TempDB에 대해 vCore당 32GB를 자동으로 할당하지만 384GB를 초과하지 않도록 합니다. TempDB는 모든 서비스 계층의 연결형 SSD에 있습니다.
- 단일 데이터베이스에 대한 스토리지의 가격은 데이터 스토리지 및 로그 스토리지 용량 합계에 해당 서비스 계층의 스토리지 단가를 곱한 값입니다. TempDB의 비용은 vCore 가격에 포함됩니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

### <a name="dtu-based-purchasing-model"></a>DTU 기반 구매 모델

- 단일 데이터베이스에 대한 DTU 가격에는 특정 크기의 저장소가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 저장소는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 스토리지 용량 및 최대 크기 제한에 대한 자세한 내용은 [단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)를 참조하세요.
- 단일 데이터베이스에 대한 추가 저장소는 Azure Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/az.sql/set-azsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 통해 해당하는 최대 크기를 늘려서 프로비전할 수 있습니다.
- 단일 데이터베이스에 대한 추가 스토리지 가격은 추가 스토리지 용량에 해당 서비스 계층의 추가 스토리지 단가를 곱한 것입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>1TB 보다 큰 P11 및 P15 제약 조건 때 최대 크기

현재 다음 지역을 제외한 모든 지역에서 프리미엄 계층의 스토리지 1TB 이상을 사용할 수 있습니다. 중국 동부, 중국 북부, 독일 중부, 독일 북동부, 미국 중서부, 미국 DoD 지역 및 미국 중앙 정부 이러한 지역에서 프리미엄 계층 저장소 최대 크기는 1TB로 제한됩니다. 다음 고려 사항 및 제한 사항은 최대 크기가 1TB보다 큰 P11 및 P15 데이터베이스에 적용됩니다.

- P11 또는 P15 데이터베이스에 대 한 최대 크기를 1TB 보다 큰 어느 값으로 설정, 하는 경우 다음이 또는 가능 복원 P11 또는 P15 데이터베이스에 복사 합니다.  그런 다음 데이터베이스 재조정 중 작업 시 할당 된 공간의 양이 새 계산 크기의 최대 크기 제한을 초과 하지 제공 다른 계산 크기로 재조정 수 있습니다.
- 활성 지역 복제 시나리오의 경우:
  - 지역에서 복제 관계 설정: 주 데이터베이스가 P11 또는 P15인 경우 보조 데이터베이스는 P11 또는 P15이어야 합니다. 낮은 컴퓨팅 크기는 1TB 초과를 지원하지 않으므로 보조 데이터베이스로 거부됩니다.
  - 지역에서 복제 관계에서 주 데이터베이스 업그레이드: 주 데이터베이스에서 최대 크기를 1TB보다 크게 변경하면 보조 데이터베이스에서도 동일한 변경 사항이 트리거됩니다. 변경 내용을 적용하려면 주 데이터베이스에서 두 가지 업그레이드에 성공해야 합니다. 1TB보다 큰 옵션에 대한 지역 제한 사항이 적용됩니다. 1TB보다 큰 크기를 지원하지 않는 지역에 보조 데이터베이스가 있는 경우 주 데이터베이스는 업그레이드되지 않습니다.
- 1TB보다 큰 크기의 P11/P15 데이터베이스 로드에 대한 Import/Export 서비스 사용은 지원되지 않습니다. SqlPackage.exe를 사용하여 데이터를 [가져오기](sql-database-import.md) 및 [내보내기](sql-database-export.md)합니다.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-single-databases.md)을 참조하세요.
