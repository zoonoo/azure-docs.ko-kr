---
title: 용어집
titleSuffix: Azure SQL
description: Azure VM에서 Azure SQL Database, Azure SQL Managed Instance 및 SQL로 작업 하기 위한 용어 설명입니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/22/2020
ms.openlocfilehash: f05871c41e2deec3f6a52446844c0b8fd2c9038d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84220807"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 용어집
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Context|용어|추가 정보|
|:---|:---|:---|
|Azure 서비스|Azure SQL Database 또는 SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|구매 모델|DTU 기반 구매 모델|[DTU 기반 구매 모델](database/service-tiers-dtu.md)|
||vCore 기반 구매 모델|[vCore 기반 구매 모델](database/service-tiers-vcore.md)|
|배포 옵션 |단일 데이터베이스|[단일 데이터베이스](database/single-database-overview.md)|
||탄력적 풀|[탄력적 풀](database/elastic-pool-overview.md)|
|서비스 계층|Basic, Standard, Premium, 범용, Hyperscale, 중요 비즈니스용|VCore 모델의 서비스 계층은 [SQL Database 서비스 계층](database/service-tiers-vcore.md#service-tiers)을 참조 하세요. DTU 모델의 서비스 계층은 [dtu 모델](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)을 참조 하세요.|
|컴퓨팅 계층|서버리스 컴퓨팅|[서버리스 컴퓨팅](database/service-tiers-vcore.md#compute-tiers)
||프로비저닝된 컴퓨팅|[프로비저닝된 컴퓨팅](database/service-tiers-vcore.md#compute-tiers)
|컴퓨팅 세대|Gen5, M 시리즈, Fsv2 시리즈|[하드웨어 생성](database/service-tiers-vcore.md#hardware-generations)
|서버 엔터티| 서버 |[논리 SQL 서버](database/logical-servers.md)|
|리소스 유형|vCore|단일 데이터베이스 탄력적 풀에 대 한 계산 리소스에 제공 된 CPU 코어입니다. |
||계산 크기 및 저장소 크기|계산 크기는 단일 데이터베이스 또는 탄력적 풀에 사용할 수 있는 CPU, 메모리 및 기타 저장소와 관련 없는 리소스의 최대 양입니다.  저장소 크기는 단일 데이터베이스 또는 탄력적 풀에 사용할 수 있는 최대 저장소 크기입니다. Vcore 모델의 크기 조정 옵션은 [vcore 단일 데이터베이스](database/resource-limits-vcore-single-databases.md)및 [vcore 탄력적 풀](database/resource-limits-vcore-elastic-pools.md)을 참조 하세요.  (.. /managed-instance/resource-limits.md).  DTU 모델의 크기 조정 옵션에 대해서는 [dtu 단일 데이터베이스](database/resource-limits-dtu-single-databases.md) 및 [dtu 탄력적 풀](database/resource-limits-dtu-elastic-pools.md)을 참조 하세요.

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

|Context|용어|추가 정보|
|:---|:---|:---|
|Azure 서비스|Azure SQL Managed Instance|[SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)|
|구매 모델|vCore 기반 구매 모델|[vCore 기반 구매 모델](database/service-tiers-vcore.md)|
|배포 옵션 |단일 인스턴스|[단일 인스턴스](managed-instance/sql-managed-instance-paas-overview.md)|
||인스턴스 풀 (미리 보기)|[인스턴스 풀](managed-instance/instance-pools-overview.md)|
|서비스 계층|범용, 중요 비즈니스용|[SQL Managed Instance 서비스 계층](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|컴퓨팅 계층|프로비저닝된 컴퓨팅|[프로비저닝된 컴퓨팅](database/service-tiers-vcore.md#compute-tiers)|
|컴퓨팅 세대|5세대|[하드웨어 생성](database/service-tiers-vcore.md#hardware-generations)
|서버 엔터티|관리 되는 인스턴스 또는 인스턴스| SQL Managed Instance 서버 자체에 있는 해당 없음 |
|리소스 유형|vCore|SQL Managed Instance에 대 한 계산 리소스에 제공 된 CPU 코어입니다.|
||계산 크기 및 저장소 크기|계산 크기는 SQL Managed Instance에 대 한 CPU, 메모리 및 기타 저장소가 아닌 관련 리소스의 최대 양입니다.  저장소 크기는 SQL Managed Instance에 사용할 수 있는 최대 저장소 크기입니다.  크기 조정 옵션의 경우 [SQL 관리 되는 인스턴스](managed-instance/resource-limits.md)를 선택 합니다. |

## <a name="sql-on-azure-vm"></a>Azure VM의 SQL

여기에서 더 많은 내용이 필요 합니다.
