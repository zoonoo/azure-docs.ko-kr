<properties
	pageTitle="SQL 데이터베이스 성능 및 옵션: 서비스 계층 | Microsoft Azure"
	description="규모를 조정하는 동안 비용 및 기능의 균형을 유지하도록 서비스 계층의 SQL 데이터베이스 성능 및 비즈니스 연속성 기능을 비교합니다."
	keywords="데이터베이스 옵션, 데이터베이스 성능"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="04/20/2016"
	ms.author="carlrab"/>

# SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해

[Azure SQL 데이터베이스](sql-database-technical-overview.md)에는 다양한 워크로드를 처리하는 여러 서비스 계층이 있습니다. 응용 프로그램의 작동 중지 시간 없이 서비스 계층을 언제든지 변경할 수 있습니다. 정의된 특성을 가진 [단일 데이터베이스를 만들고](sql-database-get-started.md) 가격을 책정할 수도 있습니다. 또는 [탄력적 데이터베이스 풀을 만들어](sql-database-elastic-pool-create-portal.md) 여러 데이터베이스를 관리할 수 있습니다. 두 경우 모두 계층은 **Basic**, **Standard** 및 **Premium**을 포함합니다. 이러한 계층의 데이터베이스 옵션은 단일 데이터베이스 및 탄력적 풀에서 유사하지만 탄력적 풀에 대해 추가 고려 사항이 있습니다. 이 문서에서는 단일 데이터베이스 및 탄력적 데이터베이스에 대한 서비스 계층에 대해 자세히 설명합니다.

## 서비스 계층 및 데이터베이스 옵션
기본, 표준 및 프리미엄 서비스 계층은 모두 가동 시간 SLA가 99.99%이고 예측 가능한 성능, 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간당 대금 청구 기능을 제공합니다. 다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
|---|---|
| **Basic** | 일반적으로 지정된 시기에 활성 작업 한 개를 지원하는 작은 데이터베이스에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 데이터베이스가 이에 해당합니다. |
| **Standard** | 대부분의 클라우드 응용 프로그램에 적합한 옵션이며 복수의 동시 쿼리를 지원합니다. 예를 들어 작업 그룹 또는 웹 응용 프로그램이 이 계층에 적합한 예입니다. |
| **Premium** | 트랜잭션 양이 많은 경우에 적합하며 다수의 동시 사용자를 지원하고 가장 높은 수준의 비즈니스 연속성 기능을 요구합니다. 예를 들어 업무 필수 응용 프로그램을 지원하는 데이터베이스가 이 계층에 적합합니다. |

>[AZURE.NOTE] Web 및 Business 버전은 사용이 중지되었습니다. Web 및 Business Edition을 계속 사용하려는 경우 [지원 종료 FAQ](https://azure.microsoft.com/pricing/details/sql-database/web-business/)를 잘 읽어 보세요.

## 단일 데이터베이스 서비스 계층 및 성능 수준
단일 데이터베이스의 경우 각 서비스 계층 내에는 여러 성능 수준이 있습니다. 워크로드 요구에 가장 잘 맞는 수준을 선택할 수 있는 유연성이 있습니다. 규모를 확장 또는 축소해야 하는 경우는 **응용 프로그램 가동 중지 시간 없이** 데이터베이스의 계층을 간편하게 변경할 수 있습니다. 자세한 내용은 [데이터베이스 서비스 계층 및 성능 수준 변경](sql-database-scale-up.md)을 참조하세요.

여기에 나열된 성능 특성은 [SQL 데이터베이스 V12](sql-database-v12-whats-new.md)를 사용하여 만든 데이터베이스에 적용됩니다. Azure에서 기본 하드웨어가 여러 데이터베이스를 호스팅하는 경우 데이터베이스는 여전히 보장된 리소스 집합을 가져오며 데이터베이스의 예상되는 성능 특징은 영향을 받지 않습니다.

[AZURE.INCLUDE [SQL DB 서비스 계층 테이블](../../includes/sql-database-service-tiers-table.md)]

DTU를 보다 잘 이해하려면 이 항목에서 [DTU 섹션](#understanding-dtus)을 참조하세요.

>[AZURE.NOTE] 이 서비스 계층 표에서 다른 모든 행에 대한 자세한 설명은 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)을 참조하세요.

## 탄력적 풀 서비스 계층 및 eDTU의 성능
단일 데이터베이스를 만들고 규모를 확장하는 것 외에도 [탄력적 풀](sql-database-elastic-pool.md) 내에 복수의 데이터베이스를 관리하는 옵션이 있습니다. 탄력적 풀에 있는 모든 데이터베이스가 공통의 리소스 집합을 공유합니다. 성능 특성은 *탄력적 데이터베이스 트랜잭션 장치*(eDTU)에 의해 측정됩니다. 단일 데이터베이스의 경우와 마찬가지로 풀은 세 가지 서비스 계층 **Basic**, **Standard** 및 **Premium**으로 제공됩니다. 풀의 경우 이러한 세 가지 서비스 계층은 여전히 전체 성능 한계와 여러 기능을 정의합니다.

풀을 사용하여 탄력적 데이터베이스가 풀 내의 데이터베이스에 특정 성능 수준을 할당할 필요 없이 DTU 리소스를 공유하고 사용할 수 있습니다. 예를 들어 표준 풀의 단일 데이터베이스에서 eDTU를 0개 사용하다가 풀을 구성할 때 설정한 최대 데이터베이스 eDTU 사용으로 전환할 수 있습니다. 따라서 다양한 워크로드를 가진 복수의 데이터베이스에서 전체 풀에 사용 가능한 eDTU 리소스를 효율적으로 사용할 수 있습니다. 자세한 내용은 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)을 참조하세요.

다음 표에서는 풀 서비스 계층의 특성을 설명합니다.

[AZURE.INCLUDE [탄력적 데이터베이스에 대한 SQL DB 서비스 계층 표](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

또한 풀 내의 각 데이터베이스는 해당 계층에 대한 단일 데이터베이스 특성을 준수합니다. 예를 들어 Basic 풀의 최대 세션 수 한계는 2400 ~ 28800개이지만, 해당 풀 내의 개별 데이터베이스에 대한 데이터베이스 세션 한계는 300개입니다(이전 섹션에서 지정한 단일 Basic 데이터베이스에 대한 한계).

## DTU 이해

[AZURE.INCLUDE [SQL DB DTU 설명](../../includes/sql-database-understanding-dtus.md)]

## 다음 단계
- [SQL 데이터베이스 가격](https://azure.microsoft.com/pricing/details/sql-database/)에서 이러한 계층의 가격에 대해 자세히 알아보세요.
- [탄력적 데이터베이스 풀](sql-database-elastic-pool-guidance.md) 및 [탄력적 데이터베이스 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)에 대해 자세히 알아봅니다.
- [탄력적 풀을 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)하고 [단일 데이터베이스의 성능을 모니터링](sql-database-single-database-monitor.md)하는 방법에 대해 알아봅니다.
- SQL 데이터베이스 계층에 대해 알아 보았으면 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 사용해보고 [첫 번째 SQL 데이터베이스를 만드는 방법](sql-database-get-started.md)에 대해 알아보세요.

<!---HONumber=AcomDC_0427_2016-->