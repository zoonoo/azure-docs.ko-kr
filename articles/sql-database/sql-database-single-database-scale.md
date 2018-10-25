---
title: 단일 데이터베이스 리소스 크기 조정 - Azure SQL Database | Microsoft Docs
description: 이 문서에서는 Azure SQL Database에서 단일 데이터베이스에 사용할 수 있는 계산 및 저장소 리소스의 크기를 조정하는 방법을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 258f8fbe8d99923240db8d6d10c4cf812c939510
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466889"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Azure SQL Database에서 단일 데이터베이스 리소스 크기 조정

이 문서에서는 Azure SQL Database에서 단일 데이터베이스에 사용할 수 있는 계산 및 저장소 리소스의 크기를 조정하는 방법을 설명합니다.

## <a name="vcore-based-purchasing-model-change-storage-size"></a>vCore 기반 구매 모델: 저장소 크기 변경

- 저장소는 1GB 증분 단위로 최대 크기 제한까지 프로비전할 수 있습니다. 구성 가능한 최소 데이터 저장소는 5GB입니다.
- 단일 데이터베이스에 대한 저장소는 [Azure Portal](https://portal.azure.com), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 사용하여 해당 최대 크기를 늘리거나 줄여서 프로비전할 수 있습니다.
- SQL Database는 로그 파일에 대해 추가 저장소의 30% 및 TempDB에 대해 vCore당 32GB를 자동으로 할당하지만 384GB를 초과하지 않도록 합니다. TempDB는 모든 서비스 계층의 연결형 SSD에 있습니다.
- 단일 데이터베이스에 대한 저장소의 가격은 데이터 저장소 및 로그 저장소의 용량 합계에 해당 서비스 계층의 저장소 단가를 곱한 값입니다. TempDB의 비용은 vCore 가격에 포함됩니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="vcore-based-purchasing-model-change-compute-resources"></a>vCore 기반 구매 모델: 계산 리소스 변경

vCore 수를 처음 선택한 후에는 [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-server), [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 사용하여 실제 환경에 따라 단일 데이터베이스를 동적으로 늘리거나 줄일 수 있습니다.

서비스 계층 및/또는 데이터베이스의 계산 크기를 변경하게 되면 새 계산 크기에서 원본 데이터베이스의 복제본을 만든 다음, 복제본에 대한 연결을 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 전환 시간은 다양하지만, 일반적으로 30초보다 작은 4초 미만이 해당 시간의 99%를 차지하고 있습니다. 연결을 사용할 수 없는 짧은 시간에 많은 수의 항공편 트랜잭션이 있으면 전환 시간이 더 길어질 수 있습니다.

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 범용 서비스 계층으로, 이 계층에서 또는 이 계층 내에서 변경되는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 중요 비즈니스 계층 내에서 계산 크기를 변경하는 동일한 크기의 데이터베이스에 대한 강화는 3시간 내에 완료되어야 합니다.

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [SQL REST API를 사용하여 작업 관리](https://docs.microsoft.com/rest/api/sql/operations/list), [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL을 사용하여 작업 모니터링](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)과 두 가지 PowerShell 명령 즉, [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 및 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)를 참조하세요.

- 상위 서비스 계층이나 계산 크기로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한, 최대 데이터베이스 크기는 증가하지 않습니다.
- 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 계산 크기의 최대 허용 크기보다 작아야 합니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드할 때 주 데이터베이스를 먼저 다운그레이드해야 합니다.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

## <a name="dtu-based-purchasing-model-change-storage-size"></a>DTU 기반 구매 모델: 저장소 크기 변경

- 단일 데이터베이스에 대한 DTU 가격에는 특정 크기의 저장소가 추가 비용 없이 포함됩니다. 포함된 용량 외 추가 저장소는 최대 250GB씩 총 1TB이 최대 크기 제한까지 추가 비용을 내고 프로비전할 수 있고 1TB 이상일 경우 256GB씩 프로비전할 수 있습니다. 포함된 저장소 크기 및 최대 크기 제한에 대한 자세한 내용은 [단일 데이터베이스: 저장소 크기 및 계산 크기](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)를 참조하세요.
- 단일 데이터베이스에 대한 추가 저장소는 Azure Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 통해 해당하는 최대 크기를 늘려서 프로비전할 수 있습니다.
- 단일 데이터베이스에 대한 추가 저장소 가격은 추가 저장소 용량에 해당 서비스 계층의 추가 저장소 단가를 곱한 것입니다. 추가 저장소 가격에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요.

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="dtu-based-purchasing-model-change-compute-resources-dtus"></a>DTU 기반 구매 모델: 계산 리소스 변경(DTU)

처음에 서비스 계층, 계산 크기 및 저장소 용량을 선택한 후에 Azure Portal, [Transact-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#examples-1), [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase), [Azure CLI](/cli/azure/sql/db#az-sql-db-update) 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases/update)를 사용하여 실제 환경에 따라 단일 데이터베이스를 동적으로 확장 또는 축소할 수 있습니다.

다음 비디오에서는 서비스 계층 및 계산 크기를 동적으로 변경하여 단일 데이터베이스에 대해 사용 가능한 DTU를 늘리는 방법을 보여줍니다.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

서비스 계층 및/또는 데이터베이스의 계산 크기를 변경하게 되면 새 계산 크기에서 원본 데이터베이스의 복제본을 만든 다음, 복제본에 대한 연결을 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 전환 시간은 다양하지만, 99%는 30초 미만입니다. 연결을 사용할 수 없는 짧은 시간에 많은 수의 항공편 트랜잭션이 있으면 전환 시간이 더 길어질 수 있습니다.

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층 내에서 변경되고 있는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 프리미엄 서비스 계층 내에서 계산 크기를 변경하는 동일한 크기의 데이터베이스인 경우 3시간 내에 확장을 완료해야 합니다.

> [!TIP]
> 진행 중인 작업을 모니터링하려면 [SQL REST API를 사용하여 작업 관리](https://docs.microsoft.com/rest/api/sql/operations/list), [CLI를 사용하여 작업 관리](/cli/azure/sql/db/op), [T-SQL을 사용하여 작업 모니터링](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)과 두 가지 PowerShell 명령 즉, [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) 및 [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity)를 참조하세요.

- 상위 서비스 계층이나 계산 크기로 업그레이드하는 경우 더 큰 크기(최대 크기)를 명시적으로 지정하지 않는 한, 최대 데이터베이스 크기는 증가하지 않습니다.
- 데이터베이스를 다운그레이드하려면 데이터베이스 사용 공간이 대상 서비스 계층 및 계산 크기의 최대 허용 크기보다 작아야 합니다.
- **프리미엄**에서 **표준** 계층으로 다운그레이드하는 경우 (1) 데이터베이스의 최대 크기가 대상 계산 크기에서 지원되고 (2) 최대 크기가 대상 계산 크기의 포함된 저장소 용량을 초과하는 경우 추가 저장소 비용이 적용됩니다. 예를 들어 최대 크기가 500GB인 P1 데이터베이스 크기를 S3으로 줄이는 경우 S3이 최대 크기인 500GB를 지원하고 포함된 저장소 크기는 250GB에 불과하므로 추가 저장소 비용이 적용됩니다. 따라서 추가 저장소 크기는 500GB – 250GB = 250GB입니다. 추가 저장소 가격 책정에 대한 자세한 내용은 [SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 실제 사용된 공간의 크기가 포함된 저장소의 크기보다 작은 경우 데이터베이스 최대 크기를 포함된 크기로 줄여 이러한 추가 비용을 방지할 수 있습니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 업그레이드할 때 보조 데이터베이스를 먼저 업그레이드해야 합니다.
- [지역 복제](sql-database-geo-replication-portal.md)를 사용하도록 설정된 데이터베이스를 업그레이드하는 경우, 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 서비스 계층 및 계산 크기로 업그레이드합니다(최상의 성능을 위한 일반 지침). 다른 버전으로 다운그레이드할 때 주 데이터베이스를 먼저 다운그레이드해야 합니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. **기본** 계층으로 다운그레이드하는 경우 백업 보존 기간이 더 짧아집니다. [Azure SQL Database 백업](sql-database-automated-backups.md)을 참조하세요.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

## <a name="dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb"></a>DTU 기반 구매 모델: 최대 크기가 1TB보다 큰 경우 P11 및 P15의 제한 사항

오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 미국 중부, 프랑스 중부, 독일 중부, 일본 동부, 일본 서부, 한국 중부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 영국 남부, 영국 서부, 미국 동부 2, 미국 서부, 미국 버지니아 주 정부 및 유럽 서부에서는 P11 및 P15 데이터베이스에 대해 1TB를 초과하는 최대 크기가 지원됩니다. 다음 고려 사항 및 제한 사항은 최대 크기가 1TB보다 큰 P11 및 P15 데이터베이스에 적용됩니다.

- (4TB 또는 4096GB의 값을 사용하여) 데이터베이스를 만들 때 1TB보다 큰 최대 크기를 선택하면 지원되지 않는 지역에서 데이터베이스를 프로비전할 때 오류가 발생하여 만들기 명령이 실패합니다.
- 지원되는 하위 지역 중 하나에 있는 기존 P11 및 P15 데이터베이스의 경우 1TB보다 크게 최대 저장소를 늘리려면 256GB씩 최대 4TB로 늘릴 수 있습니다. 해당 지역에서 더 큰 크기를 사용할 수 있는지 확인하려면 [DATABASEPROPERTYEX](/sql/t-sql/functions/databasepropertyex-transact-sql) 함수를 사용하거나 Azure Portal에서 데이터베이스 크기를 검사합니다. 기존 P11 또는 P15 데이터베이스 업그레이드는 서버 수준 보안 주체 로그인 또는 dbmanager 데이터베이스 역할의 멤버에 의해서만 수행될 수 있습니다.
- 지원되는 지역에서 업그레이드 작업을 실행하면 구성이 즉시 업데이트됩니다. 업그레이드 프로세스 동안 데이터베이스는 온라인 상태로 유지됩니다. 그러나 실제 데이터베이스 파일이 새 최대 크기로 업그레이드될 때까지 1TB보다 큰 전체 저장소 크기를 사용할 수 없습니다. 필요한 시간의 길이는 업그레이드 중인 데이터베이스 크기에 따라 달라집니다.
- P11 또는 P15 데이터베이스를 만들거나 업데이트하는 경우 256GB씩 1TB까지 늘릴지, 4TB까지 늘릴지 중에서만 선택할 수 있습니다. P11/P15를 만들 때 1TB의 기본 저장소 옵션이 미리 선택됩니다. 지원되는 지역 중 하나에 있는 데이터베이스의 경우 새 또는 기존 단일 데이터베이스의 저장소를 최대 4TB 크기로 늘릴 수 있습니다. 다른 모든 지역의 경우 최대 크기를 1TB 이상으로 늘릴 수 없습니다. 4TB의 포함된 저장소를 선택하면 가격이 변경되지 않습니다.
- 데이터베이스의 최대 크기를 1TB보다 크게 설정하면 사용되는 실제 저장소가 1TB 미만인 경우에도 1TB로 변경할 수 없습니다. 따라서 최대 크기가 1TB보다 큰 P11 또는 P15를 1TB P11 또는 1TB P15 이하의 계산 크기(예: P1-P6)로 다운그레이드할 수 없습니다. 이 제한 사항은 지정 시간, 지역 복원, 장기 백업 보존 및 데이터베이스 복사를 포함한 복원 및 복사 시나리오에도 적용됩니다. 데이터베이스가 1TB보다 큰 최대 크기로 구성되면 이 데이터베이스의 모든 복원 작업은 최대 크기가 1TB보다 큰 P11/P15로 실행되어야 합니다.
- 활성 지역 복제 시나리오의 경우:
  - 지역에서 복제 관계 설정: 주 데이터베이스가 P11 또는 P15인 경우 보조 데이터베이스는 P11 또는 P15이어야 합니다. 낮은 계산 크기는 1TB 초과를 지원하지 않으므로 보조 데이터베이스로 거부됩니다.
  - 지역에서 복제 관계에서 주 데이터베이스 업그레이드: 주 데이터베이스에서 최대 크기를 1TB보다 크게 변경하면 보조 데이터베이스에서도 동일한 변경 사항이 트리거됩니다. 변경 내용을 적용하려면 주 데이터베이스에서 두 가지 업그레이드에 성공해야 합니다. 1TB보다 큰 옵션에 대한 지역 제한 사항이 적용됩니다. 1TB보다 큰 크기를 지원하지 않는 지역에 보조 데이터베이스가 있는 경우 주 데이터베이스는 업그레이드되지 않습니다.
- 1TB보다 큰 크기의 P11/P15 데이터베이스 로드에 대한 Import/Export 서비스 사용은 지원되지 않습니다. SqlPackage.exe를 사용하여 데이터를 [가져오기](sql-database-import.md) 및 [내보내기](sql-database-export.md)합니다.

## <a name="next-steps"></a>다음 단계

전반적인 리소스 제한은 [SQL Database vCore 기반 리소스 제한 - 단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [SQL Database DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-single-databases.md)을 참조하세요.
