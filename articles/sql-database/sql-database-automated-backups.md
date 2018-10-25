---
title: Azure SQL Database 자동, 지역 중복 백업 | Microsoft Docs
description: SQL Database는 몇 분마다 로컬 데이터베이스 백업을 자동으로 만들고 Azure 읽기 액세스 지역 중복 저장소를 사용하여 지리적 중복을 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: e01f48ebee9ade35b44242eba3b03e6e0a4faf46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802035"
---
# <a name="learn-about-automatic-sql-database-backups"></a>자동 SQL Database 백업에 대한 자세한 정보

SQL Database는 데이터베이스 백업을 자동으로 만들고 Azure RA-GRS(읽기 액세스 지역 중복 저장소)를 사용하여 지리적 중복을 제공합니다. 이러한 백업은 추가 비용 없이 자동으로 만들어집니다. 사용자는 아무 작업도 할 필요가 없습니다. 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 보안 규칙에서 오랫동안 백업을 사용할 수 있도록 요구하는 경우, 장기 백업 보존 정책을 구성할 수 있습니다. 자세한 내용은 [장기 보존](sql-database-long-term-retention.md)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database 백업이란?

SQL Database는 SQL Server 기술을 사용하여 PITR(지정 시간 복원)의 목적으로 [전체](https://msdn.microsoft.com/library/ms186289.aspx), [차등](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server) 및 [트랜잭션 로그](https://msdn.microsoft.com/library/ms191429.aspx) 백업을 만듭니다. 트랜잭션 로그 백업은 일반적으로 5~10분마다 발생하고, 차등 백업은 일반적으로 12시간마다 발생하며, 이러한 빈도는 계산 크기와 데이터베이스 작업량에 따라 달라집니다. 전체 및 차등 백업을 사용하는 트랜잭션 로그 백업을 통해 데이터베이스를 호스트하는 동일한 서버로 특정 시점에 대해 데이터베이스를 복원할 수 있습니다. 백업은 데이터 센터 가동 중단으로부터 보호하기 위해 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 복제되는 RA-GRS 저장소 Blob에 저장됩니다. 사용자가 데이터베이스를 복원할 때 서비스에서는 전체, 차등, 트랜잭션 로그 백업 중 무엇을 복원해야 하는지 파악합니다.


이러한 백업을 사용하여 다음을 수행할 수 있습니다.

* 보존 기간 내 지정 시간으로 데이터베이스를 복원합니다. 이 작업으로 원본 데이터베이스와 같은 서버에 새 데이터베이스가 만들어집니다.
* 삭제된 시간 또는 보존 기간 내 임의 시간으로 삭제된 데이터베이스를 복원합니다. 삭제된 데이터베이스는 원래 데이터베이스가 생성되었던 동일한 서버에만 복원할 수 있습니다.
* 데이터베이스를 다른 지리적 지역으로 복원합니다. 이렇게 하면 서버 및 데이터베이스에 액세스할 수 없는 경우 지리적 재해로부터 복구할 수 있습니다. 그러면 전 세계 어디에서든 기존 서버에 새 데이터베이스가 만들어집니다. 
* LTR(장기 보존 정책)을 사용하여 데이터베이스를 구성한 경우, 특정 장기 백업에서 데이터베이스를 복원합니다. 그러면 이전 버전의 데이터베이스를 복원하여 규정 준수 요청을 충족하고 이전 버전의 응용 프로그램을 실행할 수 있습니다. [장기 보존](sql-database-long-term-retention.md)을 참조하세요.
* 복원을 수행하려면 [백업에서 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 ‘데이터베이스 복제’는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 
> 

## <a name="how-long-are-backups-kept"></a>백업은 얼마 동안 유지되나요?
각 SQL Database 백업에는 데이터베이스의 서비스 계층을 기반으로 하는 기본 보존 기간이 있으며, 이 기간은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)과 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md) 간에 차이가 있습니다. 데이터베이스의 백업 보존 기간을 업데이트할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-backup-retention-period)을 참조하세요.

데이터베이스를 삭제하면 SQL Database는 온라인 데이터베이스에 하는 것과 동일한 방식으로 백업을 유지합니다. 예를 들어, 보존 기간이 7일인 기본 데이터베이스를 삭제하는 경우, 4일 된 백업은 앞으로 3일 동안 더 저장됩니다.

최대 PITR 보존 기간보다 더 오랫동안 백업을 유지해야 하는 경우, 백업 속성을 수정하여 하나 이상의 장기 보존 기간을 데이터베이스에 추가할 수 있습니다. 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

> [!IMPORTANT]
> SQL Database를 호스트하는 Azure SQL 서버를 삭제하면 해당 서버에 속하는 모든 탄력적 풀과 데이터베이스도 삭제되어 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다. 그러나 장기 보존을 구성한 경우, LTR을 사용하는 데이터베이스의 백업이 삭제되지 않으므로 이러한 데이터베이스는 복원할 수 있습니다.

### <a name="pitr-retention-period"></a>PITR 보존 기간
DTU 기반 구매 모델을 사용하여 만든 데이터베이스의 기본 보존 기간은 서비스 계층에 따라 달라집니다.

* 기본 서비스 계층은 1주일입니다.
* 표준 서비스 계층은 5주입니다.
* 프리미엄 서비스 계층은 5주입니다.

[vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용하는 경우 기본 백업 보존 기간은 7일입니다(논리 서버 및 관리되는 인스턴스에서 둘 다).
논리 서버에서 [백업 보존 기간을 최대 35일로 변경](#how-to-change-backup-retention-period)할 수 있습니다. Managed Instance에서는 백업 보존 기간을 변경할 수 없습니다. 

현재 PITR 보존 기간을 줄이면 새 보존 기간보다 오래된 기존의 모든 백업을 더 이상 사용할 수 없게 됩니다. 

현재 PITR 보존 기간을 늘리면 SQL Database는 더 긴 보존 기간에 도달할 때까지 기존 백업을 유지합니다.

## <a name="how-often-do-backups-happen"></a>백업이 얼마나 자주 수행됩니까?
### <a name="backups-for-point-in-time-restore"></a>지정 시간 복원에 대한 백업
SQL Database는 전체 백업, 차등 백업 및 트랜잭션 로그 백업을 자동으로 생성하여 PITR(지정 시간 복원)에 대한 셀프 서비스를 지원합니다. 전체 데이터베이스 백업은 매주 만들어지고, 차등 데이터베이스 백업은 일반적으로 12시간마다 만들어지고, 트랜잭션 로그 백업은 일반적으로 5~10분마다 만들어지며, 이러한 빈도는 계산 크기와 데이터베이스 작업량에 따라 달라집니다. 첫 번째 전체 백업은 데이터베이스를 만든 후에 즉시 예약됩니다. 일반적으로 30분 내에 완료되지만 데이터베이스의 크기가 상당히 큰 경우에는 더 오래 걸릴 수 있습니다. 예를 들어, 복원된 데이터베이스 또는 데이터베이스 사본에 대한 초기 백업은 더 오래 걸릴 수 있습니다. 첫 번째 전체 백업 후에 모든 향후 백업은 자동으로 예약되며 백그라운드에서 자동으로 관리됩니다. 모든 데이터베이스 백업의 정확한 타이밍은 전반적인 시스템 워크로드를 감안하여 SQL Database 서비스에 의해 결정됩니다.

PITR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)을 참조하세요.

### <a name="backups-for-long-term-retention"></a>장기 보존에 대한 백업
논리 서버에서 호스트되는 SQL Database는 Azure Blob Storage에서 전체 백업의 LTR(장기 보존)을 최대 10년 동안 구성하는 옵션을 제공합니다. LTR 정책을 사용하면 매주 전체 백업이 다른 RA-GRS 저장소 컨테이너로 자동으로 복사됩니다. 서로 다른 준수 요구 사항을 충족하려면 주별, 월별 및/또는 연도별 백업에 대해 다른 보존 기간을 선택할 수 있습니다. 저장소 사용량은 선택한 백업 빈도 및 보존 기간에 따라 다릅니다. [LTR 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=sql-database)를 사용하여 LTR 저장소 비용을 추정할 수 있습니다. 

PITR과 마찬가질 LTR 백업은 지역 중복 백업이며 [Azure Storage 지역 간 복제](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)로 보호됩니다.

자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

## <a name="are-backups-encrypted"></a>백업이 암호화되나요?

데이터베이스가 TDE를 사용하여 암호화된 경우, LTR 백업을 포함한 백업이 미사용 시 자동으로 암호화됩니다. Azure SQL Database에 TDE를 사용할 때 백업이 암호화됩니다. 모든 새 Azure SQL Database는 기본적으로 사용한 TDE로 구성됩니다. TDE에 대한 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)를 참조하세요.

## <a name="how-does-microsoft-ensure-backup-integrity"></a>백업 무결성은 Microsoft에서 어떻게 보장하나요?

Azure SQL Database 엔지니어링 팀은 지속적으로 서비스 전체에서 데이터베이스의 자동화된 데이터베이스 백업에 대한 복원을 자동으로 테스트합니다. 복원 시 데이터베이스도 DBCC CHECKDB를 사용하여 무결성 검사를 받습니다. 무결성 검사 중에 문제가 발견되면 해당 경고를 엔지니어링 팀에 알려줍니다. Azure SQL Database의 데이터 무결성에 대한 자세한 내용은 [Azure SQL Database의 데이터 무결성](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)을 참조하세요.

## <a name="how-do-automated-backups-impact-my-compliance"></a>자동화된 백업은 준수에 어떻게 영향을 주나요?

기본 PITR 보존 기간이 35일인 DTU 기반 서비스 계층에서 vCore 기반 서비스 계층으로 데이터베이스를 마이그레이션하는 경우, 응용 프로그램의 데이터 복구 정책이 손상되지 않도록 PITR 보존이 유지됩니다. 기본 보존이 준수 요구 사항을 충족하지 못하는 경우, PowerShell 또는 REST API를 사용하여 PITR 보존 기간을 변경할 수 있습니다. 자세한 내용은 [백업 보존 기간 변경](#how-to-change-backup-retention-period)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>백업 보존 기간을 변경하는 방법

> [!Note]
> Managed Instance에서는 기본 백업 보존 기간(7일)을 변경할 수 없습니다. 

REST API 또는 PowerShell을 사용하여 기본 보존을 변경할 수 있습니다. 지원되는 값은 7일, 14일, 21일, 28일 또는 35일입니다. 다음 예제에서는 PITR 보존 기간을 28일로 변경하는 방법을 보여 줍니다. 

> [!NOTE]
> 이 API는 PITR 보존 기간에만 영향을 줍니다. 데이터베이스에 대해 LTR을 구성한 경우에는 영향을 받지 않습니다. LTR 보존 기간을 변경하는 방법에 대한 자세한 내용은 [장기 백업 보존](sql-database-long-term-retention.md)을 참조하세요.

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell을 사용하여 PITR 백업 보존 기간 변경
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> 이 API는 [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview) 버전부터 AzureRM.Sql PowerShell 모듈에 포함됩니다. 

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API를 사용하여 PITR 보존 기간 변경
**샘플 요청**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**요청 본문**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**샘플 응답**

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
자세한 내용은 [Backup Retention REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)(백업 보존 REST API)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 다른 Azure SQL Database 비즈니스 연속성 솔루션에 대해 알아보려면 [비즈니스 연속성 개요](sql-database-business-continuity.md)를 참조하세요.
- Azure Portal을 사용하여 지정 시간으로 복원하려면 [Azure Portal을 사용하여 지정 시간으로 데이터베이스 복원](sql-database-recovery-using-backups.md)을 참조하세요.
- PowerShell을 사용하여 지정 시간으로 복원하려면 [PowerShell을 사용하여 지정 시간으로 데이터베이스 복원](scripts/sql-database-restore-database-powershell.md)을 참조하세요.
- Azure Portal을 사용하여 Azure Blob 저장소에서 자동화된 백업을 장기 보존에서 구성, 관리 및 복원하려면 [Azure Portal을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
- PowerShell을 사용하여 Azure Blob 저장소의 장기 보존된 자동화된 백업에서 구성, 관리 및 복원하려면 [PowerShell을 사용하여 장기 백업 보존 관리](sql-database-long-term-backup-retention-configure.md)를 참조하세요.
