---
title: Azure SQL Data Warehouse 백업 - 스냅숏, 지역 중복 | Microsoft Docs
description: Azure SQL Data Warehouse를 복원 지점 또는 다른 지역에 복원할 수 있는 SQL Data Warehouse 기본 제공 데이터베이스 백업에 대해 알아봅니다.
services: sql-data-warehouse
documentationcenter: ''
author: barbkess
manager: jhubbard
editor: ''
ms.assetid: b5aff094-05b2-4578-acf3-ec456656febd
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.custom: backup-restore
ms.date: 10/23/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 159a1d34caba829750da33dbc4ad403fb21cd147
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30198421"
---
# <a name="backup-and-restore-in-sql-data-warehouse"></a>SQL Data Warehouse에서 백업 및 복원
이 문서에서는 SQL Data Warehouse의 백업에 대해 구체적으로 설명합니다. 데이터 웨어하우스 백업을 사용하여 데이터베이스 스냅숏을 주 지역으로 복원하거나 지역 백업을 지역 쌍으로 연결된 지역으로 복원할 수 있습니다. 

## <a name="what-is-a-data-warehouse-backup"></a>데이터 웨어하우스 백업이란?
데이터 웨어하우스 백업은 데이터 웨어하우스를 복원하는 데 사용할 수 있는 데이터베이스 복사본입니다.  SQL Data Warehouse는 분산 시스템이므로 데이터 웨어하우스 백업은 Azure Storage에 있는 여러 파일로 구성됩니다. 데이터 웨어하우스 백업에는 로컬 데이터베이스 스냅숏과 데이터 웨어하우스와 연결된 모든 데이터베이스 및 파일의 지역 백업이 모두 포함됩니다. 

## <a name="local-snapshot-backups"></a>로컬 스냅숏 백업
SQL Data Warehouse는 온종일 데이터 웨어하우스의 스냅숏을 생성합니다. 스냅숏은 7일 동안 사용할 수 있습니다. SQL Data Warehouse는 8시간 RPO(복구 지점 목표)를 지원합니다. 주 지역의 데이터 웨어하우스를 지난 7일 내에 생성된 스냅숏 중 하나로 복원할 수 있습니다.

마지막 스냅숏이 시작한 시간을 보려면 온라인 SQL Data Warehouse에서 이 쿼리를 실행합니다. 

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs 
order by run_id desc
;
```

## <a name="geo-backups"></a>지역 백업
SQL Data Warehouse는 하루에 한 번 [쌍으로 연결된 데이터 센터](../best-practices-availability-paired-regions.md)로 지역 백업을 수행합니다. 지역 복원의 RPO는 24시간입니다. 지역 백업을 지역 쌍으로 연결된 지역의 서버로 복원할 수 있습니다. 지역 백업을 사용하면 주 지역의 스냅숏에 액세스할 수 없는 경우에 데이터 웨어하우스를 복원할 수 있습니다.

지역 백업은 기본적으로 켜져 있습니다. 데이터 웨어하우스가 탄력성에 최적화된 경우 원하는 대로 [옵트아웃](/powershell/module/azurerm.sql/set-azurermsqldatabasegeobackuppolicy)할 수 있습니다. 계산 성능 계층에 최적화된 경우에는 지역 백업을 옵트아웃할 수 없습니다.

## <a name="backup-costs"></a>백업 비용
Azure 청구서에는 Azure Premium Storage에 대한 항목과 지역 중복 스토리지에 대한 항목이 있습니다. Premium Storage 요금은 주 지역에 데이터를 저장하는 데 드는 총 비용이며, 스냅숏을 포함합니다.  지역 중복 요금은 지역 백업을 저장하는 데 드는 비용을 포함합니다.  

주 데이터 웨어하우스 및 Azure Blob 스냅숏 7일 보관에 드는 총 비용은 가장 가까운 TB로 반올림하여 계산됩니다. 예를 들어 데이터 웨어하우스가 1.5TB이고 스냅숏에서 100GB를 사용하면 Azure Premium Storage 요금으로 2TB 데이터에 대한 비용이 청구됩니다. 

> [!NOTE]
> 각 스냅숏은 처음에는 비어 있으며 주 데이터 웨어하우스를 변경할 때마다 증가합니다. 데이터 웨어하우스가 변경되면 모든 스냅숏의 크기가 커집니다. 따라서 변경 비율에 따라 저장소 비용이 증가합니다.
> 
> 

지역 중복 저장소를 사용하는 경우 별도의 저장소 비용이 청구됩니다. 지역 중복 스토리지는 표준 RA-GRS(Read-Access Geo Redundant Storage) 요금이 청구됩니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="snapshot-retention-when-a-data-warehouse-is-paused"></a>데이터 웨어하우스가 일시 중지된 경우 스냅숏 보존
SQL Data Warehouse는 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏을 만들지 않으며 데이터 웨어하우스가 만료되지 않습니다. 데이터 웨어하우스가 일시 중지된 동안에는 스냅숏 경과 시간이 변하지 않습니다. 스냅숏 보존 기간은 달력 일 수가 아닌 데이터 웨어하우스가 온라인 상태인 일 수를 기반으로 합니다.

예를 들어 스냅숏이 10월 1일 오후 4시에 시작되고 데이터 웨어하우스가 10월 3일 오후 4시에 일시 중지되면 스냅숏 경과 시간은 최대 2일입니다. 데이터 웨어하우스가 다시 온라인 상태가 되면 스냅숏 경과 시간은 2일입니다. 데이터 웨어하우스가 10월 5일 오후 4시에 온라인 상태가 되면 스냅숏 경과 시간은 2일이고 앞으로 5일 동안 유지됩니다.

데이터 웨어하우스가 다시 온라인 상태가 되면 SQL Data Warehouse는 새 스냅숏을 다시 시작하고 7일이 넘은 데이터가 있으면 스냅숏을 만료합니다.

## <a name="can-i-restore-a-dropped-data-warehouse"></a>삭제된 데이터 웨어하우스를 복원할 수 있나요?
데이터 웨어하우스를 삭제하면 SQL Data Warehouse에서 최종 스냅숏을 만들어 7일 동안 저장합니다. 데이터 웨어하우스를 삭제 시 생성된 최종 복원 지점으로 복원할 수 있습니다. 

> [!IMPORTANT]
> 논리적 SQL 서버 인스턴스를 삭제하면 이 인스턴스에 속하는 모든 데이터베이스도 삭제되고 복구될 수 없습니다. 삭제된 서버는 복원할 수 없습니다.
> 

## <a name="next-steps"></a>다음 단계
SQL Data Warehouse를 복원하려면 [SQL Data Warehouse 복원](sql-data-warehouse-restore-database-overview.md)을 참조하세요.

비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.
