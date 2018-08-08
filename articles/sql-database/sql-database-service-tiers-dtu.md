---
title: Azure SQL Database 서비스 계층 - DTU | Microsoft Docs
description: 성능 수준 및 저장소 크기를 제공하기 위한 단일 및 풀 데이터베이스에 대한 서비스 계층에 대해 알아보세요.
services: sql-database
author: sachinpMSFT
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/01/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5d16763fc8f3331082b98216d25190b945d95b60
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411823"
---
# <a name="choosing-a-dtu-based-service-tier-performance-level-and-storage-resources"></a>DTU 기반 서비스 계층, 성능 수준 및 저장소 리소스 선택 

서비스 계층은 포함된 저장소의 고정된 양, 고정된 백업 보존 기간 및 고정 가격을 갖춘 다양한 성능 수준으로 구분됩니다. 모든 서비스 계층은 가동 중지 시간 없이 성능 수준을 변경할 수 있는 유연성을 제공합니다. 단일 데이터베이스 및 탄력적 풀은 서비스 계층 및 성능 수준에 따라 시간 단위로 청구됩니다.

> [!IMPORTANT]
> 현재 공개 미리 보기로 있는 SQL Database 관리되는 인스턴스는 DTU 기반 구매 모델을 지원하지 않습니다. 자세한 내용은 [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요. 

## <a name="choosing-a-dtu-based-service-tier"></a>DTU 기반 서비스 계층 선택

서비스 계층을 선택하는 작업은 주로 비즈니스 연속성, 저장소 및 성능 요구 사항에 따라 다릅니다.
||Basic|Standard|Premium|
| :-- | --: |--:| --:| --:| 
|대상 워크로드|개발 및 프로덕션|개발 및 프로덕션|개발 및 프로덕션||
|작동 시간 SLA|99.99%|99.99%|99.99%|미리 보기에 있는 동안 해당 없음|
|Backup 보존|7 일|35일|35일|
|CPU|낮음|낮음, 보통, 높음|보통, 높음|
|IO 처리량(근사치) |DTU당 2.5 IOPS| DTU당 2.5 IOPS | DTU당 48 IOPS|
|IO 대기 시간(근사치)|5ms(읽기), 10ms(쓰기)|5ms(읽기), 10ms(쓰기)|2ms(읽기/쓰기)|
|Columnstore 인덱싱 |해당 없음|S3 이상|지원됨|
|메모리 내 OLTP|해당 없음|해당 없음|지원됨|
|||||

## <a name="single-database-dtu-and-storage-limits"></a>단일 데이터베이스 DTU 및 저장소 제한

성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)을 참조하세요.

||Basic|Standard|Premium|
| :-- | --: | --: | --: | --: |
| 최대 저장소 크기 | 2GB | 1TB | 4 TB  | 
| 최대 DTU | 5 | 3000 | 4000 | |
||||||

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>탄력적 풀 eDTU, 저장소 및 풀링된 데이터베이스 제한

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| 데이터베이스당 최대 저장소 크기  | 2GB | 1TB | 1TB | 
| 풀당 최대 저장소 크기 | 156GB | 4 TB | 4 TB | 
| 데이터베이스당 최대 eDTU | 5 | 3000 | 4000 | 
| 풀당 최대 eDTU | 1600 | 3000 | 4000 | 
| 풀당 최대 데이터베이스 수 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> 현재 미국 중서부, 중국 동부, USDoDCentral, 독일 중부, USDoDEast, US Gov 남서부, US Gov 아이오와, 독일 북동부, 중국 북부를 제외한 모든 지역에서 1TB를 초과하는 저장소를 프리미엄 계층에 사용할 수 있습니다. 다른 지역에서 프리미엄 계층 저장소 크기는 1TB로 제한됩니다. [P11-P15 현재 제한 사항](sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  

> [!IMPORTANT]
> 경우에 따라 사용하지 않는 공간을 회수하기 위해 데이터베이스를 축소해야 할 수도 있습니다. 자세한 내용은 [Azure SQL Database의 파일 공간 관리](sql-database-file-space-management.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-performance-levels)을 참조하세요.
- 탄력적 풀에 사용할 수 있는 특정 성능 수준 및 저장소 크기를 선택하는 방법에 대한 자세한 내용은 [SQL Database DTU 기반 리소스 제한](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.
