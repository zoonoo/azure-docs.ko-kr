---
title: 백업에서 데이터베이스 복원
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database의 데이터베이스나 Azure Managed Instance SQL의 인스턴스를 최대 35 일 동안 롤백할 수 있는 특정 시점 복원에 대해 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein, danil
ms.date: 11/13/2020
ms.openlocfilehash: 138cd03989008aa4fbd7d6ca3e06aff94fa76e33
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989465"
---
# <a name="recover-using-automated-database-backups---azure-sql-database--sql-managed-instance"></a>자동화 된 데이터베이스 백업을 사용 하 여 복구-SQL Managed Instance & Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[자동화 된 데이터베이스 백업을](automated-backups-overview.md)사용 하 여 데이터베이스 복구에 사용할 수 있는 옵션은 다음과 같습니다. 다음을 수행할 수 있습니다.

- 동일한 서버에 새 데이터베이스를 만들어 보존 기간 내의 지정 된 특정 시점으로 복구 합니다.
- 삭제 된 데이터베이스에 대 한 삭제 시간으로 복구 되는 동일한 서버에 데이터베이스를 만듭니다.
- 동일한 지역에 있는 모든 서버에서 새 데이터베이스를 만들어 가장 최근의 백업 지점으로 복구 합니다.
- 가장 최근에 복제 된 백업 지점으로 복구 되는 다른 모든 지역에 있는 모든 서버에서 새 데이터베이스를 만듭니다.

[백업 장기 보존](long-term-retention-overview.md)을 구성한 경우 모든 서버에 있는 장기 보존 백업에서 새 데이터베이스를 만들 수도 있습니다.

> [!IMPORTANT]
> 복원 하는 동안 기존 데이터베이스를 덮어쓸 수 없습니다.

Standard 또는 Premium 서비스 계층을 사용 하는 경우 데이터베이스 복원에 추가 저장소 비용이 발생할 수 있습니다. 복원 된 데이터베이스의 최대 크기가 대상 데이터베이스의 서비스 계층 및 성능 수준에 포함 된 저장소 용량 보다 큰 경우 추가 비용이 발생 합니다. 추가 스토리지에 대한 가격 책정 정보는 [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요. 실제 사용 된 공간의 양이 포함 된 저장소 용량 보다 작은 경우 최대 데이터베이스 크기를 포함 된 용량으로 설정 하 여 이러한 추가 비용을 방지할 수 있습니다.

## <a name="recovery-time"></a>복구 시간

자동화 된 데이터베이스 백업을 사용 하 여 데이터베이스를 복원 하는 복구 시간은 다음과 같은 여러 가지 요인의 영향을 받습니다.

- 데이터베이스의 크기입니다.
- 데이터베이스의 계산 크기입니다.
- 관련 된 트랜잭션 로그의 수입니다.
- 복원 지점으로 복구 하기 위해 재생 해야 하는 작업의 양입니다.
- 다른 지역으로 복원 하는 경우 네트워크 대역폭
- 대상 지역에서 처리되는 동시 복원 요청의 수

대규모 또는 매우 활성 데이터베이스의 경우 복원에는 몇 시간이 걸릴 수 있습니다. 지역에서 장시간 가동 중단이 발생 한 경우 재해 복구를 위해 많은 수의 지역 복원 요청이 시작 될 수 있습니다. 많은 요청이 있는 경우 개별 데이터베이스의 복구 시간이 늘어날 수 있습니다. 대부분의 데이터베이스 복원은 12 시간 이내에 완료 됩니다.

단일 구독의 경우 동시 복원 요청 수에 제한이 있습니다. 이러한 제한 사항은 특정 시점 복원, 지역 복원, 장기 보존 백업에서 복원 등의 모든 조합에 적용 됩니다.

| **배포 옵션** | **처리되는 최대 동시 요청 수** | **제출되는 최대 동시 요청 수** |
| :--- | --: | --: |
|**단일 데이터베이스(구독당)**|10|60|
|**탄력적 풀(풀당)**|4|200|


전체 서버를 복원 하는 기본 제공 메서드는 없습니다. 이 작업을 수행 하는 방법에 대 한 예제는 [Azure SQL Database: 전체 서버 복구](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666)를 참조 하세요.

> [!IMPORTANT]
> 자동화 된 백업을 사용 하 여 복구 하려면 구독에서 SQL Server 참여자 역할 또는 SQL Managed Instance 참가자 역할 (복구 대상에 따라 다름)의 멤버 이거나 구독 소유자 여야 합니다. 자세한 내용은 [AZURE RBAC: 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조 하세요. Azure Portal, PowerShell 또는 REST API를 사용 하 여 복구할 수 있습니다. Transact-sql은 사용할 수 없습니다.

## <a name="point-in-time-restore"></a>지정 시간 복원

Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)또는 [REST API](/rest/api/sql/databases/createorupdate#creates-a-database-from-pointintimerestore.)를 사용 하 여 독립 실행형, 풀링된 또는 인스턴스 데이터베이스를 이전 시점으로 복원할 수 있습니다. 요청은 복원 된 데이터베이스에 대 한 서비스 계층 또는 계산 크기를 지정할 수 있습니다. 데이터베이스를 복원할 서버에 충분 한 리소스가 있는지 확인 합니다. 

완료 되 면 복원 시 원본 데이터베이스와 동일한 서버에 새 데이터베이스가 만들어집니다. 복원 된 데이터베이스는 서비스 계층 및 계산 크기를 기준으로 정상 요금으로 청구 됩니다. 데이터베이스 복원이 완료 될 때까지 요금이 부과 되지 않습니다.

일반적으로 복구를 위해 이전 지점까지 데이터베이스를 복원합니다. 복원 된 데이터베이스를 원래 데이터베이스에 대 한 대체 항목으로 처리 하거나 원본 데이터베이스를 데이터 원본으로 사용 하 여 원본 데이터베이스를 업데이트할 수 있습니다.

- **데이터베이스 대체**

  복원 된 데이터베이스를 원래 데이터베이스에 대 한 대체로 만들려면 원래 데이터베이스의 계산 크기와 서비스 계층을 지정 해야 합니다. 그런 다음 T-sql에서 [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database) 명령을 사용 하 여 원래 데이터베이스의 이름을 바꾸고 복원 된 데이터베이스에 원래 이름을 지정할 수 있습니다.

- **데이터 복구**

  복원 된 데이터베이스에서 데이터를 검색 하 여 사용자 또는 응용 프로그램 오류 로부터 복구 하려면 복원 된 데이터베이스에서 데이터를 추출 하 여 원본 데이터베이스에 적용 하는 데이터 복구 스크립트를 작성 하 고 실행 해야 합니다. 복원 작업을 완료하는 데 긴 시간이 걸리지만 복원 중인 데이터베이스가 복원 과정 내내 데이터베이스 목록에 표시됩니다. 복원 하는 동안 데이터베이스를 삭제 하면 복원 작업이 취소 되 고 복원이 완료 되지 않은 데이터베이스에 대해서는 요금이 청구 되지 않습니다.
  
### <a name="point-in-time-restore-by-using-azure-portal"></a>Azure Portal를 사용 하 여 지정 시간 복원

Azure Portal 복원 하려는 데이터베이스의 개요 블레이드에서 단일 또는 인스턴스 데이터베이스를 특정 시점으로 복구할 수 있습니다.

#### <a name="sql-database"></a>SQL Database

Azure Portal를 사용 하 여 특정 시점으로 데이터베이스를 복구 하려면 데이터베이스 개요 페이지를 열고 도구 모음에서 **복원** 을 선택 합니다. 백업 원본을 선택 하 고 새 데이터베이스를 만들 지정 시간 백업 지점을 선택 합니다.

  ![SQL Database에 대 한 데이터베이스 복원 옵션의 스크린샷](./media/recovery-using-backups/pitr-backup-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure Portal를 사용 하 여 관리 되는 인스턴스 데이터베이스를 특정 시점으로 복구 하려면 데이터베이스 개요 페이지를 열고 도구 모음에서 **복원** 을 선택 합니다. 새 데이터베이스를 만들 지정 시간 백업 지점을 선택 합니다.

  ![SQL 관리 되는 인스턴스에 대 한 데이터베이스 복원 옵션의 스크린샷](./media/recovery-using-backups/pitr-backup-managed-instance-annotated.png)

> [!TIP]
> 백업에서 데이터베이스를 프로그래밍 방식으로 복원 하려면 [자동화 된 백업을 사용 하 여 프로그래밍 방식으로 복구 수행](recovery-using-backups.md)을 참조 하세요.

## <a name="deleted-database-restore"></a>삭제된 데이터베이스 복원

삭제 된 데이터베이스를 동일한 서버나 동일한 관리 되는 인스턴스의 삭제 시간 또는 이전 시점으로 복원할 수 있습니다. Azure Portal, [PowerShell](/powershell/module/az.sql/restore-azsqldatabase)또는 [REST (Createmode = Restore)](/rest/api/sql/databases/createorupdate)를 통해이를 수행할 수 있습니다. 백업에서 새 데이터베이스를 만들어 삭제 된 데이터베이스를 복원 합니다.

> [!IMPORTANT]
> 서버 또는 관리 되는 인스턴스를 삭제 하면 모든 해당 데이터베이스도 삭제 되 고 복구할 수 없습니다. 삭제 된 서버 또는 관리 되는 인스턴스는 복원할 수 없습니다.

### <a name="deleted-database-restore-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 삭제 된 데이터베이스 복원

삭제 된 데이터베이스는 Azure Portal 서버 또는 관리 되는 인스턴스 리소스에서 복원할 수 있습니다.

> [!TIP]
> 최근에 삭제 된 데이터베이스가 Azure Portal의 **삭제 된 데이터베이스** 페이지에 나타나거나 삭제 된 데이터베이스를 [프로그래밍 방식으로](#programmatic-recovery-using-automated-backups)표시 하는 데 몇 분 정도 걸릴 수 있습니다.

#### <a name="sql-database"></a>SQL Database

Azure Portal를 사용 하 여 삭제 된 데이터베이스를 삭제 시간으로 복구 하려면 서버 개요 페이지를 열고 **삭제 된 데이터베이스** 를 선택 합니다. 복원 하려는 삭제 된 데이터베이스를 선택 하 고 백업에서 복원 된 데이터로 생성 될 새 데이터베이스의 이름을 입력 합니다.

  ![삭제 된 데이터베이스 복원의 스크린샷](./media/recovery-using-backups/restore-deleted-sql-database-annotated.png)

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure Portal를 사용 하 여 관리 되는 데이터베이스를 복구 하려면 관리 되는 인스턴스 개요 페이지를 열고 **삭제 된 데이터베이스** 를 선택 합니다. 복원 하려는 삭제 된 데이터베이스를 선택 하 고 백업에서 복원 된 데이터로 생성 될 새 데이터베이스의 이름을 입력 합니다.

  ![삭제 된 Azure SQL Managed Instance 데이터베이스 복원의 스크린샷](./media/recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="deleted-database-restore-by-using-powershell"></a>PowerShell을 사용 하 여 삭제 된 데이터베이스 복원

PowerShell을 사용 하 여 SQL Database 또는 SQL Managed Instance에 대해 삭제 된 데이터베이스를 복원 하려면 다음 샘플 스크립트를 사용 합니다.

#### <a name="sql-database"></a>SQL Database

Azure SQL Database에서 삭제 된 데이터베이스를 복원 하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [powershell을 사용 하 여 데이터베이스 복원](scripts/restore-database-powershell.md)을 참조 하세요.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

삭제 된 인스턴스 데이터베이스를 복원 하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [powershell을 사용 하 여 삭제 된 인스턴스 데이터베이스 복원](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) 을 참조 하세요.

> [!TIP]
> 삭제 된 데이터베이스를 프로그래밍 방식으로 복원 하려면 [자동화 된 백업을 사용 하 여 프로그래밍 방식으로 복구 수행](recovery-using-backups.md)을 참조 하세요.

## <a name="geo-restore"></a>지역 복원

> [!IMPORTANT]
> 지역 복원은 지역 중복 [백업 저장소](automated-backups-overview.md#backup-storage-redundancy)로 구성 된 SQL 데이터베이스 또는 관리 되는 인스턴스에서만 사용할 수 있습니다.

가장 최근의 지역에서 복제 된 백업에서 모든 Azure 지역의 관리 되는 인스턴스의 모든 SQL Database 서버 또는 인스턴스 데이터베이스에 있는 데이터베이스를 복원할 수 있습니다. 지역 복원에서는 지역에서 복제 된 백업을 원본으로 사용 합니다. 가동 중단으로 인해 데이터베이스 또는 데이터 센터에 액세스할 수 없는 경우에도 지역 복원을 요청할 수 있습니다.

지역 복원은 호스팅 지역의 인시던트에 의해 데이터베이스를 사용할 수 없는 경우의 기본 복구 옵션입니다. 다른 지역의 서버에 데이터베이스를 복원할 수 있습니다. 백업을 만들 때와 다른 지역에 있는 Azure Blob으로 지역 복제하는 사이에 지연이 있습니다. 따라서 복원 된 데이터베이스는 원본 데이터베이스의 최대 한 시간 뒤에 있을 수 있습니다. 다음 그림에서는 다른 지역에서 사용 가능한 마지막 백업 으로부터 데이터베이스 복원을 보여 줍니다.

![지역 복원 그래픽](./media/recovery-using-backups/geo-restore-2.png)

### <a name="geo-restore-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 지역 복원

Azure Portal에서 새 단일 또는 관리 되는 인스턴스 데이터베이스를 만들고 사용 가능한 지역 복원 백업을 선택 합니다. 새로 만든 데이터베이스는 지역 복원 백업 데이터를 포함 합니다.

#### <a name="sql-database"></a>SQL Database

선택한 지역 및 서버의 Azure Portal에서 단일 데이터베이스를 지역 복원 하려면 다음 단계를 수행 합니다.

1. **대시보드** 에서 **추가**  >  **SQL Database 만들기** 를 선택 합니다. **기본 사항** 탭에서 필요한 정보를 입력 합니다.
2. **추가 설정** 을 선택합니다.
3. **기존 데이터 사용** 에 대해 **백업** 을 선택 합니다.
4. **Backup** 의 경우 사용 가능한 지역 복원 백업 목록에서 백업을 선택 합니다.

    ![SQL Database 만들기 옵션의 스크린샷](./media/recovery-using-backups/geo-restore-azure-sql-database-list-annotated.png)

백업에서 새 데이터베이스를 만드는 과정을 완료 합니다. Azure SQL Database에서 데이터베이스를 만들면 복원 된 지역 복원 백업이 포함 됩니다.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure Portal에서 관리 되는 인스턴스 데이터베이스를 선택한 지역에 있는 기존 관리 되는 인스턴스로 지역 복원 하려면 데이터베이스를 복원할 관리 되는 인스턴스를 선택 합니다. 다음 단계를 수행합니다.

1. **새 데이터베이스** 를 선택 합니다.
2. 원하는 데이터베이스 이름을 입력 합니다.
3. **기존 데이터 사용** 에서 **백업** 을 선택 합니다.
4. 사용 가능한 지역 복원 백업 목록에서 백업을 선택 합니다.

    ![새 데이터베이스 옵션의 스크린샷](./media/recovery-using-backups/geo-restore-sql-managed-instance-list-annotated.png)

새 데이터베이스를 만드는 과정을 완료 합니다. 인스턴스 데이터베이스를 만들면 복원 된 지역 복원 백업이 포함 됩니다.

### <a name="geo-restore-by-using-powershell"></a>PowerShell을 사용 하 여 지역 복원

#### <a name="sql-database"></a>SQL Database

단일 데이터베이스에 대해 지역에서 복원을 수행 하는 방법을 보여 주는 PowerShell 스크립트는 [powershell을 사용 하 여 단일 데이터베이스를 이전 시점으로 복원](scripts/restore-database-powershell.md)을 참조 하세요.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

관리 되는 인스턴스 데이터베이스의 지역 복원을 수행 하는 방법을 보여 주는 PowerShell 스크립트는 [powershell을 사용 하 여 관리 되는 인스턴스 데이터베이스를 다른 지역으로 복원](../managed-instance/scripts/restore-geo-backup.md)을 참조 하세요.

### <a name="geo-restore-considerations"></a>지역 복원 고려 사항

지역 보조 데이터베이스에서는 특정 시점 복원을 수행할 수 없습니다. 주 데이터베이스 에서만이 작업을 수행할 수 있습니다. 지역 복원을 사용 하 여 가동 중단에서 복구 하는 방법에 대 한 자세한 내용은 가동 [중단에서 복구](../../key-vault/general/disaster-recovery-guidance.md)를 참조 하세요.

> [!IMPORTANT]
> 지역 복원은 SQL Database 및 SQL Managed Instance에서 사용할 수 있는 가장 기본적인 재해 복구 솔루션입니다. 복구 지점 목표 (RPO)를 최대 1 시간까지, 예상 복구 시간은 최대 12 시간으로 자동 생성 된 지역에서 복제 된 백업에 의존 합니다. 지역 가동 중단 후에는 대상 지역에서 데이터베이스를 복원할 수 있는 용량을 보장 하지 않습니다. 응용 프로그램에서 비교적 작은 데이터베이스를 사용 하 고 비즈니스에 중요 하지 않은 경우 지역 복원은 적절 한 재해 복구 솔루션입니다. 
>
> 데이터베이스 규모가 크고 비즈니스 연속성을 보장 해야 하는 업무상 중요 한 응용 프로그램의 경우 [자동 장애 조치 (failover) 그룹](auto-failover-group-overview.md)을 사용 합니다. 이는 훨씬 낮은 RPO 및 복구 시간 목표를 제공 하며 용량은 항상 보장 됩니다. 
>
> 비즈니스 연속성 선택 사항에 대 한 자세한 내용은 [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)를 참조 하세요.

## <a name="programmatic-recovery-using-automated-backups"></a>자동화 된 백업을 사용 하 여 프로그래밍 방식 복구

복구를 위해 Azure PowerShell 또는 REST API를 사용할 수도 있습니다. 다음 표는 사용 가능한 명령의 집합을 보여 줍니다.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 SQL Database 및 SQL Managed Instance에서 계속 지원 되지만 모든 향후 개발은 Az. Sql 모듈에 대 한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](/powershell/module/AzureRM.Sql/)을 참조하세요. Az module 및 Azure Resource Manager 모듈의 명령에 대 한 인수는 뛰어난 익스텐트와 동일 합니다.

#### <a name="sql-database"></a>SQL Database

독립 실행형 또는 풀링된 데이터베이스를 복원 하려면 [AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase)을 참조 하세요.

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |하나 이상의 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | 복원할 수 있는 삭제된 데이터베이스를 가져옵니다. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |데이터베이스의 지역 중복 백업을 가져옵니다. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |데이터베이스를 복원합니다. |

  > [!TIP]
  > 데이터베이스의 지정 시간 복원을 수행 하는 방법을 보여 주는 샘플 PowerShell 스크립트는 [powershell을 사용 하 여 데이터베이스 복원](scripts/restore-database-powershell.md)을 참조 하세요.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

관리 되는 인스턴스 데이터베이스를 복원 하려면 [AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)을 참조 하세요.

  | Cmdlet | Description |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |하나 이상의 관리 되는 인스턴스를 가져옵니다. |
  | [AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | 인스턴스 데이터베이스를 가져옵니다. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |인스턴스 데이터베이스를 복원 합니다. |

### <a name="rest-api"></a>REST API

REST API를 사용 하 여 데이터베이스를 복원 하려면 다음을 수행 합니다.

| API | 설명 |
| --- | --- |
| [REST(createMode=Recovery)](/rest/api/sql/databases) |데이터베이스를 복원합니다. |
| [데이터베이스 만들기 또는 업데이트 상태 가져오기](/rest/api/sql/operations) |복원 작업 중에 상태를 반환 합니다. |

### <a name="azure-cli"></a>Azure CLI

#### <a name="sql-database"></a>SQL Database

Azure CLI를 사용 하 여 데이터베이스를 복원 하려면 [az sql db restore](/cli/azure/sql/db#az-sql-db-restore)를 참조 하십시오.

#### <a name="sql-managed-instance"></a>SQL Managed Instance

Azure CLI를 사용 하 여 관리 되는 인스턴스 데이터베이스를 복원 하려면 [az sql midb restore](/cli/azure/sql/midb#az-sql-midb-restore)를 참조 하십시오.

## <a name="summary"></a>요약

자동 백업은 사용자 및 애플리케이션 오류, 우발적인 데이터베이스 삭제 및 장시간의 가동 중단에서 데이터베이스를 보호합니다. 이 기본 제공 기능은 모든 서비스 계층 및 컴퓨팅 크기에 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [비즈니스 연속성 개요](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- [SQL Database 자동화된 백업](automated-backups-overview.md)
- [장기 보존](long-term-retention-overview.md)
- 빠른 복구 옵션에 대해 알아보려면 [활성 지역 복제](active-geo-replication-overview.md) 또는 [자동 장애 조치(failover) 그룹](auto-failover-group-overview.md)을 참조하세요.