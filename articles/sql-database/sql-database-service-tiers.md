---
title: Azure SQL Database 서비스 | Microsoft Docs
description: 성능 수준 및 저장소 크기를 제공하기 위한 단일 및 풀 데이터베이스에 대한 서비스 계층에 대해 알아보세요.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 03/21/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 3fe7add4ad4c5563a6307db0dec27c658913429b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층이란?

[Azure SQL Database](sql-database-technical-overview.md)는 [단일 데이터베이스](sql-database-single-database-resources.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 둘 다에 대해 **기본**, **표준** 및 **프리미엄** 서비스 계층을 제공합니다. SQL Database는 [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md#managed-instance-service-tier)에 대해 범용 서비스 계층을 제공합니다. 서비스 계층은 주로 다양한 성능 수준 및 저장소 크기 선택 항목, 가격으로 식별됩니다.  모든 서비스 계층에서는 성능 수준 및 저장소 크기를 유연하게 변경할 수 있습니다.  단일 데이터베이스 및 탄력적 풀은 서비스 계층, 성능 수준 및 저장소 크기에 따라 시간 단위로 청구됩니다.   

> [!IMPORTANT]
> 현재 공개 미리 보기로 제공되는 SQL Database 관리되는 인스턴스는 단일 범용 서비스 계층을 제공합니다. 자세한 내용은 [Azure SQL Database 관리되는 인스턴스](sql-database-managed-instance.md)를 참조하세요. 이 문서의 나머지 부분은 관리되는 인스턴스에 적용되지 않습니다.

## <a name="choosing-a-service-tier"></a>서비스 계층 선택

서비스 계층을 선택하는 작업은 주로 비즈니스 연속성, 저장소 및 성능 요구 사항에 따라 다릅니다.
| | **Basic** | **Standard** |**Premium**  |
| :-- | --: |--:| --:| --:| 
|대상 워크로드|개발 및 프로덕션|개발 및 프로덕션|개발 및 프로덕션||
|작동 시간 SLA|99.99%|99.99%|99.99%|미리 보기에 있는 동안 해당 없음|
|Backup 보존|7 일|35일|35일|
|CPU|낮음|낮음, 보통, 높음|보통, 높음|
|IO 처리량(근사치) |DTU당 2.5 IOPS  | DTU당 2.5 IOPS | DTU당 48 IOPS|
|IO 대기 시간(근사치)|5ms(읽기), 10ms(쓰기)|5ms(읽기), 10ms(쓰기)|2ms(읽기/쓰기)|
|Columnstore 인덱싱 |해당 없음|S3 이상|지원됨|
|메모리 내 OLTP|해당 없음|해당 없음|지원됨|
|||||

## <a name="performance-level-and-storage-size-limits"></a>성능 수준 및 저장소 크기 제한

성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.

### <a name="single-databases"></a>단일 데이터베이스

|  | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| 최대 저장소 크기* | 2 GB | 1TB | 4 TB  | 
| 최대 DTU | 5 | 3000 | 4000 | |
||||||

### <a name="elastic-pools"></a>탄력적 풀

| | **Basic** | **Standard** | **Premium** | 
| :-- | --: | --: | --: | --: |
| 데이터베이스당 최대 저장소 크기*  | 2 GB | 1TB | 1TB | 
| 풀당 최대 저장소 크기* | 156GB | 4 TB | 4 TB | 
| 데이터베이스당 최대 eDTU | 5 | 3000 | 4000 | 
| 풀당 최대 eDTU | 1600 | 3000 | 4000 | 
| 풀당 최대 데이터베이스 수 | 500  | 500 | 100 | 
||||||

> [!IMPORTANT]
> \* 포함된 저장소보다 큰 저장소 크기는 미리 보기로 있으며 추가 비용이 적용됩니다. 자세한 내용은 [SQL Database 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 
>
> \* 프리미엄 계층의 경우 현재 오스트레일리아 동부, 오스트레일리아 남동부, 브라질 남부, 캐나다 중부, 캐나다 동부, 미국 중부, 프랑스 중부, 독일 중부, 일본 동부, 일본 서부, 한국 중부, 미국 중북부, 북유럽, 미국 중남부, 동남 아시아, 영국 남부, 영국 서부, 미국 동부 2, 미국 서부, 미국 버지니아 주 정부 및 유럽 서부에서 1TB 이상의 저장소를 사용할 수 있습니다. [P11-P15 현재 제한 사항](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  
> 

사용할 수 있는 특정 성능 수준 및 저장소 크기 옵션에 대한 자세한 내용은 [SQL Database 리소스 제한](sql-database-resource-limits.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [단일 데이터베이스 리소스](sql-database-single-database-resources.md)에 대해 자세히 알아봅니다.
- 탄력적 풀에 대한 자세한 내용은 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
* [DTU 및 eDTU](sql-database-what-is-a-dtu.md)에 대해 자세히 알아봅니다.
* DTU 사용 모니터링에 대한 자세한 내용은 [모니터링 및 성능 튜닝](sql-database-troubleshoot-performance.md)을 참조하세요.

