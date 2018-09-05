---
title: Azure SQL Data Warehouse 백업 및 복원 - 스냅숏, 지역 중복 | Microsoft Docs
description: Azure SQL Data Warehouse에서 백업 및 복원이 어떻게 작동하는지 알아봅니다. 데이터 웨어하우스 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원합니다. 지역 중복 백업을 사용하여 다른 지역에 복원합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/24/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e9b5005fad1eeb13314e1fb6a5708bb02b96cbf9
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43248643"
---
# <a name="backup-and-restore-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 백업 및 복원
Azure SQL Data Warehouse에서 백업 및 복원이 어떻게 작동하는지 알아봅니다. 데이터 웨어하우스 스냅숏을 사용하여 데이터 웨어하우스를 주 지역의 이전 복원 지점으로 복구하거나 복사합니다. 데이터 웨어하우스 지역 중복 백업을 사용하여 다른 지역에 복원합니다. 

## <a name="what-is-a-data-warehouse-snapshot"></a>데이터 웨어하우스 스냅숏이란?
*데이터 웨어하우스 스냅숏*은 데이터 웨어하우스를 이전 상태로 복구하거나 복사하는 데 활용할 수 있는 복원 지점을 만듭니다.  SQL Data Warehouse가 분산 시스템이므로 데이터 웨어하우스 스냅숏은 Azure 저장소에 있는 여러 파일로 구성됩니다. 스냅숏은 데이터 웨어하우스에 저장된 데이터의 증분 변경 내용을 캡처합니다.

*데이터 웨어하우스 복원*은 기존 데이터 웨어하우스 또는 삭제된 데이터 웨어하우스의 복원 지점에서 만들어지는 새 데이터 웨어하우스입니다. 데이터 웨어하우스 복원은 데이터가 실수로 손상되거나 삭제된 후 데이터를 다시 만들기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다. 또한 데이터 웨어하우스는 테스트 또는 개발 용도로 데이터 웨어하우스의 복사본을 만드는 강력한 메커니즘입니다.  SQL Data Warehouse는 동일한 지역 내에서 모든 데이터 크기에 대해 20분 미만으로 측정되는 빠른 복원 메커니즘을 사용합니다. 

## <a name="automatic-restore-points"></a>자동 복원 지점
스냅숏은 복원 지점을 만드는 서비스의 기본 제공 기능입니다. 이 기능은 사용하도록 설정할 필요가 없습니다. 서비스에서 자동 복원 지점을 사용하여 복구를 위한 SLA를 유지 관리하는 사용자는 현재 이러한 복원 지점을 삭제할 수 없습니다.

SQL Data Warehouse는 하루 종일 데이터 웨어하우스의 스냅숏을 수행하여 7일 동안 사용할 수 있는 복원 지점을 만듭니다. 이 보존 기간은 변경할 수 없습니다. SQL Data Warehouse는 8시간 RPO(복구 지점 목표)를 지원합니다. 지난 7일 동안 수행된 스냅숏 중 하나에서 주 지역의 데이터 웨어하우스를 복원할 수 있습니다.

마지막 스냅숏이 시작한 시간을 보려면 온라인 SQL Data Warehouse에서 이 쿼리를 실행합니다. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>사용자 정의 복원 지점
이 기능을 사용하면 스냅숏을 수동으로 트리거하여 대규모 수정 전후의 데이터 웨어하우스 복원 지점을 만들 수 있습니다. 이 기능은 복원 지점을 논리적으로 일관되게 유지할 수 있도록 하여 작업 중단 또는 사용자 오류가 발생하는 경우 빠른 복구 시간을 위해 추가적인 데이터 보호 기능을 제공합니다. 사용자 정의 복원 지점은 7일 동안 사용할 수 있으며 자동으로 삭제됩니다. 사용자 정의 복원 지점의 보존 기간은 변경할 수 없습니다. 어떤 시점에서든 42개의 사용자 정의 복원 지점만 지원되므로 다른 복원 지점을 만들기 전에 [삭제](https://go.microsoft.com/fwlink/?linkid=875299)해야 합니다. 사용자 정의 복원 지점은 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoint?view=azurermps-6.2.0#examples) 또는 Azure Portal을 통해 스냅숏을 트리거하여 만들 수 있습니다.


> [!NOTE]
> 7일보다 더 긴 복원 시점이 필요한 경우 [여기서](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 새 데이터 웨어하우스로 복원할 수 있습니다. 복원되면 데이터 웨어하우스가 온라인 상태가 되며, 이를 무기한 일시 중지하여 계산 비용을 절감할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 저장소 비용이 부과됩니다. 복원된 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분 만에 다시 시작할 수 있습니다.
>

### <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>데이터 웨어하우스가 일시 중지된 경우 스냅숏 보존
데이터 웨어하우스가 일시 중지된 동안에는 SQL Data Warehouse에서 스냅숏을 만들지 않으며 복원 지점이 만료되지 않습니다. 데이터 웨어하우스가 일시 중지된 동안에는 복원 지점이 변경되지 않습니다. 복원 지점 보존 기간은 달력 일 수가 아니라 데이터 웨어하우스가 온라인 상태인 일 수를 기반으로 합니다.

예를 들어 스냅숏이 10월 1일 오후 4시에 시작되고 데이터 웨어하우스가 10월 3일 오후 4시에 일시 중지되는 경우 복원 지점은 최대 2일이 경과된 것입니다. 데이터 웨어하우스가 다시 온라인 상태가 되면 2일이 경과된 복원 지점으로 남아 있습니다. 데이터 웨어하우스가 10월 5일 오후 4시에 온라인 상태가 되면 복원 지점에서 2일이 경과되었고 이 복원 지점은 앞으로 5일 동안 유지됩니다.

데이터 웨어하우스가 다시 온라인 상태가 되면 SQL Data Warehouse에서 새 복원 지점을 만들기 다시 시작하고, 7일이 경과된 복원 지점은 만료됩니다.

### <a name="snapshot-retention-when-a-data-warehouse-is-dropped"></a>데이터 웨어하우스가 삭제되는 경우 스냅숏 보존
데이터 웨어하우스를 삭제하면 SQL Data Warehouse에서 최종 스냅숏을 만들어 7일 동안 저장합니다. 데이터 웨어하우스를 삭제 시 생성된 최종 복원 지점으로 복원할 수 있습니다. 

> [!IMPORTANT]
> 논리적 SQL 서버 인스턴스를 삭제하면 이 인스턴스에 속하는 모든 데이터베이스도 삭제되고 복구될 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
>

## <a name="geo-backups"></a>지역 백업
SQL Data Warehouse는 하루에 한 번 [쌍으로 연결된 데이터 센터](../best-practices-availability-paired-regions.md)로 지역 백업을 수행합니다. 지역 복원의 RPO는 24시간입니다. SQL Data Warehouse가 지원되는 다른 지역에 있는 서버로 지역 백업을 복원할 수 있습니다. 지역 백업을 사용하면 주 지역의 복원 지점에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있습니다.

지역 백업은 기본적으로 켜져 있습니다. 데이터 웨어하우스가 Gen1인 경우 원하는 대로 [옵트아웃](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)할 수 있습니다. 데이터 보호는 기본 제공으로 보증되므로 Gen2에 대한 지역 백업을 옵트아웃할 수 없습니다.

> [!NOTE]
> 지역 백업에 대해 더 짧은 RPO가 필요한 경우 [여기서](https://feedback.azure.com/forums/307516-sql-data-warehouse) 이 기능에 대해 투표해 주세요. 또한 사용자 정의 복원 지점을 만들어 새로 만든 복원 지점에서 다른 지역의 새 데이터 웨어하우스로 복원할 수 있습니다. 복원되면 데이터 웨어하우스가 온라인 상태가 되며, 이를 무기한 일시 중지하여 계산 비용을 절감할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 저장소 비용이 부과됩니다. 그런 다음, 일시 중지합니다. 데이터 웨어하우스의 활성 복사본이 필요한 경우 몇 분 만에 다시 시작할 수 있습니다.
>


## <a name="backup-and-restore-costs"></a>백업 및 복원 비용
Azure 청구서에는 Storage 및 재해 복구 저장소에 대한 항목이 있습니다. Storage 요금은 스냅숏에서 캡처된 증분 변경과 함께 주 지역에 데이터를 저장하는 데 드는 총 비용입니다. 현재 스냅숏을 수행하는 방법에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?redirectedfrom=MSDN#snapshot-billing-scenarios)를 참조하세요. 지역 중복 요금은 지역 백업을 저장하는 데 드는 비용을 포함합니다.  

주 데이터 웨어하우스 및 7일 간의 스냅숏 변경에 대한 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅숏에서 100GB를 캡처하면 2TB 데이터에 대한 비용이 Azure Premium Storage 요율로 청구됩니다. 

지역 중복 저장소를 사용하는 경우 별도의 저장소 비용이 청구됩니다. 지역 중복 저장소는 표준 RA-GRS(Read-Access Geo Redundant Storage) 요금이 청구됩니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) 및 지역 간 복원시의 [송신 요금](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조하세요.

## <a name="restoring-from-restore-points"></a>복원 지점에서 복원
각 스냅숏에서 스냅숏이 시작된 시간을 나타내는 복원 지점을 만듭니다. 데이터 웨어하우스를 복원하려면 복원 지점을 선택하고 복원 명령을 실행합니다.  

복원된 데이터 웨어하우스와 현재 데이터 웨어하우스 중 하나를 유지하거나 삭제할 수 있습니다. 현재 데이터 웨어하우스를 복원된 데이터 웨어하우스로 바꾸려면 [ALTER DATABASE(Azure SQL Data Warehouse)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse)를 MODIFY NAME 옵션과 함께 사용하여 이름을 변경할 수 있습니다. 

데이터 웨어하우스를 복원하려면 [Azure Portal을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-portal.md), [PowerShell을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-powershell.md) 또는 [T-SQL을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-rest-api.md)을 참조하세요.

삭제되거나 일시 중지된 데이터 웨어하우스를 복원하기 위해 [지원 티켓을 만들](sql-data-warehouse-get-started-create-support-ticket.md) 수 있습니다. 


## <a name="geo-redundant-restore"></a>지역 중복 복원
[데이터 웨어하우스](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region)는 선택한 성능 수준에서 SQL Data Warehouse를 지원하는 모든 지역에 복원할 수 있습니다. 

> [!NOTE]
> 지역 중복 복원을 수행하려면 이 기능에서 옵트아웃(opt out)하지 않아야 합니다.
>

## <a name="next-steps"></a>다음 단계
재해 계획에 대한 자세한 내용은 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.
