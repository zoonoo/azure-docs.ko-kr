---
title: "Azure SQL Data Warehouse 백업 - 스냅숏, 지역 중복 | Microsoft Docs"
description: "Azure SQL Data Warehouse를 복원 지점 또는 다른 지역에 복원할 수 있는 SQL Data Warehouse 기본 제공 데이터베이스 백업에 대해 알아봅니다."
services: sql-data-warehouse
documentationcenter: 
author: lakshmi1812
manager: jhubbard
editor: 
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 94b92f05af30734de727a12fd99271aa9769723a
ms.lasthandoff: 01/24/2017


---
# <a name="sql-data-warehouse-backups"></a>SQL Data Warehouse 백업
SQL Data Warehouse는 데이터 웨어하우스 백업 기능의 일부로 로컬 및 지리적 백업을 제공합니다. 여기에는 Azure Storage Blob 스냅숏 및 지역 중복 저장소가 포함됩니다. 데이터 웨어하우스 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원하거나 다른 지리적 지역으로 복원하세요. 이 문서에서는 SQL Data Warehouse의 백업에 대해 구체적으로 설명합니다.

## <a name="what-is-a-data-warehouse-backup"></a>데이터 웨어하우스 백업이란?
데이터 웨어하우스 백업이란 데이터 웨어하우스를 특정 시점으로 복원하는 데 사용할 수 있는 데이터입니다.  SQL Data Warehouse는 분산 시스템이므로 Azure blob에 저장된 여러 파일이 데이터 웨어하우스 백업을 구성합니다. 

데이터베이스 백업은 실수로 손상되거나 삭제되지 않도록 데이터를 보호해 주기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.. 자세한 내용은 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.

## <a name="data-redundancy"></a>데이터 중복
SQL Data Warehouse는 데이터를 로컬 중복(LRS) Azure Premium Storage에 저장하여 데이터를 보호합니다. 이 Azure Storage 기능은 데이터의 여러 동기 복사본을 로컬 데이터 센터에 저장하여 지역적 오류 발생 시 투명한 데이터 보호를 보장합니다. 데이터 중복을 사용하면 데이터는 디스크 오류 등의 인프라 문제를 감당할 수 있습니다. 데이터 중복은 내결함성이 있는 비즈니스 연속성 및 고가용성 인프라를 보장합니다.

다음에 대한 자세한 정보:

* Azure Premium Storage에 대한 자세한 내용은 [Azure Premium Storage 소개](../storage/storage-premium-storage.md)를 참조하세요.
* 로컬 중복 저장소에 대한 자세한 내용은 [Azure Storage 복제](../storage/storage-redundancy.md#locally-redundant-storage)를 참조하세요.

## <a name="azure-storage-blob-snapshots"></a>Azure Storage Blob 스냅숏
Azure Premium Storage의 장점으로, SQL Data Warehouse는 Azure Storage Blob 스냅숏을 사용하여 데이터 웨어하우스를 로컬에 백업합니다. 데이터 웨어하우스를 스냅숏 복원 지점으로 복원할 수 있습니다. 스냅숏은 최소&8;시간마다 시작되며&7;일 동안 사용할 수 있습니다.  

다음에 대한 자세한 정보:

* Azure blob 스냅숏에 대한 자세한 내용은 [blob 스냅숏 만들기](../storage/storage-blob-snapshots.md)를 참조하세요.

## <a name="geo-redundant-backups"></a>지역 중복 저장소
SQL Data Warehouse는 24시간마다 전체 데이터 웨어하우스를 표준 저장소에 저장합니다. 전체 데이터 웨어하우스는 마지막 스냅숏 시간과 일치하도록 생성됩니다. 표준 저장소는 읽기 액세스(RA-GRS) 권한이 있는 지역 중복 저장소 계정에 소속됩니다. Azure Storage RA-GRS 기능은 [쌍을 이루는 데이터 센터](../best-practices-availability-paired-regions.md)에 백업을 복제합니다. 이러한 지역에서 복제는 주 지역의 스냅숏에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있도록 해 줍니다. 

이 기능은 기본적으로 켜져 있습니다. 지역 중복 저장소를 사용하지 않으려면 [선택] (https://docs.microsoft.com/powershell/resourcemanager/Azurerm.sql/v2.1.0/Set-AzureRmSqlDatabaseGeoBackupPolicy?redirectedfrom=msdn)할 수 있습니다. 

> [!NOTE]
> Azure Storage에서 *복제* 라는 용어는 한 위치에서 다른 위치로 파일을 복사하는 것을 말합니다. SQL의 *데이터베이스 복제* 는 주 데이터베이스와 동기화된 다수의 보조 데이터베이스를 유지하는 것을 말합니다. 
> 
> 

다음에 대한 자세한 정보:

* 지역 중복 저장소는 [Azure Storage 복제](../storage/storage-redundancy.md)를 참조하세요.
* RA-GRS 저장소는 [읽기 액세스 지역 중복 저장소](../storage/storage-redundancy.md#read-access-geo-redundant-storage)를 참조하세요.

## <a name="data-warehouse-backup-schedule-and-retention-period"></a>데이터 웨어하우스 백업 일정 및 보존 기간
SQL Data Warehouse는&4;~8시간마다 온라인 데이터 웨어하우스에 스냅숏을 만들고 각 스냅숏을&7;일 동안 유지합니다. 온라인 데이터베이스를 지난&7;일의 복원 지점 중 하나로 복원할 수 있습니다. 

마지막 스냅숏이 시작한 시간을 보려면 온라인 SQL Data Warehouse에서 이 쿼리를 실행합니다. 

```sql
select top 1 *
from sys.pdw_loader_backup_runs 
order by run_id desc;
```

스냅숏을&7;일 이상 보존해야 하는 경우 복원 지점을 새 데이터 웨어하우스로 복원하면 됩니다. 복원이 완료되면 SQL Data Warehouse가 새 데이터 웨어하우스에 스냅숏 만들기를 시작합니다. 새 데이터 웨어하우스를 변경하지 않으면 스냅숏이 빈 상태로 유지되고, 따라서 스냅숏 비용이 최소화됩니다. SQL Data Warehouse가 스냅숏을 만들지 못하게 데이터베이스를 일시 중지할 수도 있습니다.

### <a name="what-happens-to-my-backup-retention-while-my-data-warehouse-is-paused"></a>데이터 웨어하우스가 일시 중지된 동안 백업 보존은 어떻게 되나요?
SQL Data Warehouse는 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏을 만들지 않으며 데이터 웨어하우스가 만료되지 않습니다. 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏 경과 시간이 변하지 않습니다. 스냅숏 보존 기간은 달력 일 수가 아닌 데이터 웨어하우스가 온라인 상태인 일 수를 기반으로 합니다.

예를 들어 스냅숏이 10월 1일 오후 4시에 시작되고 데이터 웨어하우스가 10월 3일 오후 4시에 일시 중지되면 스냅샷 경과 시간은 2일입니다. 데이터 웨어하우스가 언제 다시 온라인 상태가 되더라도 스냅샷 경과 시간은&2;일입니다. 데이터 웨어하우스가 10월 5일 오후 4시에 온라인 상태가 되면 스냅숏 경과 시간은 2일이고 앞으로 5일 동안 유지됩니다.

데이터 웨어하우스가 다시 온라인 상태가 되면 SQL Data Warehouse는 새 스냅숏을 다시 시작하고&7;일이 넘은 데이터가 있으면 스냅숏을 만료합니다.

### <a name="how-long-is-the-retention-period-for-a-dropped-data-warehouse"></a>삭제된 데이터 웨어하우스의 보존 기간은 얼마입니까?
데이터 웨어하우스가 삭제되면 데이터 웨어하우스 및 스냅숏은&7;일 동안 저장된 후 제거됩니다. 저장된 아무 복원 지점으로 데이터 웨어하우스를 복원할 수 있습니다.

> [!IMPORTANT]
> 논리적 SQL 서버 인스턴스를 삭제하면 이 인스턴스에 속하는 모든 데이터베이스도 삭제되고 복구될 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
> 
> 

## <a name="data-warehouse-backup-costs"></a>데이터 웨어하우스 백업 비용
주 데이터 웨어하우스 및 Azure Blob 스냅숏&7;일 보관에 드는 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅숏에서 100GB를 사용하면 Azure Premium Storage 요금으로 2TB 데이터에 대한 비용이 청구됩니다. 

> [!NOTE]
> 각 스냅숏은 처음에는 비어 있으며 주 데이터 웨어하우스를 변경할 때마다 증가합니다. 데이터 웨어하우스가 변경되면 모든 스냅숏의 크기가 커집니다. 따라서 변경 비율에 따라 저장소 비용이 증가합니다.
> 
> 

지역 중복 저장소를 사용하는 경우 별도의 저장소 비용이 청구됩니다. 지역 중복 저장소는 표준 RA-GRS(읽기 액세스 지리 중복 저장소) 요금이 청구됩니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="using-database-backups"></a>데이터베이스 백업 사용
SQL Data Warehouse 백업의 주요 용도는 데이터 웨어하우스를 보존 기간 내 복원 지점 중 하나로 복원하는 것입니다.  

* SQL Data Warehouse를 복원하려면 [SQL Data Warehouse 복원](sql-data-warehouse-restore-database-overview.md)을 참조하세요.

## <a name="related-topics"></a>관련된 항목
### <a name="scenarios"></a>시나리오
* 비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.

<!-- ### Tasks -->

* 데이터 웨어하우스를 복원하려면 [SQL Data Warehouse 복원](sql-data-warehouse-restore-database-overview.md)을 참조하세요.

<!-- ### Tutorials -->


