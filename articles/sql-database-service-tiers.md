<properties
   pageTitle="SQL 데이터베이스 서비스 계층"
   description="필요에 따라 가동 중지 시간 없이 규모를 조정하는 동안 비용 및 기능의 균형을 잘 유지할 수 있도록 Azure SQL 데이터베이스 서비스 계층의 성능 및 비즈니스 연속성 기능을 비교합니다."
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="04/15/2015"
   ms.author="shkurhek"/>

# 서비스 계층

[SQL 데이터베이스](sql-database-technical-overview.md)는 기본, 표준 및 프리미엄 서비스 계층에서 사용할 수 있습니다. 3개 서비스 계층은 모두 가동 시간 [SLA](http://azure.microsoft.com/support/legal/sla/)가 99.99%이고 예측 가능한 성능, 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간당 대금 청구 기능을 제공합니다. 각 서비스 계층 내에 여러 성능 수준이 있으므로 작업 요구 사항에 가장 적합한 수준을 융통성 있게 선택할 수 있습니다. 규모를 확장 또는 축소해야 하는 경우는 응용 프로그램 가동 중지 시간 없이 Azure 포털에서 데이터베이스의 계층을 간편하게 변경할 수 있습니다. 자세한 내용은 [데이터베이스 서비스 계층 및 성능 수준 변경](https://msdn.microsoft.com/library/azure/dn369872.aspx)을 참조하세요.

> [AZURE.IMPORTANT]Web 및 Business 버전은 사용이 중지됩니다. [Web 및 Business 버전 업그레이드](sql-database-upgrade-new-service-tiers.md) 방법을 알아보세요. Web 및 Business Edition을 계속 사용하려는 경우 [지원 종료 FAQ](http://azure.microsoft.com/pricing/details/sql-database/web-business/)를 잘 읽어 보세요.

## Basic

기본 계층은 트랜잭션 작업이 적은 응용 프로그램을 위해 설계되었습니다. 일반적으로 해당 지정 시간에 단일 작업을 수행하는 작은 데이터베이스가 필요한 간단한 응용 프로그램에서 사용합니다.

**성능, 크기 및 기능**


| 서비스 계층 | Basic |
| :-- | :-- |
| DTU(데이터베이스 처리량 단위) | 5 |
| 최대 데이터베이스 크기 | 2 GB |
| PITR(지정 시간 복원) | 지난 7일 이내의 최대 밀리초 |
| 재해 복구 | 지역 복원, 모든 Azure 지역으로 복원 |
| 성능 목표 | 시간당 처리율 |


## 표준

표준 계층은 트랜잭션 작업을 시작하는 데 적합한 옵션입니다. 기본 계층과 비교하여 더 나은 성능과 기본 제공 비즈니스 연속성 기능을 제공합니다. 일반적으로 여러 동시 트랜잭션이 있는 응용 프로그램에서 사용합니다.

**성능 및 크기**


| 서비스 계층 | 표준 S0 | 표준 S1 | 표준 S2 | 표준 S3 |
| :-- | :-- | :-- | :-- | :-- |
| DTU(데이터베이스 처리량 단위) | 10 | 20 | 50 | 100 |
| 최대 데이터베이스 크기 | 250GB | 250GB | 250GB | 250GB |


**기능**


| 서비스 계층 | 표준(S0, S1, S2, S3) |
| :-- | :-- |
| PITR(지정 시간 복원) | 지난 14일 이내의 최대 밀리초 |
| 재해 복구 | 표준 지역에서 복제, 1개 오프라인 보조 복제본 |
| 성능 목표 | 분당 처리율 |


## Premium

프리미엄 계층은 업무 수행에 핵심적인 응용 프로그램을 위해 설계되었습니다. 사용자가 선택 가능한 최대 4개 Azure 지역에서 활성 지역 복제를 비롯하여, 가장 높은 성능 수준과 고급 비즈니스 연속성 기능에 대한 액세스를 제공합니다. 일반적으로 트랜잭션 볼륨이 높고 동시 사용자 수가 많은 업무 수행에 핵심적인 응용 프로그램에서 사용합니다.

**성능 및 크기**


| 서비스 계층 | 프리미엄 P1 | 프리미엄 P2 | 프리미엄 P3 |
| :-- | :-- | :-- | :-- |
| DTU(데이터베이스 처리량 단위) | 125 | 250 | 1000 |
| 최대 데이터베이스 크기 | 500GB | 500GB | 500GB |


**기능**


| 서비스 계층 | 프리미엄(P1, P2, P3) |
| :-- | :-- |
| PITR(지정 시간 복원) | 지난 35일 이내의 최대 밀리초 |
| 재해 복구 | 활성 지역 복제, 최대 4개의 온라인 읽기 가능 보조 복제본 |
| 성능 목표 | 초당 처리율 |


[SQL 데이터베이스 가격](http://azure.microsoft.com/pricing/details/sql-database/)에서 이러한 계층의 가격에 대해 자세히 알아보세요.

SQL 데이터베이스 계층에 대해 알아 보았으면 [무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 사용해보고 [첫 번째 SQL 데이터베이스를 만드는 방법](sql-database-get-started.md)에 대해 알아보세요!

<!---HONumber=58-->