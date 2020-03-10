---
title: DTU에서 vCore로 마이그레이션
description: DTU 모델에서 vCore 모델로 마이그레이션합니다. VCore로 마이그레이션하는 것은 표준 계층과 프리미엄 계층 간에 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 693065046f92e0e9eade14c43e9942772440937d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945409"
---
# <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>DTU 기반 모델에서 vCore 기반 모델로 마이그레이션

## <a name="migrate-a-database"></a>데이터베이스 마이그레이션

Dtu 기반 구매 모델에서 vCore 기반 구매 모델로 데이터베이스를 마이그레이션하는 것은 DTU 기반 구매 모델에서 standard 및 premium 서비스 계층 간에 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다.

## <a name="migrate-databases-that-use-geo-replication"></a>지역에서 복제를 사용 하는 데이터베이스 마이그레이션

DTU 기반 모델에서 vCore 기반 구매 모델로 마이그레이션하는 것은 standard 및 premium 서비스 계층에 있는 데이터베이스 간의 지역에서 복제 관계를 업그레이드 하거나 다운 그레이드 하는 것과 비슷합니다. 마이그레이션 중에 지역에서 복제를 중지할 필요는 없지만 다음 시퀀싱 규칙을 따라야 합니다.

- 업그레이드하는 경우 보조 데이터베이스를 먼저 업그레이드한 다음, 주 데이터베이스를 업그레이드해야 합니다.
- 다운그레이드하는 경우 반대 순서로 주 데이터베이스를 먼저 다운그레이드한 다음, 보조 데이터베이스를 다운그레이드해야 합니다.

두 탄력적 풀 간의 지역에서 복제를 사용 하는 경우 하나의 풀을 기본 풀로 지정 하 고 다른 풀을 보조 데이터베이스로 지정 하는 것이 좋습니다. 이 경우 탄력적 풀을 마이그레이션하는 경우 동일한 시퀀싱 지침을 사용 해야 합니다. 그러나 주 데이터베이스와 보조 데이터베이스를 모두 포함 하는 탄력적 풀을 사용 하는 경우 사용률이 높은 풀을 주 데이터베이스로 처리 하 고 그에 따라 시퀀싱 규칙을 따릅니다.  

다음 표에서는 특정 마이그레이션 시나리오에 대 한 지침을 제공 합니다.

|현재 서비스 계층|대상 서비스 계층|마이그레이션 유형|사용자 작업|
|---|---|---|---|
|Standard|범용 가상 컴퓨터|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Premium|중요 비즈니스용|수평|순서에 관계없이 마이그레이션할 수 있지만 적절한 vCore 크기 조정을 보장해야 합니다.*|
|Standard|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|Standard|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|Premium|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|Premium|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
|중요 비즈니스용|범용 가상 컴퓨터|다운그레이드|먼저 주 데이터베이스를 마이그레이션해야 합니다.|
|범용 가상 컴퓨터|중요 비즈니스용|업그레이드|먼저 보조 데이터베이스를 마이그레이션해야 합니다.|
||||

경험에 \* 표준 계층의 모든 100 Dtu에는 vCore가 하나 이상 필요 하며, 프리미엄 계층의 모든 125 Dtu에는 vCore가 1 개 이상 필요 합니다. 자세한 내용은 [vCore 기반 구매 모델](https://docs.microsoft.com/azure/sql-database/sql-database-purchase-models#vcore-based-purchasing-model)을 참조하세요.

## <a name="migrate-failover-groups"></a>장애 조치 (failover) 그룹 마이그레이션

여러 데이터베이스가 있는 장애 조치 그룹을 마이그레이션하려면 주 데이터베이스와 보조 데이터베이스를 개별적으로 마이그레이션해야 합니다. 이 과정에서 동일한 고려 사항과 순서 지정 규칙이 적용됩니다. 데이터베이스를 vCore 기반 구매 모델로 변환한 후에는 장애 조치 (failover) 그룹이 동일한 정책 설정에 계속 적용 됩니다.

### <a name="create-a-geo-replication-secondary-database"></a>지역에서 복제 보조 데이터베이스 만들기

주 데이터베이스에 사용한 것과 동일한 서비스 계층을 사용 하 여 지역에서 복제 보조 데이터베이스 (지역 보조 데이터베이스)를 만들 수 있습니다. 로그 생성 비율이 높은 데이터베이스의 경우 주 데이터베이스와 동일한 계산 크기로 지역 보조 데이터베이스를 만드는 것이 좋습니다.

단일 주 데이터베이스에 대 한 탄력적 풀에서 지역 보조 데이터베이스를 만드는 경우 풀에 대 한 `maxVCore` 설정이 주 데이터베이스의 계산 크기와 일치 하는지 확인 합니다. 다른 탄력적 풀에서 주 데이터베이스에 대 한 지역 보조 데이터베이스를 만드는 경우 풀에 동일한 `maxVCore` 설정 하는 것이 좋습니다.

## <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>데이터베이스 복사본을 사용 하 여 DTU 기반 데이터베이스를 vCore 기반 데이터베이스로 변환

대상 컴퓨팅 크기가 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 경우 제한 또는 특별한 순서 지정 없이 DTU 기반 컴퓨팅 크기의 데이터베이스를 vCore 기반 컴퓨팅 크기의 데이터베이스에 복사할 수 있습니다. 데이터베이스 복사본은 복사 작업을 시작할 때 데이터의 스냅숏을 만들고 원본과 대상 간의 데이터를 동기화 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목은 [단일 데이터베이스에 대 한 vCore 기반 리소스 제한 SQL Database](sql-database-vcore-resource-limits-single-databases.md)을 참조 하세요.
- 탄력적 풀에 사용할 수 있는 특정 계산 크기 및 저장소 크기 선택 항목에 대해서는 [탄력적 풀에 대 한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md)을 참조 하세요.
