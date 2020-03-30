---
title: 백업에서 데이터베이스 복원
description: Azure SQL 데이터베이스를 최대 35일까지 롤백할 수 있는 시점 복원에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 09/26/2019
ms.openlocfilehash: b98331a9cdb359aeefac5db1546f3a15b54010ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268744"
---
# <a name="recover-an-azure-sql-database-by-using-automated-database-backups"></a>자동화된 데이터베이스 백업을 사용하여 Azure SQL 데이터베이스 복구

기본적으로 Azure SQL Database 백업은 지리적으로 복제된 Blob 저장소(RA-GRS 저장소 유형)에 저장됩니다. 자동화된 데이터베이스 백업을 사용하여 데이터베이스 복구에 다음 옵션을 사용할 수 [있습니다.](sql-database-automated-backups.md) 다음을 수행할 수 있습니다.

- 보존 기간 내에 지정된 시점으로 복구된 동일한 SQL Database 서버에 새 데이터베이스를 만듭니다.
- 삭제된 데이터베이스의 삭제 시간으로 복구된 동일한 SQL Database 서버에서 데이터베이스를 만듭니다.
- 가장 최근 백업 지점까지 복구된 동일한 지역의 모든 SQL Database 서버에 새 데이터베이스를 만듭니다.
- 가장 최근에 복제된 백업 지점까지 복구된 다른 지역의 모든 SQL Database 서버에 새 데이터베이스를 만듭니다.

백업 장기 [보존을](sql-database-long-term-retention.md)구성한 경우 모든 SQL Database 서버에서 장기 보존 백업에서 새 데이터베이스를 만들 수도 있습니다.

> [!IMPORTANT]
> 복원 하는 동안 기존 데이터베이스를 덮어쓸 수 없습니다.

표준 또는 프리미엄 서비스 계층을 사용하는 경우 데이터베이스 복원시 추가 저장소 비용이 발생할 수 있습니다. 복원된 데이터베이스의 최대 크기가 대상 데이터베이스의 서비스 계층 및 성능 수준에 포함된 저장소 양보다 클 경우 추가 비용이 발생합니다. 추가 스토리지에 대한 가격 책정 정보는 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요. 실제 사용된 공간의 양이 포함된 저장소 양보다 작으면 최대 데이터베이스 크기를 포함된 양으로 설정하여 이 추가 비용을 방지할 수 있습니다.

## <a name="recovery-time"></a>복구 시간

자동화된 데이터베이스 백업을 사용하여 데이터베이스를 복원하는 복구 시간은 다음과 같은 여러 가지 요인의 영향을 받습니다.

- 데이터베이스의 크기입니다.
- 데이터베이스의 계산 크기입니다.
- 관련된 트랜잭션 로그 수입니다.
- 복원 지점으로 복구하기 위해 재생해야 하는 활동 양입니다.
- 복원이 다른 지역에 있는 경우 네트워크 대역폭입니다.
- 대상 지역에서 처리되는 동시 복원 요청의 수

대규모 또는 매우 활성 데이터베이스의 경우 복원에 몇 시간이 걸릴 수 있습니다. 지역에 장기간 중단이 있는 경우 재해 복구를 위해 많은 수의 지역 복원 요청이 시작될 수 있습니다. 요청이 많은 경우 개별 데이터베이스의 복구 시간이 늘어일 수 있습니다. 대부분의 데이터베이스는 12시간 내에 완전히 복원됩니다.

단일 구독의 경우 동시 복원 요청 수에 제한이 있습니다. 이러한 제한은 기간 중 복원, 지역 복원 및 장기 보존 백업에서 복원의 조합에 적용됩니다.

| | **처리되는 최대 동시 요청 수** | **제출되는 최대 동시 요청 수** |
| :--- | --: | --: |
|단일 데이터베이스(구독당)|10|60|
|탄력적 풀(풀당)|4|200|
||||

전체 서버를 복원하는 기본 제공 메서드는 없습니다. 이 작업을 수행하는 방법에 대한 예는 [Azure SQL 데이터베이스: 전체 서버 복구](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)를 참조하십시오.

> [!IMPORTANT]
> 자동화된 백업을 사용하여 복구하려면 구독에서 SQL Server 기여자 역할의 구성원이거나 구독 소유자여야 합니다. 자세한 내용은 [RBAC: 기본 제공 역할을](../role-based-access-control/built-in-roles.md)참조하십시오. Azure 포털, PowerShell 또는 REST API를 사용하여 복구할 수 있습니다. 거래-SQL을 사용할 수 없습니다.

## <a name="point-in-time-restore"></a>지정 시간 복원

Azure 포털, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)또는 [REST API를](https://docs.microsoft.com/rest/api/sql/databases)사용하여 독립 실행형, 풀로 된 또는 인스턴스 데이터베이스를 이전 시점으로 복원할 수 있습니다. 요청은 복원된 데이터베이스에 대한 서비스 계층 또는 계산 크기를 지정할 수 있습니다. 데이터베이스를 복원하는 서버에 충분한 리소스가 있는지 확인합니다. 완료되면 복원은 원래 데이터베이스와 동일한 서버에 새 데이터베이스를 만듭니다. 복원된 데이터베이스는 서비스 계층 및 계산 크기에 따라 정상 요금으로 요금이 부과됩니다. 데이터베이스 복원이 완료될 때까지 요금이 발생하지 않습니다.

일반적으로 복구를 위해 이전 지점까지 데이터베이스를 복원합니다. 복원된 데이터베이스를 원래 데이터베이스의 대체 데이터베이스로 처리하거나 원본 데이터베이스를 업데이트하는 데이터 원본으로 사용할 수 있습니다.

- **데이터베이스 교체**

  복원된 데이터베이스를 원래 데이터베이스의 대체 데이터베이스로 지정하려면 원래 데이터베이스의 계산 크기 및 서비스 계층을 지정해야 합니다. 그런 다음 원래 데이터베이스의 이름을 바꾸고 T-SQL에서 [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) 명령을 사용하여 복원된 데이터베이스에 원래 이름을 지정할 수 있습니다.

- **데이터 복구**

  복원된 데이터베이스에서 데이터를 검색하여 사용자 또는 응용 프로그램 오류에서 복구하려는 경우 복원된 데이터베이스에서 데이터를 추출하고 원래 데이터베이스에 적용하는 데이터 복구 스크립트를 작성하고 실행해야 합니다. 복원 작업을 완료하는 데 긴 시간이 걸리지만 복원 중인 데이터베이스가 복원 과정 내내 데이터베이스 목록에 표시됩니다. 복원 중에 데이터베이스를 삭제하면 복원 작업이 취소되고 복원을 완료하지 않은 데이터베이스에 대한 요금이 청구되지 않습니다.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure 포털을 사용하여 시간 별 복원

단일 SQL 데이터베이스 또는 인스턴스 데이터베이스를 Azure 포털에서 복원하려는 데이터베이스의 개요 블레이드에서 한 시점으로 복구할 수 있습니다.

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

Azure 포털을 사용하여 단일 또는 풀린 데이터베이스를 한 시점으로 복구하려면 데이터베이스 개요 페이지를 열고 도구 모음에서 **복원을** 선택합니다. 백업 원본을 선택하고 새 데이터베이스를 만들 수 있는 시점 백업 지점을 선택합니다. 

  ![데이터베이스 복원 옵션의 스크린샷](./media/sql-database-recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

Azure 포털을 사용하여 관리되는 인스턴스 데이터베이스를 한 시점으로 복구하려면 데이터베이스 개요 페이지를 열고 도구 모음에서 **복원을** 선택합니다. 새 데이터베이스를 만들 수 있는 시점 백업 지점을 선택합니다. 

  ![데이터베이스 복원 옵션의 스크린샷](./media/sql-database-recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 백업에서 데이터베이스를 프로그래밍 방식으로 복원하려면 [자동화된 백업을 사용하여 프로그래밍 방식으로 복구를 수행합니다.](sql-database-recovery-using-backups.md)

## <a name="deleted-database-restore"></a>삭제된 데이터베이스 복원

삭제된 데이터베이스를 동일한 SQL Database 서버 또는 동일한 관리되는 인스턴스에서 삭제 시간 또는 이전 시점으로 복원할 수 있습니다. Azure 포털, [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase)또는 [REST(createMode=복원)를](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)통해 이 작업을 수행할 수 있습니다. 백업에서 새 데이터베이스를 만들어 삭제된 데이터베이스를 복원합니다.

> [!IMPORTANT]
> Azure SQL Database 서버 또는 관리되는 인스턴스를 삭제하면 모든 데이터베이스도 삭제되고 복구할 수 없습니다. 삭제된 서버 또는 관리되는 인스턴스는 복원할 수 없습니다.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Azure 포털을 사용하여 데이터베이스 복원이 삭제되었습니다.

서버 및 인스턴스 리소스에서 Azure 포털에서 삭제된 데이터베이스를 복원합니다.

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

Azure 포털을 사용하여 삭제 된 단일 또는 풀린 된 데이터베이스를 삭제 하는 시간을 복구 하려면 서버 개요 페이지를 열고 삭제 된 데이터베이스를 선택 **합니다.** 복원할 삭제된 데이터베이스를 선택하고 백업에서 복원된 데이터로 만들 새 데이터베이스의 이름을 입력합니다.

  ![삭제된 Azure SQL 데이터베이스 복원 스크린샷](./media/sql-database-recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

Azure 포털을 사용하여 관리되는 데이터베이스를 복구하려면 관리되는 인스턴스 개요 페이지를 열고 **삭제된 데이터베이스를 선택합니다.** 복원할 삭제된 데이터베이스를 선택하고 백업에서 복원된 데이터로 만들 새 데이터베이스의 이름을 입력합니다.

  ![삭제된 Azure SQL 인스턴스 데이터베이스 복원 스크린샷](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>PowerShell을 사용하여 데이터베이스 복원 삭제

다음 샘플 스크립트를 사용하여 PowerShell을 사용하여 Azure SQL Database 및 관리되는 인스턴스에 대해 삭제된 데이터베이스를 복원합니다.

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

삭제된 Azure SQL 데이터베이스를 복원하는 방법을 보여 주려는 샘플 PowerShell 스크립트의 경우 PowerShell 을 [사용하여 SQL 데이터베이스 복원을](scripts/sql-database-restore-database-powershell.md)참조하십시오.

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

삭제된 인스턴스 데이터베이스를 복원하는 방법을 보여 주려는 샘플 PowerShell 스크립트의 경우 [PowerShell을 사용하여 관리되는 인스턴스에서 삭제된 데이터베이스 복원을](sql-database-managed-instance-point-in-time-restore.md#restore-a-deleted-database) 참조하십시오.

> [!TIP]
> 삭제된 데이터베이스를 프로그래밍 방식으로 복원하려면 [자동화된 백업을 사용하여 프로그래밍 방식으로 복구를 수행합니다.](sql-database-recovery-using-backups.md)

## <a name="geo-restore"></a>지리적 복원

가장 최근의 지역 복제 백업에서 Azure 지역에 있는 서버의 SQL 데이터베이스를 복원할 수 있습니다. 지리적 복원은 지리적 으로 복제된 백업을 소스로 사용합니다. 중단으로 인해 데이터베이스 또는 데이터 센터에 액세스할 수 없는 경우에도 지리적 복원을 요청할 수 있습니다.

지리적 복원은 호스팅 지역의 인시던트 때문에 데이터베이스를 사용할 수 없는 경우 기본 복구 옵션입니다. 다른 지역의 서버에 데이터베이스를 복원할 수 있습니다. 백업을 만들 때와 다른 지역에 있는 Azure Blob으로 지역 복제하는 사이에 지연이 있습니다. 따라서 복원된 데이터베이스는 원래 데이터베이스보다 최대 1시간 뒤에 있을 수 있습니다. 다음 그림에서는 다른 지역에서 마지막으로 사용 가능한 백업에서 데이터베이스 복원을 보여 주실 수 있습니다.

![지리적 복원 그래픽](./media/sql-database-geo-restore/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure 포털을 사용하여 지리적 복원

Azure 포털에서 새 단일 또는 관리되는 인스턴스 데이터베이스를 만들고 사용 가능한 지리적 복원 백업을 선택합니다. 새로 만든 데이터베이스에는 지리적으로 복원된 백업 데이터가 포함되어 있습니다.

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

선택한 지역의 Azure 포털과 서버에서 단일 SQL 데이터베이스를 지리적으로 복원하려면 다음 단계를 따르십시오.

1. **대시보드에서****SQL 데이터베이스 만들기** **추가를** > 선택합니다. 기본 탭에서 필요한 정보를 **입력합니다.**
2. **추가 설정**을 선택합니다.
3. **기존 데이터 사용에서는** **백업**을 선택합니다.
4. **백업의**경우 사용 가능한 지리적 복원 백업 목록에서 백업을 선택합니다.

    ![SQL 데이터베이스 만들기 옵션의 스크린샷](./media/sql-database-recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

백업에서 새 데이터베이스를 만드는 프로세스를 완료합니다. 단일 Azure SQL 데이터베이스를 만들 때 복원된 지리적 복원 백업이 포함됩니다.

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

Azure 포털에서 선택한 리전의 기존 관리되는 인스턴스로 관리되는 인스턴스 데이터베이스를 지리적으로 복원하려면 데이터베이스를 복원할 관리되는 인스턴스를 선택합니다. 다음 단계를 수행하세요.

1. **새 데이터베이스를**선택합니다.
2. 원하는 데이터베이스 이름을 입력합니다.
3. **기존 데이터 사용에서** **백업**을 선택합니다.
4. 사용 가능한 지리적 복원 백업 목록에서 백업을 선택합니다.

    ![새 데이터베이스 옵션의 스크린샷](./media/sql-database-recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

새 데이터베이스를 만드는 프로세스를 완료합니다. 인스턴스 데이터베이스를 만들 때 복원된 지역 복원 백업이 포함됩니다.

### <a name="geo-restore-by-using-powershell"></a>PowerShell을 사용하여 지역 복원

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

단일 SQL 데이터베이스에 대한 지리적 복원을 수행하는 방법을 보여 주는 PowerShell 스크립트의 경우 [PowerShell 사용을 참조하여 Azure SQL 단일 데이터베이스를 이전 시점으로 복원합니다.](scripts/sql-database-restore-database-powershell.md)

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

관리되는 인스턴스 데이터베이스에 대한 지리적 복원을 수행하는 방법을 보여 주는 PowerShell 스크립트의 경우 [PowerShell 사용을 참조하여 관리되는 인스턴스 데이터베이스를 다른 지리적 영역으로 복원합니다.](scripts/sql-managed-instance-restore-geo-backup.md)

### <a name="geo-restore-considerations"></a>지리적 복원 고려 사항

지역 보조 데이터베이스에서는 시간 별 복원을 수행할 수 없습니다. 기본 데이터베이스에서만 수행할 수 있습니다. 중단에서 복구하기 위해 지역 복원을 사용하는 것에 대한 자세한 내용은 [가동 중단으로부터 복구를](sql-database-disaster-recovery.md)참조하십시오.

> [!IMPORTANT]
> 지리적 복원은 SQL Database에서 사용할 수 있는 가장 기본적인 재해 복구 솔루션입니다. RPO(복구 지점 목표)가 1시간, 예상 복구 시간이 최대 12시간으로 자동으로 생성된 지리 복제 백업에 의존합니다. 급격한 수요 증가가 발생할 가능성이 있으므로 대상 지역이 지역 가동 중단 후 데이터베이스를 복원할 수 있는 용량을 가질 수 있다는 보장은 없습니다. 응용 프로그램이 비교적 작은 데이터베이스를 사용하고 비즈니스에 중요하지 않은 경우 지역 복원이 적절한 재해 복구 솔루션입니다. 대규모 데이터베이스가 필요하고 비즈니스 연속성을 보장해야 하는 비즈니스 크리티컬 애플리케이션의 경우 [자동 장애 조치 그룹을](sql-database-auto-failover-group.md)사용합니다. 훨씬 낮은 RPO 및 복구 시간 목표를 제공하며 용량은 항상 보장됩니다. 비즈니스 연속성 선택에 대한 자세한 내용은 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.

## <a name="programmatically-performing-recovery-by-using-automated-backups"></a>자동화된 백업을 사용하여 프로그래밍 방식으로 복구 수행

복구를 위해 Azure PowerShell 또는 REST API를 사용할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure 리소스 관리자 모듈은 Azure SQL Database에서 계속 지원되지만 향후 모든 개발은 Az.Sql 모듈용입니다. 이러한 cmdlet에 대 한 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조 합니다. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 매우 동일합니다.

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

독립 실행형 또는 풀로 풀된 데이터베이스를 복원하려면 [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)를 참조하십시오.

  | Cmdlet | 설명 |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 복원할 수 있는 삭제된 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |데이터베이스의 지역 중복 백업을 가져옵니다. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |SQL 데이터베이스를 복원합니다. |

  > [!TIP]
  > 데이터베이스의 시점 복원을 수행하는 방법을 보여 주는 샘플 PowerShell 스크립트의 경우 [PowerShell을 사용하여 SQL 데이터베이스 복원을](scripts/sql-database-restore-database-powershell.md)참조하십시오.

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

관리되는 인스턴스 데이터베이스를 복원하려면 [복원-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)를 참조하십시오.

  | Cmdlet | 설명 |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |하나 이상의 관리되는 인스턴스를 가져옵니다. |
  | [받기-아즈Sql인스턴스데이터베이스](/powershell/module/az.sql/get-azsqlinstancedatabase) | 인스턴스 데이터베이스를 가져옵니다. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |인스턴스 데이터베이스를 복원합니다. |

### <a name="rest-api"></a>REST API

REST API를 사용하여 단일 또는 풀풀 데이터베이스를 복원하려면 다음을 수행합니다.

| API | 설명 |
| --- | --- |
| [REST(createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |데이터베이스를 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](https://docs.microsoft.com/rest/api/sql/operations) |복원 작업 중에 상태를 반환합니다. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="single-azure-sql-database"></a>단일 Azure SQL 데이터베이스

Azure CLI를 사용하여 단일 또는 풀링된 데이터베이스를 복원하려면 [az sql db 복원을](/cli/azure/sql/db#az-sql-db-restore)참조하십시오.

#### <a name="managed-instance-database"></a>관리되는 인스턴스 데이터베이스

Azure CLI를 사용하여 관리되는 인스턴스 데이터베이스를 복원하려면 [az sql midb 복원을](/cli/azure/sql/midb#az-sql-midb-restore)참조하십시오.

## <a name="summary"></a>요약

자동 백업은 사용자 및 애플리케이션 오류, 우발적인 데이터베이스 삭제 및 장시간의 가동 중단에서 데이터베이스를 보호합니다. 이 기본 제공 기능은 모든 서비스 계층 및 컴퓨팅 크기에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL Database 자동화된 백업](sql-database-automated-backups.md)
- [장기 보존](sql-database-long-term-retention.md)
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](sql-database-active-geo-replication.md) 또는 [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md)을 참조하세요.
