---
title: 백업 및 복원-스냅숏, 지역 중복
description: Azure Synapse Analytics SQL 풀에서 백업 및 복원 작업을 수행 하는 방법에 대해 알아봅니다. 백업을 사용 하 여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원 합니다. 지역 중복 백업을 사용하여 다른 지역에 복원합니다.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/04/2020
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: d4a08035b03c104555c39311bfb812218cca44b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482550"
---
# <a name="backup-and-restore-in-azure-synapse-sql-pool"></a>Azure Synapse SQL 풀에서 백업 및 복원

Azure Synapse SQL 풀에서 백업 및 복원을 사용 하는 방법에 대해 알아봅니다. SQL 풀 복원 요소를 사용 하 여 데이터 웨어하우스를 복구 하거나 주 지역의 이전 상태로 복사 합니다. 데이터 웨어하우스 지역 중복 백업을 사용하여 다른 지역에 복원합니다.

## <a name="what-is-a-data-warehouse-snapshot"></a>데이터 웨어하우스 스냅숏의 정의

*데이터 웨어하우스 스냅샷*은 데이터 웨어하우스를 이전 상태로 복구하거나 복사하는 데 활용할 수 있는 복원 지점을 만듭니다.  SQL 풀은 분산 시스템 이므로 데이터 웨어하우스 스냅숏은 Azure storage에 있는 여러 파일로 구성 됩니다. 스냅샷은 데이터 웨어하우스에 저장된 데이터의 증분 변경 내용을 캡처합니다.

*데이터 웨어하우스 복원*은 기존 데이터 웨어하우스 또는 삭제된 데이터 웨어하우스의 복원 지점에서 만들어지는 새 데이터 웨어하우스입니다. 데이터 웨어하우스 복원은 데이터가 실수로 손상되거나 삭제된 후 데이터를 다시 만들기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 또한 데이터 웨어하우스는 테스트 또는 개발 용도로 데이터 웨어하우스의 복사본을 만드는 강력한 메커니즘입니다.  SQL 풀 복원 요금은 원본 및 대상 데이터 웨어하우스의 데이터베이스 크기와 위치에 따라 달라질 수 있습니다.

## <a name="automatic-restore-points"></a>자동 복원 지점

스냅숏은 복원 지점이 만들어지는 기본 제공 기능입니다. 이 기능은 사용하도록 설정할 필요가 없습니다. 그러나 복원 지점을 만들기 위해 SQL 풀이 활성 상태 여야 합니다. SQL 풀이 자주 일시 중지 된 경우에는 자동 복원 지점을 만들지 못할 수 있으므로 SQL 풀을 일시 중지 하기 전에 사용자 정의 복원 지점을 만들어야 합니다. 서비스에서 복구에 대 한 Sla를 유지 하기 위해 이러한 복원 지점이 사용 되므로 현재 자동 복원 지점은 사용자가 삭제할 수 없습니다.

7 일 동안 사용할 수 있는 복원 지점이 생성 되는 날 내내 데이터 웨어하우스의 스냅숏이 생성 됩니다. 이 보존 기간은 변경할 수 없습니다. SQL 풀은 8 시간 RPO (복구 지점 목표)를 지원 합니다. 지난 7일 동안 수행된 스냅샷 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다.

마지막 스냅숏이 시작 된 시간을 확인 하려면 온라인 SQL 풀에서이 쿼리를 실행 합니다.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>사용자 정의 복원 지점

이 기능을 사용 하면 스냅숏을 수동으로 트리거하여 많은 수정 작업 전후에 데이터 웨어하우스의 복원 지점이 생성 될 수 있습니다. 이 기능을 사용 하면 복원 지점이 논리적으로 일치 하므로, 작업 중단 또는 빠른 복구 시간에 대 한 사용자 오류 발생 시 추가 데이터 보호를 제공 합니다. 사용자 정의 복원 지점은 7일 동안 사용할 수 있으며 자동으로 삭제됩니다. 사용자 정의 복원 지점의 보존 기간은 변경할 수 없습니다. 어떤 시점에서든 **42개의 사용자 정의 복원 지점**만 지원되므로 다른 복원 지점을 만들기 전에 [삭제](https://go.microsoft.com/fwlink/?linkid=875299)해야 합니다. 사용자 정의 복원 지점은 [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) 또는 Azure Portal을 통해 스냅샷을 트리거하여 만들 수 있습니다.

> [!NOTE]
> 7일보다 더 긴 복원 시점이 필요한 경우 [여기서](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 새 데이터 웨어하우스로 복원할 수 있습니다. 복원 된 후에는 SQL 풀이 온라인 상태 이며 계산 비용을 절약 하기 위해 무기한 일시 중지할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 스토리지 비용이 부과됩니다. 복원된 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분 만에 다시 시작할 수 있습니다.

### <a name="restore-point-retention"></a>복원 지점 보존

다음은 복원 지점 보존 기간에 대 한 세부 정보를 나열 합니다.

1. SQL 풀은 7 일의 보존 기간에 도달 했 **을 때와** 최소 42의 총 복원 지점 (사용자 정의 및 자동 포함)이 있는 경우 복원 지점을 삭제 합니다.
2. SQL 풀이 일시 중지 되 면 스냅숏이 생성 되지 않습니다.
3. 복원 지점의 보존 기간은 SQL 풀이 일시 중지 될 때를 포함 하 여 복원 지점이 수행 된 시간부터 절대 달력 일 수로 측정 됩니다.
4. 언제 든 지 SQL 풀은 이러한 복원 지점이 7 일의 보존 기간에 도달 하지 않은 경우 사용자 정의 복원 지점 및 42 자동 복원 지점을 최대 42까지 저장할 수 있도록 보장 합니다.
5. 스냅숏이 생성 되는 경우 SQL 풀은 7 일 넘게 일시 중지 된 후 다시 시작 됩니다. 42의 총 복원 지점 (사용자 정의 및 자동 포함)이 있을 때까지 복원 지점이 유지 됩니다.

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>SQL 풀을 삭제할 때의 스냅숏 보존

SQL 풀을 삭제 하면 최종 스냅숏이 생성 되 고 7 일 동안 저장 됩니다. 삭제할 때 생성 되는 최종 복원 지점으로 SQL 풀을 복원할 수 있습니다. SQL 풀을 일시 중지 된 상태로 놓으면 스냅숏이 생성 되지 않습니다. 이 시나리오에서는 SQL 풀을 삭제 하기 전에 사용자 정의 복원 지점을 만들어야 합니다.

> [!IMPORTANT]
> SQL 풀을 호스트 하는 서버를 삭제 하면 해당 서버에 속한 모든 데이터베이스도 삭제 되 고 복구할 수 없습니다. 삭제된 서버는 복원할 수 없습니다.

## <a name="geo-backups-and-disaster-recovery"></a>지역 백업 및 재해 복구

지역 백업은 쌍으로 연결 된 [데이터 센터](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)에 하루에 한 번 생성 됩니다. 지역 복원의 RPO는 24시간입니다. SQL 풀이 지원 되는 다른 지역의 서버에 지역 백업을 복원할 수 있습니다. 지역 백업을 사용하면 주 지역의 복원 지점에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있습니다.

> [!NOTE]
> 지역 백업에 더 짧은 RPO가 필요한 경우 [여기에서](https://feedback.azure.com/forums/307516-sql-data-warehouse) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 다른 지역의 새 데이터 웨어하우스로 복원할 수 있습니다. 복원되면 데이터 웨어하우스가 온라인 상태가 되며, 이를 무기한 일시 중지하여 컴퓨팅 비용을 절감할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 스토리지 비용이 부과됩니다. 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분 만에 다시 시작할 수 있습니다.

## <a name="backup-and-restore-costs"></a>백업 및 복원 비용

Azure 청구서에는 Storage 및 재해 복구 스토리지에 대한 항목이 있습니다. 저장소 요금은 스냅숏이 캡처한 증분 변경 내용과 함께 주 지역에 데이터를 저장 하는 총 비용입니다. 스냅샷 요금 청구 방법에 대한 자세한 내용은 [스냅샷 요금 청구 방법 이해](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조하세요. 지역 중복 요금은 지역 백업을 저장하는 데 드는 비용을 포함합니다.  

주 데이터 웨어하우스 및 7일 간의 스냅샷 변경에 대한 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅샷에서 100GB를 캡처하면 2TB 데이터에 대한 비용이 Azure Premium Storage 요율로 청구됩니다.

지역 중복 스토리지를 사용하는 경우 별도의 스토리지 비용이 청구됩니다. 지역 중복 스토리지는 표준 RA-GRS(Read-Access Geo Redundant Storage) 요금이 청구됩니다.

Azure Synapse 가격 책정에 대 한 자세한 내용은 [Azure Synapse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)을 참조 하세요. 지역에서 복원 하는 경우 데이터 송신 요금이 청구 되지 않습니다.

## <a name="restoring-from-restore-points"></a>복원 지점에서 복원

각 스냅샷에서 스냅샷이 시작된 시간을 나타내는 복원 지점을 만듭니다. 데이터 웨어하우스를 복원하려면 복원 지점을 선택하고 복원 명령을 실행합니다.  

복원된 데이터 웨어하우스와 현재 데이터 웨어하우스 중 하나를 유지하거나 삭제할 수 있습니다. 현재 데이터 웨어하우스를 복원 된 데이터 웨어하우스로 바꾸려는 경우 [ALTER database (SQL 풀)](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 를 사용 하 여 이름 수정 옵션으로 이름을 바꿀 수 있습니다.

데이터 웨어하우스를 복원 하려면 [SQL 풀 복원](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)을 참조 하세요.

삭제되거나 일시 중지된 데이터 웨어하우스를 복원하기 위해 [지원 티켓을 만들](sql-data-warehouse-get-started-create-support-ticket.md) 수 있습니다.

## <a name="cross-subscription-restore"></a>구독 간 복원

구독 간에 직접 복원 해야 하는 경우 [여기](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore)에서이 기능에 투표 합니다. 다른 서버로 복원 하 고 구독 간에 서버를 [' 이동 '](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 하 여 구독 간 복원을 수행 합니다.

## <a name="geo-redundant-restore"></a>지역 중복 복원

선택한 성능 수준에서 SQL 풀을 지 원하는 모든 지역으로 [sql 풀을 복원할](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) 수 있습니다.

> [!NOTE]
> 지역 중복 복원을 수행하려면 이 기능에서 옵트아웃(opt out)하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

복원 요소에 대 한 자세한 내용은 [사용자 정의 복원 요소](sql-data-warehouse-restore-points.md) 를 참조 하세요.