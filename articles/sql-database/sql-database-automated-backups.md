---
title: 자동 지역 중복 백업
description: SQL Database은 몇 분 마다 로컬 데이터베이스 백업을 자동으로 만들고 지역 중복성을 위해 Azure 읽기 액세스 지역 중복 저장소를 사용 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 09/26/2019
ms.openlocfilehash: 1754168478caf3ca029e003ad0187fc29e85fa8a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74997295"
---
# <a name="automated-backups"></a>Automatizált biztonsági mentések

SQL Database는 7 일에서 35 일 사이에 유지 되는 데이터베이스 백업을 자동으로 만들고, Azure [읽기 액세스 지역 중복 저장소 (RA-GRS)](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 를 사용 하 여 데이터 센터를 사용할 수 없는 경우에도 유지 되도록 합니다. 이러한 백업은 자동으로 생성 됩니다. 데이터베이스 백업은 실수로 손상 되거나 삭제 되지 않도록 데이터를 보호 하기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 보안 규칙에 따라 오랫동안 백업을 사용할 수 있어야 하는 경우 (최대 10 년) 단일 데이터베이스 및 탄력적 풀에 대 한 [장기 보존](sql-database-long-term-retention.md) 을 구성할 수 있습니다.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업 이란?

SQL Database는 SQL Server 기술을 사용 하 여 매주 [전체 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server) , 12 시간 마다 [차등 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 및 5-10 분 마다 [트랜잭션 로그 백업을](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) 만듭니다. 백업은 데이터 센터 가동 중단 으로부터 보호 하기 위해 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md) 에 복제 되는 [RA GRS storage blob](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 에 저장 됩니다. 데이터베이스를 복원 하면 전체, 차등 및 트랜잭션 로그 백업을 복원 해야 합니다.

A biztonsági másolatokat a következő célokra használhatja:

- Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용 하 여 기존 데이터베이스를 보존 기간 내의 **특정 시점으로 복원** 합니다. 단일 데이터베이스 및 탄력적 풀에서이 작업은 원본 데이터베이스와 동일한 서버에 새 데이터베이스를 만듭니다. Managed Instance이 작업을 수행 하면 데이터베이스의 복사본 또는 동일한 구독에서 동일 하거나 다른 Managed Instance을 만들 수 있습니다.
  - 백업 정책을 구성 하려면 **[백업 보존 기간](#how-to-change-the-pitr-backup-retention-period)** 을 7 일에서 35 일로 변경 합니다.
  - [Azure Portal](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies) 또는 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell)를 사용 하 여 Single Database 및 탄력적 풀에서 **최대 10 년까지 장기 보존 정책을 변경** 합니다.
- 삭제 **된 데이터베이스를 삭제 된 시간** 또는 보존 기간 내에 언제 든 지 복원 합니다. 삭제 된 데이터베이스는 원래 데이터베이스가 만들어진 동일한 논리 서버 또는 Managed Instance 에서만 복원할 수 있습니다.
- **데이터베이스를 다른 지역으로 복원**합니다. 지역 복원 기능을 사용 하면 서버 및 데이터베이스에 액세스할 수 없을 때 지리적 재해 로부터 복구할 수 있습니다. 전 세계의 모든 기존 서버에 새 데이터베이스를 만듭니다.
- Single Database 또는 Elastic Pool에 대 한 **특정 장기 백업에서 데이터베이스를 복원** 합니다. 단, 데이터베이스가 LTR (장기 보존 정책)로 구성 된 경우에는를 사용 합니다. LTR을 사용 하면 [Azure Portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) 또는 [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) 를 사용 하 여 이전 버전의 데이터베이스를 복원 하 여 규정 준수 요청을 충족 하거나 이전 버전의 응용 프로그램을 실행할 수 있습니다. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).
- 복원을 수행 하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조 하세요.

> [!NOTE]
> Azure storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사 하는 것을 의미 합니다. SQL의 *데이터베이스 복제* 는 여러 보조 데이터베이스를 주 데이터베이스와 동기화 된 상태로 유지 하는 것을 말합니다.

다음 예제를 사용 하 여 이러한 작업 중 일부를 시도할 수 있습니다.

| | Az Azure Portal | Azure PowerShell |
|---|---|---|
| 백업 보존 변경 | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 장기 백업 보존 기간 변경 | [단일 데이터베이스](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance-해당 없음  | [Single Database](sql-database-long-term-backup-retention-configure.md)<br/>Managed Instance-해당 없음  |
| 특정 시점에서 데이터베이스 복원 | [단일 데이터베이스](sql-database-recovery-using-backups.md#point-in-time-restore) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| Törölt adatbázis visszaállítása | [단일 데이터베이스](sql-database-recovery-using-backups.md) | [단일 데이터베이스](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage에서 데이터베이스 복원 | 단일 데이터베이스-해당 없음 <br/>Managed Instance-해당 없음  | 단일 데이터베이스-해당 없음 <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="how-long-are-backups-kept"></a>백업 보존 기간

모든 Azure SQL 데이터베이스 (단일, 풀링된 및 관리 되는 인스턴스 데이터베이스)의 기본 백업 보존 기간은 **7** 일입니다. [백업 보존 기간은 최대 35 일까지 변경할](#how-to-change-the-pitr-backup-retention-period)수 있습니다.

데이터베이스를 삭제 하는 경우 SQL Database은 온라인 데이터베이스의 경우와 동일한 방식으로 백업을 유지 합니다. 예를 들어 보존 기간이 7 일인 기본 데이터베이스를 삭제 하는 경우 5 일 동안 경과한 백업은 3 일 동안 저장 됩니다.

최대 보존 기간 보다 오래 된 백업을 유지 해야 하는 경우 백업 속성을 수정 하 여 데이터베이스에 장기 보존 기간을 하나 이상 추가할 수 있습니다. További információkért lásd: [Hosszú távú megőrzés](sql-database-long-term-retention.md).

> [!IMPORTANT]
> SQL 데이터베이스를 호스트 하는 Azure SQL server를 삭제 하면 해당 서버에 속한 모든 탄력적 풀 및 데이터베이스도 삭제 되며 복구할 수 없습니다. 삭제 된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우에는 LTR을 사용 하는 데이터베이스에 대 한 백업이 삭제 되지 않으며 이러한 데이터베이스를 복원할 수 있습니다.

## <a name="how-often-do-backups-happen"></a>백업이 수행 되는 빈도

### <a name="backups-for-point-in-time-restore"></a>지정 시간 복원에 대 한 백업

SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 만들어 PITR (지정 시간 복원)를 위한 셀프 서비스를 지원 합니다. 전체 데이터베이스 백업은 매주 생성 되 고, 차등 데이터베이스 백업은 12 시간 마다 일반적으로 생성 되며, 트랜잭션 로그 백업은 일반적으로 5-10 분 마다 생성 되며 데이터베이스 작업의 계산 크기와 양에 따라 결정 됩니다. 첫 번째 전체 백업은 데이터베이스를 만든 직후에 예약 됩니다. 일반적으로 30 분 내에 완료 되지만 데이터베이스의 크기가 큰 경우에는 시간이 오래 걸릴 수 있습니다. 예를 들어 초기 백업은 복원 된 데이터베이스 또는 데이터베이스 복사본에서 더 오래 걸릴 수 있습니다. Az első teljes biztonsági mentés után a további mentések felhasználói beavatkozás nélkül, a háttérben lesznek automatikusan ütemezve és felügyelve. Az adatbázis-mentések pontos időzítését az SQL Database szolgáltatás határozza meg a teljes rendszer terhelésének kiegyensúlyozásával. A biztonsági mentési feladatok nem módosíthatók és nem tilthatók le. 

PITR 백업은 지역 중복 이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) 를 통해 보호 됩니다.

자세한 내용은 지정 [시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore) 을 참조 하세요.

### <a name="backups-for-long-term-retention"></a>장기 보존을 위한 백업

단일 및 풀링된 데이터베이스는 Azure Blob storage에서 최대 10 년 동안 전체 백업의 장기 보존 (LTR)을 구성 하는 옵션을 제공 합니다. LTR 정책을 사용 하도록 설정 하면 주간 전체 백업이 다른 RA GRS 저장소 컨테이너에 자동으로 복사 됩니다. 서로 다른 규정 준수 요구 사항을 충족 하기 위해 주별, 월별 및/또는 매년 백업에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 저장소 사용량은 선택한 백업 빈도 및 보존 기간에 따라 달라 집니다. [Ltr 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database) 를 사용 하 여 ltr 저장소의 비용을 예측할 수 있습니다.

PITR와 마찬가지로 LTR 백업은 지역 중복 이며 [지역 간 복제를 Azure Storage](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)하 여 보호 됩니다.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조 하세요.

## <a name="storage-costs"></a>Tárolási költségek
단일 데이터베이스 및 관리 되는 인스턴스의 경우 데이터베이스 크기의 100%에 해당 하는 최소 백업 저장소 양은 추가 비용 없이 제공 됩니다. 탄력적 풀의 경우 풀에 할당 된 데이터 저장소의 100%와 같은 최소 백업 저장소 양은 추가 비용 없이 제공 됩니다. A biztonsági mentési tár ezt meghaladó használata GB/hó díjszabási alapon történik. 이러한 추가 사용은 개별 데이터베이스의 워크 로드 및 크기에 따라 달라 집니다.

Azure 구독 비용 분석을 사용 하 여 백업 저장소에 대 한 현재 비용을 확인할 수 있습니다.

![백업 저장소 비용 분석](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)

구독으로 이동 하 여 비용 분석 블레이드를 열면 측정기 하위 범주 **mi pitr backup storage** 를 선택 하 여 현재 백업 비용 및 요금 예측을 확인할 수 있습니다. 또한 **관리 되는 인스턴스 범용 저장소** 또는 **관리 되는 인스턴스 범용-compute gen5** 와 같은 다른 미터 하위 범주를 포함 하 여 백업 저장소 비용을 다른 비용 범주와 비교할 수 있습니다.

> [!Note]
> [보존 기간을 7 일로 변경](#change-pitr-backup-retention-period-using-azure-portal) 하 여 백업 저장소 비용을 줄일 수 있습니다.

저장소 가격에 대 한 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/) 페이지를 참조 하세요. 

## <a name="are-backups-encrypted"></a>암호화 된 백업

데이터베이스가 TDE를 사용 하 여 암호화 된 경우에는 LTR 백업을 포함 하 여 백업이 미사용 상태로 자동으로 암호화 됩니다. Azure SQL database에 대해 TDE를 사용 하도록 설정 하면 백업만 암호화 됩니다. 모든 새 Azure SQL database는 기본적으로 TDE를 사용 하도록 설정 하 여 구성 됩니다. TDE에 대 한 자세한 내용은 [Azure SQL Database를 사용 하 여 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조 하세요.

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft에서 백업 무결성을 어떻게 보장 하나요?

Azure SQL Database 엔지니어링 팀은 지속적으로 논리 서버 및 탄력적 풀에 배치 된 데이터베이스의 자동화 된 데이터베이스 백업 복원을 자동으로 테스트 합니다 .이는 Managed Instance에서 사용할 수 없습니다. 지정 시간 복원 시 데이터베이스는 DBCC CHECKDB를 사용 하 여 무결성 검사를 수신 합니다.

마이그레이션이 완료 되 면 기본 `RESTORE` 명령 또는 데이터 마이그레이션 서비스를 사용 하 여 복원 된 데이터베이스의 `CHECKSUM`를 사용 하 여 자동 초기 백업을 수행 Managed Instance 합니다.

무결성 검사를 수행 하는 동안 발견 된 문제는 엔지니어링 팀에 경고를 생성 합니다. Azure SQL Database의 데이터 무결성에 대 한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조 하세요.

## <a name="how-do-automated-backups-impact-compliance"></a>자동화 된 백업에 대 한 영향 준수 방법

기본 PITR 보존이 35 일인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하면 응용 프로그램의 데이터 복구 정책이 손상 되지 않도록 PITR 보존이 보존 됩니다. 기본 보존이 규정 준수 요구 사항을 충족 하지 않는 경우 PowerShell 또는 REST API를 사용 하 여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-the-pitr-backup-retention-period)을 참조 하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR 백업 보존 기간을 변경 하는 방법

Azure Portal, PowerShell 또는 REST API를 사용 하 여 기본 PITR 백업 보존 기간을 변경할 수 있습니다. 다음 예에서는 PITR 보존을 28 일로 변경 하는 방법을 보여 줍니다.

> [!WARNING]
> 현재 보존 기간을 줄이면 새 보존 기간 보다 오래 된 기존 백업은 더 이상 사용할 수 없습니다. 현재 보존 기간을 늘리면 SQL Database는 보존 기간에 도달할 때까지 기존 백업을 유지 합니다.

> [!NOTE]
> 이러한 Api는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경 하는 방법에 대 한 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조 하세요.

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Azure Portal를 사용 하 여 PITR 백업 보존 기간 변경

Azure Portal를 사용 하 여 PITR 백업 보존 기간을 변경 하려면 포털 내에서 보존 기간을 변경 하려는 서버 개체로 이동한 후 수정 하려는 서버 개체에 따라 적절 한 옵션을 선택 합니다.

#### <a name="single-database--elastic-poolstabsingle-database"></a>[단일 데이터베이스 & 탄력적 풀](#tab/single-database)

단일 Azure SQL Database에 대 한 PITR 백업 보존 변경은 서버 수준에서 수행 됩니다. 서버 수준에서 변경한 내용은 해당 서버의 데이터베이스에 적용 됩니다. Azure Portal에서 Azure SQL Database server에 대 한 PITR를 변경 하려면 서버 개요 블레이드로 이동 하 여 탐색 메뉴에서 백업 관리를 클릭 한 다음 탐색 모음에서 보존 구성을 클릭 합니다.

![PITR Azure Portal 변경](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instancetabmanaged-instance"></a>[Managed Instance](#tab/managed-instance)

SQL Database 관리 되는 인스턴스에 대 한 PITR 백업 보존 변경은 개별 데이터베이스 수준에서 수행 됩니다. Azure Portal에서 인스턴스 데이터베이스의 PITR 백업 보존 기간을 변경 하려면 개별 데이터베이스 개요 블레이드로 이동한 다음 탐색 모음에서 백업 보존 구성을 클릭 합니다.

![PITR Azure Portal 변경](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용 하 여 PITR 백업 보존 기간 변경

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대한 내용은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조합니다. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API를 사용 하 여 PITR 보존 기간 변경

#### <a name="sample-request"></a>Mintakérelem

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>A kérelem törzse

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>Mintaválasz

상태 코드: 200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

자세한 내용은 [백업 보존 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)를 참조 하세요.

## <a name="next-steps"></a>Következő lépések

- 데이터베이스 백업은 실수로 손상 되거나 삭제 되지 않도록 데이터를 보호 하기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조 하세요.
- Azure Portal를 사용 하 여 지정 시간으로 복원 하려면 Azure Portal를 [사용 하 여 특정 시점으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조 하세요.
- PowerShell을 사용 하 여 특정 시점으로 복원 하려면 [powershell을 사용 하 여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조 하세요.
- Azure Portal를 사용 하 여 Azure Blob storage에서 자동화 된 백업의 장기 보존을 구성, 관리 및 복원 하려면 [Azure Portal를 사용 하 여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조 하세요.
- PowerShell을 사용 하 여 Azure Blob storage에서 자동화 된 백업의 장기 보존을 구성, 관리 및 복원 하려면 PowerShell을 [사용 하 여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조 하세요.
