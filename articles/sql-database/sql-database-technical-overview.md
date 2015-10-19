<properties
   pageTitle="SQL 데이터베이스 정의 | Microsoft Azure"
   description="클라우드 환경에서 Azure SQL 데이터베이스, Microsoft의 관계형 데이터베이스 관리 시스템(RDBMS) 및 PaaS 솔루션의 기능과 기술 세부 정보를 살펴봅니다."
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
   ms.date="09/30/2015"
   ms.author="shkurhek"/>

# SQL 데이터베이스 소개

SQL 데이터베이스는 시장 선도적인 Microsoft SQL Server 엔진을 기반으로 한 클라우드 환경의 관계형 데이터베이스 서비스로, 업무 수행에 중요한 기능을 제공합니다. SQL 데이터베이스는 예측 가능한 성능, 가동 중지 시간이 없는 확장성, 비즈니스 연속성, 데이터 보호 기능을 제공하며 관리할 필요가 거의 없습니다. 개발자는 가상 컴퓨터와 인프라 관리가 아닌 빠른 앱 개발 및 제품 출시 기간 단축에 전념할 수 있습니다. SQL 데이터베이스는 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 엔진을 기반으로 하여 기존 SQL Server 도구, 라이브러리 및 API를 지원하므로 클라우드로 더 쉽게 전환 및 확장할 수 있습니다.

이 문서에서는 세부 정보를 찾는 링크를 통해 성능, 확장성 및 관리 효율성과 관련된 SQL 데이터베이스의 핵심 개념과 기능을 소개합니다. 이동할 준비가 된 경우 바로 [먼저 SQL 데이터베이스를 만들거나](sql-database-get-started.md) [탄력적 데이터베이스 풀을 만들 수](sql-database-elastic-pool-portal.md) 있습니다. 자세히 알아보려면 30분짜리 비디오를 보세요.


> [AZURE.VIDEO azurecon-2015-get-started-with-azure-sql-database]


## 가동 중지 시간 없이 성능 및 규모 조정
SQL 데이터베이스는 Basic, Standard 및 Premium *서비스 계층*에서 사용할 수 있습니다. 각 서비스 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 [다양한 수준의 성능 및 기능](sql-database-service-tiers.md)을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스용 첫 번째 앱을 만든 다음 앱을 가상 환경으로 전환할 때 언제든지 앱 또는 고객에 대한 가동 중지 시간 없이 수동으로 또는 프로그램에 의해 [서비스 계층을 변경](sql-database-scale-up.md)할 수 있습니다.

많은 업무와 앱에서, 특히 사용 패턴이 비교적 예측 가능한 경우 데이터베이스를 만들고 단일 데이터베이스 성능을 확장하거나 축소할 수 있으면 충분합니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다.

SQL 데이터베이스의 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)로 이 문제를 해결합니다. 개념은 간단합니다. 성능을 풀에 할당하고 단일 데이터베이스 성능이 아닌 풀의 집합적 성능에 대해 지불합니다. 데이터베이스 성능을 확장하거나 축소할 필요가 없습니다. *탄력적 데이터베이스*라는 풀의 데이터베이스가 요구 사항에 맞게 자동으로 확장 및 축소됩니다. 탄력적 데이터베이스는 풀의 한계를 사용하지만 초과하지 않으므로 데이터베이스 사용량을 예측할 수 없는 경우에도 비용을 계속 예측할 수 있습니다. 뿐만 아니라 [풀에 데이터베이스를 추가 및 제거](sql-database-elastic-pool-portal.md)하여 소수의 데이터베이스에서 수천 개의 데이터베이스까지 자신이 관리하는 예산 범위 내에서 앱의 규모를 조정할 수 있습니다.

— 단일이든 또는 탄력적이든 — 구애 받지 않습니다. 단일 데이터베이스를 탄력적 데이터베이스 풀과 혼합하고 단일 데이터베이스와 풀의 서비스 계층을 변경하여 혁신적인 디자인을 만들 수 있습니다. 또한 Azure의 성능 및 도달 범위를 바탕으로 Azure 서비스를 SQL 데이터베이스와 조합하여 사용하면 고유한 현대적 응용 프로그램 설계 요구를 충족시키고, 비용 및 리소스를 효율적으로 운용하고, 새로운 비즈니스 기회를 만들 수 있습니다.

하지만 데이터베이스와 데이터베이스 풀의 상대적 성능을 비교하려면 어떻게 해야 합니까? 확장 및 축소할 때 마우스 오른쪽 단추 클릭이 정지하는 것을 어떻게 알 수 있습니까? 단일 데이터베이스에 데이터베이스 트랜잭션 장치를 선택하고 탄력적 데이터베이스와 데이터베이스 풀에 탄력적 DTU(eDTU)를 선택하는 것이 답입니다.

## DTU 이해

데이터베이스 트랜잭션 단위(DTU)는 SQL 데이터베이스에서 실제 측정: 데이터베이스 트랜잭션을 기반으로 데이터베이스의 상대적 위력을 보여 주는 측정 단위입니다. OLTP(온라인 트랜잭션 처리) 요청에 대표적인 작업 집합을 선택한 다음 완전히 로드된 상태에서 초당 완료할 수 있는 트랜잭션 수를 측정했습니다(이는 단축 버전이며, [벤치마크 개요](https://msdn.microsoft.com/library/azure/dn741327.aspx)에서 복잡한 세부 정보를 읽을 수 있음).

기본 데이터베이스에는 DTU 5개가 있습니다. 즉, 초당 트랜잭션 5개를 완료할 수 있으며, 프리미엄 P11 데이터베이스에는 DTU 1750개가 있습니다.

![단일 데이터베이스 DTU](./media/sql-database-technical-overview/single_db_dtus.png)

단일 데이터베이스에 대한 DTU는 탄력적 데이터베이스에 대한 eDTU로 직접 변환됩니다. 예를 들어 기본 탄력적 데이터베이스 풀의 데이터베이스는 최대 5개의 eDTUs를 제공합니다. 이는 단일 기본 데이터베이스와 같은 성능입니다. 차이점은 탄력적 데이터베이스가 필요할 때까지 풀에서 eDTUs를 사용하지 않는다는 것입니다.

![탄력적 풀 및 eDTU](./media/sql-database-technical-overview/sqldb_elastic_pools.png)

참고로 간단한 예를 살펴보겠습니다. DTU가 1000개인 기본 탄력적 데이터베이스 풀을 선택하고 그 안에서 데이터베이스 800개를 삭제합니다. 어느 시점에 데이터베이스 800개 중 200개만 사용 중이라면(5 DTU X 200 = 1000) 풀의 용량에 도달하지 않으며 데이터베이스 성능이 저하되지 않습니다. 이 예제는 쉽게 이해할 수 있도록 단순화했습니다. 실제 수치는 좀더 복잡합니다. 포털이 수치 계산을 수행하며, 기록 데이터베이스 사용량을 기반으로 권장 사항을 제공합니다. 권장 사항의 작동 원리에 대해 알아보거나 직접 계산을 수행하려면 [탄력적 데이터베이스 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

## 앱 및 비즈니스 운영 유지

Azure의 업계 선도적인 99.99% 가용성 [SLA](http://azure.microsoft.com/support/legal/sla/)(서비스 수준 계약)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 설계, 구입, 구축 및 관리해야 할 수 있는 기본 제공 데이터 보호, 내결함성 및 데이터 보호를 모든 SQL 데이터베이스에서 이용합니다. 그러한 경우 비즈니스 요구 사항에 따라 추가적인 보호 계층을 요구하여 재해, 오류 또는 다른 문제가 발생한 경우 앱 및 비즈니스를 신속하게 복구할 수 있습니다. SQL 데이터베이스에서는 각 서비스 계층에서 실행하고 유지하기 위해 사용할 수 있는 다양한 기능 메뉴를 제공합니다. 시점 복원을 사용하여 데이터베이스를 최대 35일 전의 상태로 복원할 수 있습니다. 또한 데이터베이스를 호스팅하는 데이터 센터가 가동 중지하면 여러 지역의 데이터베이스 복제를 사용하여 장애 조치를 할 수 있습니다. 또는 업그레이드 또는 다른 지역에 다시 배치하기 위해 복제를 사용할 수 있습니다.

![SQL 데이터베이스 지리적 복제](./media/sql-database-technical-overview/azure_sqldb_map.png)


서로 다른 서비스 계층에 사용할 수 있는 다양한 무중단 업무 방식 기능에 대한 자세한 내용은 [무중단 업무 방식](sql-database-business-continuity.md)을 참조하세요.

## 데이터 보호
SQL Server에는 SQL 데이터베이스가 액세스 제한, 데이터 보호 및 작업 모니터링 도움을 수행하는 기능을 통해 지원하는 확실한 데이터 보안의 전통을 이어갑니다. SQL 데이터베이스의 보안 옵션에 대한 빠른 요약은 [SQL 데이터베이스 보안](sql-database-security.md)을 참조하세요. 보안 기능을 더 광범위하게 보려면 [SQL Server 데이터베이스 엔진 및 SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589)를 참조하세요. 그리고 Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](http://azure.microsoft.com/support/trust-center/security/)를 방문하세요.

## 다음 단계

- 단일 데이터베이스 및 탄력적 데이터베이스 가격 책정 및 계산기는 [가격 책정 페이지](http://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.

- [첫 번째 데이터베이스 만들기](sql-database-get-started.md)로 시작합니다. 그런 다음 [C#](sql-database-connect-query.md), [Java](sql-database-develop-java-simple-windows.md), [Node.js](sql-database-develop-nodejs-simple-windows.md), [PHP](sql-database-develop-php-retry-windows.md), [Python](sql-database-develop-python-simple-windows.md) 또는 [Ruby](sql-database-develop-ruby-simple-linux)에서 첫 앱을 만듭니다.

<!---HONumber=Oct15_HO2-->