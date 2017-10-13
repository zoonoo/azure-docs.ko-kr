---
title: "Azure SQL Database 서비스 | Microsoft Docs"
description: "SQL Database 서비스 계층에 대해 자세히 알아보기"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/20/2017
ms.author: carlrab
ms.openlocfilehash: 0183e9c60da7c216a6268dfec3874eb76270f71d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="what-are-azure-sql-database-service-tiers"></a>Azure SQL Database 서비스 계층이란?

[Azure SQL Database](sql-database-technical-overview.md)는 [단일 데이터베이스](sql-database-single-database-resources.md) 및 [탄력적 풀](sql-database-elastic-pool.md) 둘 다에 대해 **기본**, **표준**, **프리미엄** 및 **프리미엄 RS** 서비스 계층을 제공합니다. 서비스 계층은 주로 다양한 성능 수준 및 저장소 크기 선택 항목, 가격으로 식별됩니다.  모든 서비스 계층에서는 성능 수준 및 저장소 크기를 유연하게 변경할 수 있습니다.  단일 데이터베이스 및 탄력적 풀은 서비스 계층, 성능 수준 및 저장소 크기에 따라 시간 단위로 청구됩니다.   

## <a name="choosing-a-service-tier"></a>서비스 계층 선택

서비스 계층을 선택하는 작업은 주로 비즈니스 연속성, 저장소 및 성능 요구 사항에 따라 다릅니다.
| | **Basic** | **Standard** |**Premium** |**Premium RS** |
| :-- | --: |--:| --:| --:| 
|대상 워크로드|개발 및 프로덕션|개발 및 프로덕션|개발 및 프로덕션|서비스 장애로 인한 데이터 손실을 최대 5분 동안 허용할 수 있는 워크로드|
|작동 시간 SLA|99.99%|99.99%|99.99%|미리 보기에 있는 동안 해당 없음|
|백업 보존|7 일|35일|35일|35일|
|CPU|낮음|낮음, 보통, 높음|보통, 높음|중간|
|IO 처리량|낮음  | 중간 | 표준보다 높음|프리미엄과 동일|
|IO 대기 시간|프리미엄보다 높음|프리미엄보다 높음|기본 및 표준보다 낮음|프리미엄과 동일|
|Columnstore 인덱싱 및 메모리 내 OLTP|해당 없음|해당 없음|지원됨|지원됨|
|||||

## <a name="performance-level-and-storage-size-limits"></a>성능 수준 및 저장소 크기 제한

성능 수준은 단일 데이터베이스에 대해서는 DTU(데이터베이스 트랜잭션 단위), 탄력적 풀에 대해서는 eDTU(탄력적 데이터베이스 트랜잭션 단위)로 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU 및 eDTU란?](sql-database-what-is-a-dtu.md)을 참조하세요.

### <a name="single-databases"></a>단일 데이터베이스

|  | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 최대 저장소 크기* | 2 GB | 1TB | 4 TB  | 1TB  |
| 최대 DTU | 5 | 3000 | 4000 | 1000 |
||||||

### <a name="elastic-pools"></a>탄력적 풀

| | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 데이터베이스당 최대 저장소 크기*  | 2 GB | 1TB | 1TB | 1TB |
| 풀당 최대 저장소 크기* | 156GB | 4 TB | 4 TB | 1TB |
| 데이터베이스당 최대 eDTU | 5 | 3000 | 4000 | 1000 |
| 풀당 최대 eDTU | 1600 | 3000 | 4000 | 1000 |
| 풀당 최대 데이터베이스 수 | 500  | 500 | 100 | 100 |
||||||

> [!IMPORTANT]
> \* 포함된 저장소보다 큰 저장소 크기는 미리 보기로 있으며 추가 비용이 적용됩니다. 자세한 내용은 [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)을 참조하세요. 
>
> \* 프리미엄 계층의 경우 현재 미국 동부 2, 미국 서부, 미국 버지니아 주 정부, 유럽 서부, 독일 중부, 동남 아시아, 일본 동부, 오스트레일리아 동부, 캐나다 중부 및 캐나다 동부 지역에서 1TB 이상의 저장소를 사용할 수 있습니다. [P11-P15 현재 제한 사항](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)을 참조하세요.  
> 

사용할 수 있는 특정 성능 수준 및 저장소 크기 옵션에 대한 자세한 내용은 [SQL Database 리소스 제한](sql-database-resource-limits.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [단일 데이터베이스 리소스](sql-database-single-database-resources.md)에 대해 자세히 알아봅니다.
- 탄력적 풀에 대한 자세한 내용은 [탄력적 풀](sql-database-elastic-pool.md)을 참조하세요.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)에 대해 자세히 알아보세요.
* [DTU 및 eDTU](sql-database-what-is-a-dtu.md)에 대해 자세히 알아봅니다.
* DTU 사용 모니터링에 대한 자세한 내용은 [모니터링 및 성능 튜닝](sql-database-troubleshoot-performance.md)을 참조하세요.

