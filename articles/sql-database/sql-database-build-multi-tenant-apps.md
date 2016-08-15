<properties
   pageTitle="Azure SQL 데이터베이스에서 격리되고 효율적인 다중 테넌트 앱 빌드"
   description="SQL 데이터베이스에서 다중 테넌트 앱을 빌드하는 방법을 알아보세요."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="07/19/2016"
   ms.author="carlrab"/>

# Azure SQL 데이터베이스에서 격리되고 효율적인 다중 테넌트 앱 빌드

## 탄력적 풀 활용 및 보다 효율적인 다중 테넌트 앱 구축

여러 고객을 처리하고 다중 테넌트 앱을 작성하는 SaaS 개발자인 경우, 종종 고객 성능, 관리 및 보안을 조정합니다. Azure SQL 데이터베이스 탄력적 데이터베이스 풀을 사용하면 그러한 조정이 필요하지 않습니다. 이러한 풀로 다중 테넌트 앱을 관리 및 모니터링하고 데이터베이스당 고객 한 명이라는 격리 이점을 얻으세요. [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

## 자동 크기 조정 제어

풀은 탄력적 데이터베이스에 대해 성능 및 저장소 용량을 즉시 자동으로 조정합니다. 풀에 할당된 성능을 제어하고 요청 시 탄력적 데이터베이스를 추가 또는 제거하며 풀의 전체 비용에 영향을 주지 않고 탄력적 데이터베이스의 성능을 정의할 수 있습니다. 즉, 개별 데이터베이스의 사용을 관리하는 데 신경을 쓸 필요가 없습니다.

[설명서 읽기](sql-database-elastic-pool.md)

## 사용자 환경의 지능적 관리

기본 제공된 크기 권장 사항은 풀을 활용할 수 있는 데이터베이스를 사전에 식별합니다. 이러한 권장 사항을 통해 성능 목표를 충족하도록 빠른 최적화를 위해 "가정" 분석을 허용합니다. 풍부한 성능 모니터링 및 문제 해결 대시보드를 통해 기록 풀 사용률을 시각화할 수 있습니다.

[설명서 읽기](sql-database-elastic-pool-guidance.md)

## 사용자 요구에 맞는 성능 및 가격

Basic, Standard 및 Premium 풀은 광범위한 성능, 저장소 및 가격 옵션을 제공합니다. 풀은 최대 400개의 탄력적 데이터베이스를 포함할 수 있습니다. 탄력적 데이터베이스는 최대 1000개의 탄력적 데이터베이스 트랜잭션 단위(eDTU)까지 자동으로 강화할 수 있습니다.

[설명서 읽기](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## 탄력적 도구

탄력적 풀 외에도 여러 데이터베이스 간에 운영 작업을 관리할 수 있는 SQL 데이터베이스 기능이 있습니다.

** 데이터베이스 간 쿼리 및 보고를 수행합니다. ** [탄력적 데이터베이스 쿼리](sql-database-elastic-query-overview.md)를 통해 탄력적 풀의 데이터베이스 간에 쿼리 또는 보고를 실행하고 풀의 여러 데이터베이스에 저장된 원격 데이터를 한 번에 액세스할 수 있습니다.

** 데이터베이스 간 트랜잭션을 실행합니다. ** [탄력적 데이터베이스 트랜잭션](sql-database-elastic-transactions-overview.md)을 통해 SQL 데이터베이스의 여러 데이터베이스에 걸쳐 있는 트랜잭션을 실행하고 작업을 수행할 수 있습니다(즉, 데이터베이스 간에 금융 거래를 처리하거나 한 데이터베이스 및 주문에서 인벤토리 업데이트).

** 여러 데이터베이스에 같은 작업을 실행합니다. ** [탄력적 데이터베이스 작업](sql-database-elastic-jobs-overview.md)은 탄력적 풀의 각 데이터베이스 간에 인덱스 다시 작성 또는 스키마 업데이트 같은 관리 작업을 실행합니다.

SQL 데이터베이스에서 제공해야 하는 다른 항목을 보려면 홈 페이지로 이동합니다. [확인해 보세요.](https://azure.microsoft.com/services/sql-database/)

## 다음 단계

[무료 Azure 구독](https://azure.microsoft.com/get-started/)을 가져오고 [첫 번째 Azure SQL 데이터베이스를 만듭니다](sql-database-get-started.md).

## 추가 리소스

모든 [SQL 데이터베이스의 기능](https://azure.microsoft.com/services/sql-database/)을 탐색합니다.
 
[SQL 데이터베이스의 기술 개요](sql-database-technical-overview.md)를 검토합니다.

<!---HONumber=AcomDC_0803_2016-->