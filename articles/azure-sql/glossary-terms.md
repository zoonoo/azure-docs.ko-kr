---
title: 용어집
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure VM에서 Azure SQL Database, Azure SQL Managed Instance 및 SQL로 작업하기 위한 용어 설명입니다.
services: sql-database
ms.service: sql-database
ms.subservice: service-overview
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: MashaMSFT
ms.author: mathoma
ms.reviewer: ''
ms.date: 5/18/2021
ms.openlocfilehash: 42e701a53502ce024118686f9835ddfdbc7543b6
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111413524"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 용어집
[!INCLUDE[appliesto-sqldb-sqlmi](./includes/appliesto-sqldb-sqlmi.md)]

## <a name="azure-sql-database"></a>Azure SQL Database

|Context|용어|자세한 정보|
|:---|:---|:---|
|Azure 서비스|Azure SQL Database 또는 SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|구매 모델|DTU 기반 구매 모델|[DTU 기반 구매 모델](database/service-tiers-dtu.md)|
||vCore 기반 구매 모델|[vCore 기반 구매 모델](database/service-tiers-sql-database-vcore.md)|
|배포 옵션 |단일 데이터베이스|[단일 데이터베이스](database/single-database-overview.md)|
||탄력적 풀|[탄력적 풀](database/elastic-pool-overview.md)|
|서비스 계층|기본, 표준, 프리미엄, 범용, 하이퍼스케일, 중요 비즈니스용|vCore 모델의 서비스 계층은 [SQL Database 서비스 계층](database/service-tiers-sql-database-vcore.md#service-tiers)을 참조하세요. DTU 모델의 서비스 계층은 [DTU 모델](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)을 참조하세요.|
|컴퓨팅 계층|서버리스 컴퓨팅|[서버리스 컴퓨팅](database/service-tiers-sql-database-vcore.md#compute-tiers)
||프로비저닝된 컴퓨팅|[프로비저닝된 컴퓨팅](database/service-tiers-sql-database-vcore.md#compute-tiers)
|컴퓨팅 세대|Gen5, M 시리즈, Fsv2 시리즈, DC 시리즈|[하드웨어 세대](database/service-tiers-sql-database-vcore.md#hardware-generations)
|서버 엔터티| 서버 |[논리 SQL 서버](database/logical-servers.md)|
|리소스 종류|vCore|단일 데이터베이스, 탄력적 풀에 대한 컴퓨팅 리소스에 제공된 CPU 코어입니다. |
||컴퓨팅 크기 및 스토리지 용량|컴퓨팅 크기는 단일 데이터베이스 또는 탄력적 풀에 사용할 수 있는 CPU, 메모리 및 기타 스토리지와 관련 없는 리소스의 최대 양입니다.  스토리지 크기는 단일 데이터베이스 또는 탄력적 풀에 사용할 수 있는 최대 스토리지 용량입니다. vCore 모델의 크기 조정 옵션은 [vCore 단일 데이터베이스](database/resource-limits-vcore-single-databases.md) 및 [vCore 탄력적 풀](database/resource-limits-vcore-elastic-pools.md)을 참조하세요.  (../managed-instance/resource-limits.md).  DTU 모델의 크기 조정 옵션은 [DTU 단일 데이터베이스](database/resource-limits-dtu-single-databases.md) 및 [DTU 탄력적 풀](database/resource-limits-dtu-elastic-pools.md)을 참조하세요.

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

|Context|용어|자세한 정보|
|:---|:---|:---|
|Azure 서비스|Azure SQL Managed Instance|[SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)|
|구매 모델|vCore 기반 구매 모델|[vCore 기반 구매 모델](managed-instance/service-tiers-managed-instance-vcore.md)|
|배포 옵션 |단일 인스턴스|[단일 인스턴스](managed-instance/sql-managed-instance-paas-overview.md)|
||인스턴스 풀(미리 보기)|[인스턴스 풀](managed-instance/instance-pools-overview.md)|
|서비스 계층|범용/중요 비즈니스용|[SQL Managed Instance 서비스 계층](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|컴퓨팅 계층|프로비저닝된 컴퓨팅|[프로비저닝된 컴퓨팅](managed-instance/service-tiers-managed-instance-vcore.md#compute-tiers)|
|컴퓨팅 세대|5세대|[하드웨어 세대](managed-instance/service-tiers-managed-instance-vcore.md#hardware-generations)
|서버 엔터티|관리되는 인스턴스 또는 인스턴스| SQL Managed Instance는 그 자체로 서버이므로 해당 없음 |
|리소스 종류|vCore|SQL Managed Instance에 대한 컴퓨팅 리소스에 제공된 CPU 코어입니다.|
||컴퓨팅 크기 및 스토리지 용량|컴퓨팅 크기는 SQL Managed Instance에 대한 CPU, 메모리 및 기타 스토리지와 관련 없는 리소스의 최대 양입니다.  스토리지 크기는 SQL Managed Instance에 사용할 수 있는 최대 스토리지 용량입니다.  크기 조정 옵션은 [SQL Managed Instance](managed-instance/resource-limits.md)를 참조하세요. |
