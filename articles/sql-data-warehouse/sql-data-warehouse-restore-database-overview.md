---
title: "Azure 데이터 웨어하우스 - 로컬 및 지역 중복 복원 | Microsoft Docs"
description: "Azure SQL 데이터 웨어하우스의 데이터베이스를 복구하기 위한 데이터베이스 복원 옵션 개요입니다."
services: sql-data-warehouse
documentationcenter: NA
author: Lakshmi1812
manager: jhubbard
editor: 
ms.assetid: 3e01c65c-6708-4fd7-82f5-4e1b5f61d304
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 10/31/2016
ms.author: lakshmir;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 43ab6a2f71ab51c50847b1ba5249f51c48e03fea
ms.openlocfilehash: 104986e88ededf2137725fe258b6ce51f608b37d
ms.lasthandoff: 01/24/2017


---
# <a name="sql-data-warehouse-restore"></a>SQL Data Warehouse 복원
> [!div class="op_single_selector"]
> * [개요][Overview]
> * [포털][Portal]
> * [PowerShell][PowerShell]
> * [REST (영문)][REST]
> 
> 

SQL Data Warehouse는 데이터 웨어하우스 재해 복구 기능의 일부로 로컬 및 지리적 복원을 제공합니다. 데이터 웨어하우스 백업을 사용하여 데이터 웨어하우스를 주 지역의 복원 지점으로 복원하거나 지역 중복 백업을 사용하여 다른 지리적 지역으로 복원하세요. 이 문서에서는 데이터 웨어하우스 복원에 대해 구체적으로 설명합니다.

## <a name="what-is-a-data-warehouse-restore"></a>데이터 웨어하우스 복원이란?
데이터 웨어하우스 복원은 기존 데이터 웨어하우스 또는 삭제된 데이터 웨어하우스의 백업에서 생성되는 새 데이터 웨어하우스입니다. 복원된 데이터 웨어하우스는 특정 시점에 백업된 데이터 웨어하우스를 다시 만듭니다. SQL Data Warehouse는 분산 시스템이므로 Azure blob에 저장된 여러 백업 파일에서 데이터 웨어하우스 복원이 생성됩니다. 

데이터베이스 복원은 실수로 손상되거나 삭제된 후 데이터를 다시 만들기 때문에 비즈니스 연속성 및 재해 복구 전략의 필수적인 부분입니다.

자세한 내용은 다음을 참조하세요.

* [SQL Data Warehouse 백업](sql-data-warehouse-backups.md)
* [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)

## <a name="data-warehouse-restore-points"></a>데이터 웨어하우스 복원 지점
Azure Premium Storage의 장점으로, SQL Data Warehouse는 Azure Storage Blob 스냅숏을 사용하여 기본 데이터 웨어하우스를 백업합니다. 각 스냅숏에는 스냅숏이 시작된 시간을 나타내는 복원 지점이 있습니다. 데이터 웨어하우스를 복원하려면 복원 지점을 선택하고 복원 명령을 실행합니다.  

SQL Data Warehouse는 항상 새 데이터 웨어하우스에 백업을 복원합니다. 복원된 데이터 웨어하우스와 현재 데이터 웨어하우스 중 하나를 유지하거나 삭제할 수 있습니다. 현재 데이터 웨어하우스를 복원된 데이터 웨어하우스로 대체하려는 경우 이름을 바꿀 수 있습니다.

삭제되거나 일시 중지된 데이터 웨어하우스를 복원해야 하는 경우 [지원 티켓을 만들](sql-data-warehouse-get-started-create-support-ticket.md) 수 있습니다. 

<!-- 
### Can I restore a deleted data warehouse?

Yes, you can restore the last available restore point.

Yes, for the next seven calendar days. When you delete a data warehouse, SQL Data Warehouse actually keeps the data warehouse and its snapshots for seven days just in case you need the data. After seven days, you won't be able to restore to any of the restore points. -->

## <a name="geo-redundant-restore"></a>지역 중복 복원
지역 중복 저장소를 사용하는 경우 다른 지역에 [쌍으로 구성된 데이터 센터](../best-practices-availability-paired-regions.md)에 데이터 웨어하우스를 복원할 수 있습니다. 데이터 웨어하우스는 마지막 일일 백업에서 복원됩니다. 

## <a name="restore-timeline"></a>복원 타임라인
지난&7;일 이내의 사용 가능한 복원 지점으로 데이터베이스를 복원할 수 있습니다. 스냅숏은&4;~8시간마다 시작되며&7;일 동안 사용할 수 있습니다. 7일보다 오래된 스냅숏은 만료되고 해당 복원 지점을 더 이상 사용할 수 없게 됩니다.

## <a name="restore-costs"></a>복원 비용
복원된 데이터 웨어하우스에 대한 저장소 비용은 Azure Premium Storage 요금으로 청구됩니다. 

복원된 데이터 웨어하우스를 일시 중지하면 저장소에 Azure Premium Storage 요금이 부과됩니다. 일시 중지의 장점은 DWU 컴퓨팅 리소스에 대한 요금이 부과되지 않는다는 점입니다.

SQL Data Warehouse 가격 책정에 대한 자세한 내용은 [SQL Data Warehouse 가격 책정](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)을 참조하세요.

## <a name="uses-for-restore"></a>복원의 용도
데이터 웨어하우스 복원의 주 용도는 실수로 인한 데이터 손실 또는 손상 후 데이터를 복구하는 것입니다.

백업을&7;일 넘게 보존하는 데에도 데이터 웨어하우스 복원을 사용할 수 있습니다. 백업이 복원되면 데이터 웨어하우스가 온라인 상태가 되며 계산 비용을 절감하기 위해 이를 무기한 일시 중지할 수 있습니다. 일시 중지된 데이터베이스에는 Azure Premium Storage 요금으로 저장소 비용이 부과됩니다. 

## <a name="related-topics"></a>관련된 항목
### <a name="scenarios"></a>시나리오
* 비즈니스 연속성을 대략적으로 이해하려면 [비즈니스 연속성 개요](../sql-database/sql-database-business-continuity.md)를 참조하세요.

<!-- ### Tasks -->

데이터 웨어하우스 복원을 수행하려면 다음을 사용하여 복원합니다.

* Azure Portal의 경우 [Azure Portal을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-portal.md)을 참조하세요.
* PowerShell cmdlet의 경우 [PowerShell cmdlet을 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-powershell.md)을 참조하세요.
* REST API의 경우 [REST API를 사용하여 데이터 웨어하우스 복원](sql-data-warehouse-restore-database-rest-api.md)을 참조하세요.

<!-- ### Tutorials -->

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md

<!--MSDN references-->


<!--Other Web references-->

