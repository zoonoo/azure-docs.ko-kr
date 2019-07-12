---
title: 백업에서 Azure SQL 데이터베이스 복원 | Microsoft Docs
description: Azure SQL Database를 이전 시점(최대 35일)에 롤백할 수 있는 특정 시점 복원에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 80d01a360a2f80749bd7fbe7a9aadb9dda1189c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706985"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>자동화된 데이터베이스 백업을 사용하여 Azure SQL 데이터베이스 복구

기본적으로 SQL Database 백업은 지역 복제 Blob Storage(RA-GRS)에 저장됩니다. 다음 옵션은 [자동화된 데이터베이스 백업](sql-database-automated-backups.md)을 통한 데이터베이스 복구에 사용할 수 있습니다.

- 동일한 SQL Database 서버에 보존 기간 내의 지정된 시점으로 복구된 새 데이터베이스를 만듭니다.
- 동일한 SQL Database 서버에 삭제된 데이터베이스의 삭제 시간으로 복구된 데이터베이스를 만듭니다.
- 동일한 지역의 임의 SQL Database 서버에 최신 백업 지점으로 복구된 새 데이터베이스를 만듭니다.
- 다른 지역의 임의 SQL Database 서버에 최신 복제 백업 지점으로 복구된 새 데이터베이스를 만듭니다.

구성한 경우 [장기 보존 백업](sql-database-long-term-retention.md)을 모든 SQL Database 서버에서 LTR 백업에서 새 데이터베이스를 만들 수도 있습니다.

> [!IMPORTANT]
> 복원하는 동안 기존 데이터베이스를 덮어쓸 수는 없습니다.

표준 또는 프리미엄 서비스 계층을 사용하는 경우 다음 조건에 따라 복원된 데이터베이스에서 추가 스토리지 비용이 발생합니다.

- 데이터베이스 최대 크기가 500GB보다 큰 경우 P11–P15에서 S4-S12 또는 P1–P6로의 복원
- 데이터베이스 최대 크기가 250GB보다 큰 경우 P1–P6에서 S4-S12로의 복원

추가 비용은 icurred 복원된 된 데이터베이스의 최대 크기가 대상 데이터베이스의 서비스 계층 및 성능 수준에 포함 된 저장소의 크기 보다 큰 경우. 포함된 된 용량 이상으로 프로 비전 하는 추가 저장소는 추가 요금이 청구 됩니다. 추가 저장소에 대한 가격 책정 정보는 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요. 실제 사용 되는 공간의 크기가 포함 된 저장소의 크기 보다 적은 경우 이러한 포함된 된 용량을 최대 데이터베이스 크기를 설정 하 여 추가 비용을 방지할 수 있습니다.

> [!NOTE]
> [데이터베이스 사본](sql-database-copy.md)을 만들 때 [자동화된 데이터베이스 백업](sql-database-automated-backups.md)이 사용됩니다.

## <a name="recovery-time"></a>복구 시간

자동화된 데이터베이스 백업을 사용하여 데이터베이스를 복원하기 위한 복구 시간은 다음과 같은 다양한 요인의 영향을 받습니다.

- 데이터베이스의 크기
- 데이터베이스의 컴퓨팅 크기
- 관련된 트랜잭션 로그의 수
- 복원 지점으로 복구하기 위해 재생해야 하는 작업의 양
- 다른 지역으로 복원되는 경우의 네트워크 대역폭
- 대상 지역에서 처리되는 동시 복원 요청 개수

큰 및/또는 매우 활동적인 데이터베이스에 대 한 복원에는 몇 시간이 걸릴 수 있습니다. 지역에서 장시간 가동 중단된 경우 다른 지역에서 많은 수의 지리적 복원 요청이 처리 중일 수 있습니다. 많은 요청이 있는 경우 해당 지역에 데이터베이스의 복구 시간이 늘어날 수 있습니다. 대부분의 데이터베이스는 12시간 내에 완전히 복원됩니다.

단일 구독에 대 한 동시 복원 요청의 수에 제한이 있습니다.  이러한 제한이 적용 시간 복원, 지역 복원 및 복원 지점의 조합으로 장기 보존 백업에서):

| | **처리되는 최대 동시 요청 수** | **제출되는 최대 동시 요청 수** |
| :--- | --: | --: |
|단일 데이터베이스(구독당)|10|60|
|탄력적 풀(풀당)|4|200|
||||

현재 전체 서버를 복원 하기 위한 기본 메서드가 없습니다. [Azure SQL Database: 전체 서버 복구](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) 스크립트는이 작업을 수행할 수는 방법의 예입니다.

> [!IMPORTANT]
> 자동화된 백업을 사용하여 복구하려면 구독에서 SQL Server 참여자 역할의 구성원이거나 구독 소유자여야 합니다. [RBAC: 기본 제공 역할](../role-based-access-control/built-in-roles.md)을 참조하세요. Azure 포털, PowerShell 또는 REST API를 사용하여 복구할 수 있습니다. Transact-SQL은 사용할 수 없습니다.

## <a name="point-in-time-restore"></a>지정 시간 복원

풀링 하는 독립 실행형으로 복원 하거나 데이터베이스를 이전 시점으로 Azure portal을 사용 하 여 인스턴스 수 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), 또는 [REST API](https://docs.microsoft.com/rest/api/sql/databases)합니다. 요청 모든 서비스 계층을 지정 하거나 복원된 된 데이터베이스의 크기를 계산할 수 있습니다. 서버에 데이터베이스를 복원하기에 충분한 리소스가 있는지 확인합니다. 완료 되 면 새 데이터베이스를 원본 데이터베이스와 동일한 서버에 만들어집니다. 복원된 된 데이터베이스 서비스 계층 및 계산 크기에 따라 정상 요금이 부과 됩니다. 데이터베이스 복원이 완료될 때까지 요금이 발생하지 않습니다.

일반적으로 복구를 위해 이전 지점까지 데이터베이스를 복원합니다. 원본 데이터베이스에 대 한 대체로 복원된 된 데이터베이스를 처리할 수도 있고 원래 데이터베이스를 업데이트 원본 데이터로 사용할 수 있습니다.

- **데이터베이스 교체**

  원본 데이터베이스에 대 한 대체로 복원된 된 데이터베이스는 사용 하는 경우 원본 데이터베이스의 계산 크기 및 서비스 계층을 지정 해야 합니다. 다음 원래 데이터베이스 이름을 변경 하 고 사용 하 여 원래 이름이 복원된 된 데이터베이스를 부여할 수 있습니다 합니다 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL 명령을 합니다.

- **데이터 복구**

  사용자 또는 응용 프로그램 오류 로부터 복구 하려면 복원된 된 데이터베이스에서 데이터를 검색 하려는 경우 작성 하 고 복원된 된 데이터베이스에서 데이터를 추출 하 여 원본 데이터베이스에 적용 하는 데이터 복구 스크립트를 실행 해야 합니다. 복원 작업을 완료하는 데 긴 시간이 걸리지만 복원 중인 데이터베이스가 복원 과정 내내 데이터베이스 목록에 표시됩니다. 복원 중 데이터베이스를 삭제 하는 경우 복원 작업이 취소 되 고 하면 요금이 청구 되지 않습니다 복원이 완료 되지 않은 데이터베이스에 대 한 합니다.

Azure Portal을 사용하여 단일 데이터베이스, 풀링된 데이터베이스 또는 인스턴스 데이터베이스를 특정 시점으로 복구하려면 데이터베이스에 대한 페이지를 열고 도구 모음에서 **복원**을 클릭합니다.

![point-in-time-restore](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> 프로그래밍 방식으로 백업에서 데이터베이스를 복원하려면 [자동화된 백업을 사용하여 프로그래밍 방식으로 복구 수행](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)을 참조하세요.

## <a name="deleted-database-restore"></a>삭제된 데이터베이스 복원

Azure portal을 사용 하 여 동일한 SQL Database 서버에 시간에 삭제 시간 또는 이전 시점에 삭제 된 데이터베이스를 복원할 수 있습니다 [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), 또는 [REST (createMode = Restore)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)합니다. [PowerShell을 사용하여 Managed Instance에서 삭제된 데이터베이스를 복원](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance)할 수 있습니다. 

> [!TIP]
> 삭제된 데이터베이스를 복원하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [PowerShell을 사용하여 SQL 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
> [!IMPORTANT]
> Azure SQL Database 서버 인스턴스를 삭제하면 모든 해당 데이터베이스도 삭제되고 복구할 수 없습니다. 현재는 삭제된 서버 복원에 대한 지원이 제공되지 않습니다.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Azure Portal을 사용하여 삭제된 데이터베이스 복원

Azure portal을 사용 하 여 삭제 된 데이터베이스를 복구 하려면 서버에 대 한 작업 영역에서 페이지를 열고, 클릭 **삭제 된 데이터베이스**합니다.

![삭제된 데이터베이스 복원-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![삭제된 데이터베이스 복원-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> 삭제된 데이터베이스를 프로그래밍 방식으로 복원하려면 [자동화된 백업을 사용하여 프로그래밍 방식으로 복구 수행](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)을 참조하세요.

## <a name="geo-restore"></a>지역 복원

가장 최근의 지역 복제 백업에서 Azure 지역에 있는 서버의 SQL 데이터베이스를 복원할 수 있습니다. 지역에서 복제 된 백업을 해당 원본으로 사용 하는 지역 복원 합니다. 데이터베이스 또는 데이터 센터 가동 중단으로 인해 액세스할 수 없는 경우에 요청할 수 있습니다.

지리적 복원은 호스팅 지역에 사고가 발생 데이터베이스를 사용할 수 없는 경우 기본 복구 옵션입니다. 다른 지역의 서버로 데이터베이스를 복원할 수 있습니다. 백업을 만들 때와 다른 지역에 있는 Azure Blob으로 지역 복제하는 사이에 지연이 있습니다. 결과적으로, 복원된 된 데이터베이스는 최대 1 시간이 고 원래 데이터베이스 보다 뒤 수 있습니다. 다음 그림에서는 다른 지역에서 마지막으로 사용할 수 있는 백업에서 데이터베이스의 복원을 보여 줍니다.

![geo-restore](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> 지역 복원을 수행하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [PowerShell을 사용하여 SQL 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.

지역 보조 복제본에 대한 지정 시간 복원은 현재 지원되지 않습니다. 주 데이터베이스에서만 지정 시간 복원을 수행할 수 있습니다. 지역 복원 기능을 사용하여 가동 중단에서 복구하는 방법에 대한 자세한 내용은 [가동 중단에서 복구](sql-database-disaster-recovery.md)를 참조하세요.

> [!IMPORTANT]
> 지리적 복원은 SQL Database에서 사용할 수 있는 가장 기본적인 재해 복구 솔루션입니다. RPO를 사용 하 여 백업을 지역 복제를 자동으로 생성에 의존 하므로 = 1 시간 및 12 시간 최대 예상된 복구 시간입니다. 대상 지역 용량 수요의 증가 선명 하 게 될 가능성이 있으므로 국가별 ourage 후 데이터베이스를 복원 하려면 있는지는 보장 하지 않습니다. 비 비즈니스 중요 한 응용 프로그램의 상대적으로 작은 데이터베이스를 사용 하는 경우 지리적 복원은 적절 한 재해 복구 솔루션입니다. 비즈니스 중요 한 응용 프로그램의 많은 데이터베이스를 사용 하 고 비즈니스 연속성을 보장 해야 하는 경우 사용할지 [자동 장애 조치 그룹](sql-database-auto-failover-group.md)합니다. 용량 항상 및를 훨씬 더 낮은 RPO와 RTO를 제공 합니다. 비즈니스 연속성 선택에 대한 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

### <a name="geo-restore-using-the-azure-portal"></a>Azure Portal을 사용한 지역 복원

Azure Portal을 사용하여 [DTU 기반 모델 보존 기간](sql-database-service-tiers-dtu.md) 또는 [vCore 기반 모델 보존 기간](sql-database-service-tiers-vcore.md) 중에 데이터베이스를 지역 복원하려면 SQL Databases 페이지를 연 다음, **추가**를 클릭합니다. **Select source**(소스 선택) 텍스트 상자에서 **Backup**을 선택합니다. 선택한 서버 또는 지역에서 복구를 수행할 백업을 지정합니다.

> [!Note]
> 지역에서 복원은 Azure portal을 사용 하 여 관리 되는 인스턴스에서 사용할 수 없는 경우 PowerShell을 대신 사용 하십시오.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>자동화된 백업을 사용하여 프로그래밍 방식으로 복구 수행

앞서 설명한 것처럼 Azure Portal 외에, Azure PowerShell 또는 REST API를 사용하여 데이터베이스 복구를 프로그래밍 방식으로 수행할 수 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

- 독립 실행형 또는 풀링된 데이터베이스를 복원 하려면 참조 [복원 AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)합니다.

  | Cmdlet | 설명 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 복원할 수 있는 삭제된 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |데이터베이스의 지역 중복 백업을 가져옵니다. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |SQL 데이터베이스를 복원합니다. |

  > [!TIP]
  > 데이터베이스에 대한 특정 시점 복원을 수행하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [PowerShell을 사용하여 SQL 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.

- 관리 되는 인스턴스로 데이터베이스를 복원 하려면 참조 [복원 AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)합니다.

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |하나 이상의 관리 되는 인스턴스를 가져옵니다. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 데이터베이스를 인스턴스를 가져옵니다. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |인스턴스에 데이터베이스를 복원합니다. |

### <a name="rest-api"></a>REST API

REST API를 사용하여 단일 또는 풀링된 데이터베이스를 복원하려면:

| API | Description |
| --- | --- |
| [REST(createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |데이터베이스를 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](https://docs.microsoft.com/rest/api/sql/operations) |복원 작업 동안 상태를 반환합니다. |

### <a name="azure-cli"></a>Azure CLI

- Azure CLI를 사용하여 단일 또는 풀링된 데이터베이스를 복원하려면 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)를 참조하세요.
- Azure CLI를 사용 하 여 관리 되는 인스턴스를 복원 하려면 참조 [az sql midb에서 복원](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>요약

자동 백업은 사용자 및 애플리케이션 오류, 우발적인 데이터베이스 삭제 및 장시간의 가동 중단에서 데이터베이스를 보호합니다. 이 기본 제공 기능은 모든 서비스 계층 및 컴퓨팅 크기에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 비즈니스 연속성의 개요 및 시나리오를 보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure SQL Database 자동화 백업에 대한 자세한 내용은 [SQL Database 자동화 백업](sql-database-automated-backups.md)을 참조하세요.
- 장기 보존에 대해 알아보려면 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-active-geo-replication.md) 또는 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 참조하세요.
