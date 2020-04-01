---
title: Azure SQL Database 서비스란?
description: 'SQL 데이터베이스 소개: 클라우드에서 Microsoft 관계형 데이터베이스 관리 시스템(RDBMS)의 기술 세부 정보 및 기능을 소개합니다.'
keywords: sql 소개, sql 소개, sql 데이터베이스란
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 0d50ddbbeeaed48c14d07c42588efcbb20bb7d79
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411167"
---
# <a name="what-is-the-azure-sql-database-service"></a>Azure SQL Database 서비스란?

Azure SQL Database는 사용자의 개입 없이도 업그레이드, 패치, 백업 및 모니터링과 같은 데이터베이스 관리 기능 대부분을 처리하는 완전히 관리되는 PaaS(Platform as a Service) 데이터베이스 엔진입니다. Azure SQL Database는 SQL Server 데이터베이스 엔진의 안정적인 최신 버전에서 항상 실행되고 99.99% 가용성이 있는 OS가 패치됩니다. Azure SQL 데이터베이스에 내장된 PaaS 기능을 사용하면 비즈니스에 중요한 도메인별 데이터베이스 관리 및 최적화 활동에 집중할 수 있습니다.

Azure SQL Database를 사용하면 Azure의 응용 프로그램 및 솔루션에 대해 가용성이 높고 고성능 데이터 저장소 계층을 만들 수 있습니다. SQL Database는 그래프, JSON, 공간 및 XML과 같은 관계형 데이터와 [비관계형 구조를](sql-database-multi-model-features.md)모두 처리할 수 있으므로 다양한 최신 클라우드 응용 프로그램에 적합한 선택이 될 수 있습니다.

Microsoft [SQL Server 데이터베이스 엔진의](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)최신 안정 버전을 기반으로 합니다. [고성능 인메모리 기술](sql-database-in-memory.md) 및 [지능형 쿼리](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json)처리와 같은 고급 쿼리 처리 기능을 사용할 수 있습니다. 실제로 SQL Server의 최신 기능은 먼저 SQL Database에 릴리스된 다음 SQL Server 자체에 릴리스됩니다. 수백만 개의 데이터베이스에서 테스트된 패치 또는 업그레이드에 대한 오버헤드없이 최신 SQL Server 기능을 사용할 수 있습니다. 

SQL Database를 사용하면 [vCore 기반 구매 모델과](sql-database-service-tiers-vcore.md) [DTU 기반 구매 모델이라는](sql-database-service-tiers-dtu.md)두 가지 구매 모델 내에서 성능을 쉽게 정의하고 확장할 수 있습니다. SQL Database는 고가용성, 백업 및 기타 일반적인 유지 관리 작업이 내장된 완전히 관리되는 서비스입니다. Microsoft는 SQL 및 운영 체제 코드의 모든 패치 및 업데이트를 처리합니다. 기본 인프라를 관리할 필요가 없습니다.

> [!NOTE]
> 관련 용어 및 해당 정의는 [SQL Database 용어 집입니다.](sql-database-glossary-terms.md)

## <a name="deployment-models"></a>배포 모델

Azure SQL Database는 Azure SQL 데이터베이스에 다음과 같은 옵션을 제공합니다.

![배포 옵션 다이어그램](./media/sql-database-technical-overview/deployment-options.png)

- [단일 데이터베이스는](sql-database-single-database.md) 완전히 관리되는 격리된 데이터베이스를 나타냅니다. 신뢰할 수 있는 단일 데이터 원본이 필요한 최신 클라우드 응용 프로그램 및 마이크로 서비스가 있는 경우 이 옵션을 사용할 수 있습니다. 단일 데이터베이스는 Microsoft SQL Server 데이터베이스 엔진에 [포함된 데이터베이스와](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) [유사합니다.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)
- [관리되는 인스턴스는](sql-database-managed-instance.md) [Microsoft SQL Server 데이터베이스 엔진의](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)완전히 관리되는 인스턴스입니다. 여기에는 함께 사용할 수 있는 데이터베이스 집합이 포함되어 있습니다. 온-프레미스 SQL Server 데이터베이스를 Azure 클라우드로 쉽게 마이그레이션하고 SQL Server Database Engine에서 제공하는 데이터베이스 기능을 사용해야 하는 응용 프로그램에 이 옵션을 사용합니다.
- [탄력적 풀은](sql-database-elastic-pool.md) CPU 또는 메모리와 같은 공유 리소스 집합이 있는 [단일 데이터베이스의](sql-database-single-database.md) 모음입니다. 단일 데이터베이스를 탄력적 풀로 이동하거나 탄력적 풀에서 제거할 수 있습니다.

> [!IMPORTANT]
> SQL Database와 SQL Server 간의 기능 차이와 다른 Azure SQL Database 배포 옵션 간의 차이점을 이해하려면 [SQL Database 기능을](sql-database-features.md)참조하십시오.

SQL Database는 여러 리소스 유형, 서비스 계층 및 계산 크기를 사용하여 예측 가능한 성능을 제공합니다. 가동 중지 시간 없이 동적 확장성을 제공하고, 내장된 지능형 최적화, 글로벌 확장성 및 가용성, 고급 보안 옵션을 제공합니다. 이러한 기능을 사용하면 가상 컴퓨터 및 인프라를 관리하는 대신 빠른 앱 개발과 출시 기간을 단축하는 데 집중할 수 있습니다. SQL Database 서비스는 현재 전 세계 38개 데이터 센터에 있으므로 가까운 데이터 센터에서 데이터베이스를 실행할 수 있습니다.

## <a name="scalable-performance-and-pools"></a>확장 가능한 성능 및 풀

할당된 리소스의 양을 정의할 수 있습니다. 
- 단일 데이터베이스를 사용하면 각 데이터베이스가 다른 데이터베이스와 격리되어 이식가능합니다. 각각에는 자체적으로 보장된 컴퓨팅, 메모리 및 저장소 리소스가 있습니다. 데이터베이스에 할당된 리소스의 양은 해당 데이터베이스에 전용이며 Azure의 다른 데이터베이스와 공유되지 않습니다. 단일 데이터베이스 [리소스를](sql-database-single-database-scale.md) 위아래로 동적으로 확장할 수 있습니다. 단일 데이터베이스 옵션은 다양한 요구 사항에 따라 서로 다른 계산, 메모리 및 저장소 리소스를 제공합니다. 예를 들어 1~80vCores 또는 32GB ~ 4TB를 얻을 수 있습니다. 단일 데이터베이스에 대한 [하이퍼스케일 서비스 계층을](sql-database-service-tier-hyperscale.md) 사용하면 빠른 백업 및 복원 기능을 통해 100TB로 확장할 수 있습니다.
- 탄력적 풀을 사용하면 풀의 모든 데이터베이스에서 공유하는 리소스를 할당할 수 있습니다. 새 데이터베이스를 만들거나 기존 단일 데이터베이스를 리소스 풀로 이동하여 리소스 사용을 최대화하고 비용을 절감할 수 있습니다. 또한 이 옵션을 사용하면 [탄력적 풀 리소스를](sql-database-elastic-pool-scale.md) 위아래로 동적으로 확장할 수 있습니다.
- 관리되는 인스턴스를 사용할 경우 각각의 관리되는 인스턴스가 다른 인스턴스에서 격리되고 리소스가 보장됩니다. 관리되는 인스턴스 내에서 인스턴스 데이터베이스는 리소스 집합을 공유합니다. [관리되는 인스턴스 리소스를](sql-database-managed-instance-resource-limits.md) 동적으로 확장및 축소할 수 있습니다.

범용 서비스 계층에서 매월 저렴한 비용으로 작은 단일 데이터베이스에 첫 번째 앱을 빌드할 수 있습니다. 그런 다음 솔루션의 요구 사항을 충족하기 위해 언제든지 서비스 계층을 비즈니스 크리티컬 서비스 계층으로 수동으로 또는 프로그래밍 방식으로 변경할 수 있습니다. 앱이나 고객에게 가동 중지 시간 없이 성능을 조정할 수 있습니다. 동적 확장성을 사용하면 데이터베이스가 빠르게 변화하는 리소스 요구 사항에 투명하게 대응할 수 있습니다. 필요할 때 필요한 리소스에 대해서만 비용을 지불합니다.

*동적 확장성은* 자동 *크기 조정과*다릅니다. 자동 크기 조정은 서비스가 조건에 따라 자동으로 크기를 조정하는 경우인 반면 동적 확장성은 가동 중지 시간 없이 수동 크기 조정을 허용합니다. 단일 데이터베이스 옵션은 수동 동적 확장성을 지원하지만 자동 크기 조정은 지원하지 않습니다. 더 많은 자동 환경은 데이터베이스에서 개별 데이터베이스 요구 사항에 따라 풀에 리소스를 공유하도록 허용하는 탄력적 풀을 사용하는 것이 좋습니다. 또 다른 옵션은 단일 데이터베이스의 확장성을 자동화하는 데 도움이 되는 스크립트를 사용하는 것입니다. 예제는 [PowerShell을 사용하여 단일 데이터베이스 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-database-powershell.md)을 참조하세요.

### <a name="purchasing-models"></a>구매 모델

SQL Database는 다음과 같은 구매 모델을 제공합니다.
- [vCore 기반 구매 모델을](sql-database-service-tiers-vcore.md) 사용하면 vCore 수, 메모리 양 및 저장소 양 및 속도를 선택할 수 있습니다. vCore 기반 구매 모델을 사용하면 [SQL Server용 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 비용을 절약할 수도 있습니다. Azure 하이브리드 혜택에 대한 자세한 내용은 이 문서의 후반부 "자주 묻는 질문" 섹션을 참조하십시오.
- [DTU 기반 구매 모델은](sql-database-service-tiers-dtu.md) 세 가지 서비스 계층에서 계산, 메모리 및 I/O 리소스의 혼합을 제공하여 무거운 데이터베이스 워크로드에 대한 가벼운 지원을 제공합니다. 각 계층 내의 컴퓨팅 크기는 이러한 리소스의 다양한 조합을 제공하여 추가 스토리지 리소스를 추가할 수 있습니다.
- [서버리스 모델은](sql-database-serverless.md) 워크로드 수요에 따라 계산을 자동으로 확장하고 초당 사용되는 계산 양에 대한 요금을 청구합니다. 또한 서버리스 계산 계층은 저장소만 청구되는 비활성 기간 동안 데이터베이스를 자동으로 일시 중지하고 활동이 반환될 때 데이터베이스를 자동으로 다시 시작합니다.

### <a name="service-tiers"></a>서비스 계층

Azure SQL Database는 다양한 유형의 응용 프로그램에 맞게 설계된 세 가지 서비스 계층을 제공합니다.
- 일반적인 워크로드를 위해 설계된 [범용/표준](sql-database-service-tier-general-purpose.md) 서비스 계층입니다. 예산 중심의 균형 잡힌 컴퓨팅 및 스토리지 옵션을 제공합니다.
- 트랜잭션 속도가 높고 대기 시간이 가장 낮은 I/O를 가진 OLTP 애플리케이션을 위해 설계된 [비즈니스 크리티컬/프리미엄](sql-database-service-tier-business-critical.md) 서비스 계층입니다. 여러 격리된 복제본을 사용하여 오류에 대한 가장 높은 복원력을 제공합니다.
- 초대형 OLTP 데이터베이스와 스토리지를 자동으로 확장하고 컴퓨팅을 유동적으로 확장할 수 있도록 설계된 [하이퍼스케일](sql-database-service-tier-hyperscale.md) 서비스 계층입니다.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>리소스 사용률 극대화를 위한 탄력적 풀

특히 사용 패턴이 비교적 예측 가능한 경우 많은 비즈니스 및 애플리케이션에서 단일 데이터베이스를 만들고 필요에 따라 충분히 성능을 확장하거나 축소할 수 있습니다. 예측할 수 없는 사용 패턴으로 인해 비용과 비즈니스 모델을 관리하기가 어려울 수 있습니다. [탄력적 풀은](sql-database-elastic-pool.md) 이 문제를 해결하도록 설계되었습니다. 개별 데이터베이스가 아닌 풀에 성능 리소스를 할당합니다. 단일 데이터베이스 성능이 아니라 풀의 집합성능 리소스에 대한 비용을 지불합니다.

   ![기본, 표준 및 프리미엄 에디션의 탄력적 풀을 표시하는 그래픽](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

탄력적 풀을 사용하면 리소스 에 대한 수요가 변동함에 따라 데이터베이스 성능을 위아래로 전화 걸기에 집중할 필요가 없습니다. 풀링된 데이터베이스는 필요에 따라 탄력적 풀의 성능 리소스를 사용합니다. 풀된 데이터베이스는 풀의 한계를 초과하지 않으므로 개별 데이터베이스 사용이 필요하지 않더라도 비용을 예측할 수 있습니다.

[풀에 데이터베이스를 추가하고 제거하고,](sql-database-elastic-pool-manage-portal.md)소수의 데이터베이스에서 수천 개의 데이터베이스로 앱을 확장할 수 있습니다. 또한 풀의 데이터베이스에 사용할 수 있는 최소 및 최대 리소스를 제어하여 풀에 있는 데이터베이스가 모든 풀 리소스를 사용하지 않고 풀링된 모든 데이터베이스에 보장된 최소 리소스가 있는지 확인할 수 있습니다. 탄력적 풀을 사용하는 서비스(SaaS) 응용 프로그램의 디자인 패턴에 대한 자세한 내용은 [SQL Database를 사용하는 다중 테넌트 SaaS 응용 프로그램에 대한 디자인 패턴을](sql-database-design-patterns-multi-tenancy-saas-applications.md)참조하십시오.

스크립트는 탄력적 풀 모니터링 및 크기 조정에 도움을 줄 수 있습니다. 예를 들어 PowerShell 사용을 사용하여 [Azure SQL Database 에서 SQL 탄력적 풀을 모니터링하고 확장합니다.](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> 관리되는 인스턴스는 탄력적 풀을 지원하지 않습니다. 대신, 관리되는 인스턴스는 관리되는 인스턴스 리소스를 공유하는 인스턴스 데이터베이스 컬렉션입니다.

### <a name="blend-single-databases-with-pooled-databases"></a>단일 데이터베이스와 풀링된 데이터베이스의 혼합

단일 데이터베이스를 탄력적 풀과 혼합하고 단일 데이터베이스 및 탄력적 풀의 서비스 계층을 변경하여 상황에 맞게 조정할 수 있습니다. 또한 다른 Azure 서비스를 SQL Database와 혼합및 일치하여 고유한 앱 디자인 요구 사항을 충족하고, 비용 및 리소스 효율성을 높이고, 새로운 비즈니스 기회를 열 수 있습니다.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>광범위한 모니터링 및 경고 기능

Azure SQL Database는 워크로드 특성에 대한 심층적인 통찰력을 얻을 수 있도록 고급 모니터링 및 문제 해결 기능을 제공합니다. 이러한 기능 및 도구는 다음과 같습니다.
 - SQL Server 데이터베이스 엔진의 최신 버전에서 제공하는 기본 제공 모니터링 기능입니다. 이를 통해 실시간 성능 통찰력을 찾을 수 있습니다. 
 - 많은 수의 데이터베이스 인스턴스를 모니터링하고 문제를 해결할 수 있도록 Azure에서 제공하는 PaaS 모니터링 기능입니다.

기본 제공 SQL Server 모니터링 기능인 [Query Store는](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store)쿼리 성능을 실시간으로 기록하며 잠재적인 성능 문제와 상위 리소스 소비자를 식별할 수 있습니다. 자동 튜닝 및 권장 사항은 회귀된 성능및 누락되거나 중복된 인덱스가 있는 쿼리에 대한 조언을 제공합니다. SQL Database에서 자동 조정을 사용하면 문제를 해결할 수 있는 스크립트를 수동으로 적용하거나 SQL Database에서 수정 프로그램을 적용할 수 있습니다. SQL Database는 수정 프로그램이 몇 가지 이점을 제공하는지 테스트하고 확인할 수 있으며 결과에 따라 변경 사항을 유지하거나 되돌릴 수 있습니다. 쿼리 저장소 및 자동 튜닝 기능 외에도 표준 [DMV 및 XEvent를](sql-database-monitoring-with-dmvs.md) 사용하여 워크로드 성능을 모니터링할 수 있습니다.

Azure는 수천 개의 데이터베이스 상태를 모니터링할 수 있는 성능 [등급과](sql-database-performance-guidance.md) 함께 기본 제공 성능 모니터링 및 [경고](sql-database-insights-alerts-portal.md) 도구를 제공합니다. 이러한 도구를 사용하면 현재 또는 예상 성능 요구 사항에 따라 확장 또는 축소의 영향을 신속하게 평가할 수 있습니다. 또한 SQL Database는 쉬운 모니터링을 위해 [메트릭 및 진단 로그를 내보낼](sql-database-metrics-diag-logging.md) 수 있습니다. 리소스 사용량, 작업자와 세션 및 연결을 이러한 Azure 리소스 중 하나에 저장하도록 SQL Database를 구성할 수 있습니다.

- **Azure Storage**: 저렴한 가격으로 방대한 양의 원격 분석을 보관할 수 있습니다.
- **Azure 이벤트 허브**: SQL Database 원격 분석을 사용자 지정 모니터링 솔루션 또는 핫 파이프라인과 통합합니다.
- **Azure Monitor 로그**: 보고, 경고 및 완화 기능을 갖춘 기본 제공 모니터링 솔루션의 경우

![Azure 모니터링 아키텍처 다이어그램](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>가용성 기능

Azure SQL Database를 사용하면 중단 중에 비즈니스를 계속 운영할 수 있습니다. 기존 SQL Server 환경에서는 일반적으로 로컬로 두 대 이상의 컴퓨터를 설정합니다. 이러한 컴퓨터는 단일 컴퓨터 또는 구성 요소의 오류로부터 보호하기 위해 정확하고 동기적으로 유지 관리되는 데이터 복사본을 가지고 있습니다. 이 환경은 고가용성을 제공하지만 데이터 센터를 파괴하는 자연 재해로부터 보호하지는 않습니다.

재해 복구는 치명적인 이벤트가 지리적으로 지역화되어 데이터 복사본이 있는 다른 컴퓨터 나 컴퓨터 집합을 멀리 있게 한다고 가정합니다. SQL Server에서 비동기 모드에서 실행되는 항상 사용 가능 그룹을 사용하여 이 기능을 사용할 수 있습니다. 사람들은 트랜잭션을 커밋하기 전에 복제가 발생할 때까지 기다리지 않으려하므로 계획되지 않은 장애 조치(failover)를 수행하면 데이터가 손실될 수 있습니다.

프리미엄 및 비즈니스 크리티컬 서비스 계층의 데이터베이스는 이미 가용성 그룹의 동기화와 [비슷한 작업을 수행합니다.](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) 낮은 서비스 계층의 데이터베이스는 [서로 다르지만 동등한 메커니즘을](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability)사용하여 저장소를 통해 중복성을 제공합니다. 내장 된 논리는 단일 시스템 오류로부터 보호하는 데 도움이됩니다. 활성 지역 복제 기능을 사용하면 전체 지역이 파괴된 재해로부터 보호할 수 있습니다.

Azure 가용성 영역은 단일 지역 내의 단일 데이터 센터 건물의 중단을 방지하려고 합니다. 건물에 대한 전력 또는 네트워크 손실을 방지할 수 있습니다. SQL Database에서 다른 사용 가능 영역(다른 건물, 효과적으로)에 다른 복제본을 배치합니다.

실제로 Microsoft에서 관리하는 데이터 센터의 글로벌 네트워크를 기반으로 하는 Azure의 [SLA(서비스 수준 계약)는](https://azure.microsoft.com/support/legal/sla/) 앱을 24/7 계속 실행하는 데 도움이 됩니다. Azure 플랫폼은 모든 데이터베이스를 완벽하게 관리하며 데이터 손실과 높은 데이터 가용성을 보장합니다. Azure는 패치, 백업, 복제, 오류 감지, 기본 하드웨어, 소프트웨어 또는 네트워크 오류, 배포 버그 픽스, 장애 조치(failover), 데이터베이스 업그레이드 및 기타 유지 관리 작업을 자동으로 처리합니다. 표준 가용성은 컴퓨팅 계층과 스토리지 계층을 분리하여 달성합니다. 성능을 위해 단일 노드에 컴퓨팅과 스토리지를 통합한 다음 Always On 가용성 그룹과 유사한 기술을 구현함으로써 프리미엄 가용성을 달성할 수 있습니다. Azure SQL Database의 고가용성 기능에 대한 자세한 내용은 [SQL Database 가용성](sql-database-high-availability.md)을 참조하세요. 

또한 SQL Database는 기본 제공 [비즈니스 연속성 및 글로벌 확장성](sql-database-business-continuity.md) 기능을 제공합니다. 여기에는 다음이 포함됩니다.

- [자동 백업](sql-database-automated-backups.md):

  SQL Database는 SQL 데이터베이스의 전체, 차동 및 트랜잭션 로그 백업을 자동으로 수행하여 언제든지 복원할 수 있습니다. 단일 데이터베이스 및 풀된 데이터베이스의 경우 장기 백업 보존을 위해 전체 데이터베이스 백업을 Azure Storage에 저장하도록 SQL Database를 구성할 수 있습니다. 관리되는 인스턴스의 경우 장기 백업 보존을 위해 복사 전용 백업도 수행할 수 있습니다.

- [시점 복원](sql-database-recovery-using-backups.md):

  모든 SQL Database 배포 옵션은 모든 SQL 데이터베이스의 자동 백업 보존 기간 내에 있는 모든 시점까지 복구를 지원합니다.
- [활성 지역 복제](sql-database-active-geo-replication.md):

  단일 데이터베이스 및 풀처리된 데이터베이스 옵션을 사용하면 동일하거나 전역적으로 분산된 Azure 데이터 센터에서 최대 4개의 읽을 수 있는 보조 데이터베이스를 구성할 수 있습니다. 예를 들어 동시 읽기 전용 트랜잭션이 많은 카탈로그 데이터베이스가 있는 SaaS 응용 프로그램이 있는 경우 활성 지역 복제를 사용하여 전역 읽기 배율을 사용하도록 설정합니다. 이렇게 하면 읽기 워크로드로 인해 기본 에서 병목 현상이 제거됩니다. 관리되는 인스턴스의 경우 자동 장애 조치(failover) 그룹을 사용합니다.
- [자동 장애 조치(failover) 그룹](sql-database-auto-failover-group.md):

  모든 SQL Database 배포 옵션을 사용하면 장애 조치 그룹을 사용하여 고가용성 및 로드 분산을 전역 규모로 사용할 수 있습니다. 여기에는 대규모 데이터베이스 집합, 탄력적 풀 및 관리되는 인스턴스의 투명한 지역 복제 및 장애 조치(failover)가 포함됩니다. 장애 조치 그룹을 사용하면 관리 오버를 최소화하면서 전역으로 분산된 SaaS 응용 프로그램을 만들 수 있습니다. 이렇게 하면 모든 복잡한 모니터링, 라우팅 및 장애 조치(failover) 오케스트레이션을 SQL Database에 남게 됩니다.
- [영역 중복 데이터베이스](sql-database-high-availability.md):

  SQL Database를 사용하면 여러 가용성 영역에서 프리미엄 또는 중요 비즈니스용 데이터베이스 또는 탄력적 풀을 프로비전할 수 있습니다. 이러한 데이터베이스 및 탄력적 풀에는 고가용성을 위해 여러 중복 복제본이 있으므로 이러한 복제본을 여러 가용성 영역에 배치하면 복원력이 높아지습니다. 여기에는 데이터 손실 없이 데이터 센터 스케일 오류에서 자동으로 복구하는 기능이 포함됩니다.

## <a name="built-in-intelligence"></a>기본 제공 인텔리전스

SQL Database를 사용하면 데이터베이스 실행 및 관리 비용을 크게 절감하고 응용 프로그램의 성능과 보안을 모두 최대화하는 기본 제공 인텔리전스를 얻을 수 있습니다. SQL Database는 항상 수백만 개의 고객 워크로드를 실행하면서 방대한 양의 원격 분석 데이터를 수집하고 처리하는 동시에 고객 의 개인 정보를 완전히 존중합니다. 다양한 알고리즘은 서비스가 응용 프로그램에 대해 학습하고 적응할 수 있도록 원격 분석 데이터를 지속적으로 평가합니다.

### <a name="automatic-performance-monitoring-and-tuning"></a>자동 성능 모니터링 및 튜닝

SQL Database는 모니터링해야 하는 쿼리에 대한 자세한 정보를 제공합니다. SQL Database는 데이터베이스 패턴에 대해 학습하고 데이터베이스 스키마를 워크로드에 맞게 조정할 수 있도록 합니다. SQL Database는 튜닝 작업을 검토하고 적용할 수 있는 [성능 튜닝 권장 사항](sql-database-advisor.md)을 제공합니다.

그러나 데이터베이스를 지속적으로 모니터링하는 것은 어렵고 지루한 작업이며, 특히 많은 데이터베이스를 처리할 때는 매우 어렵고 지루한 작업입니다. [지능형 인사이트는](sql-database-intelligent-insights.md) SQL Database 성능을 대규모로 자동으로 모니터링하여 이 작업을 수행합니다. 성능 저하 문제를 알려주고 각 문제의 근본 원인을 식별하며 가능한 경우 성능 향상 권장 사항을 제공합니다.

SQL Database 및 Azure에서 제공하는 사용 가능한 모든 도구와 보고서에서도 수많은 데이터베이스를 효율적으로 관리하는 것은 불가능할 수 있습니다. 데이터베이스를 수동으로 모니터링하고 튜닝하는 대신 [자동 튜닝을](sql-database-automatic-tuning.md)사용하여 일부 모니터링 및 튜닝 작업을 SQL Database에 위임하는 것이 좋습니다. SQL Database는 각 튜닝 작업을 권장 사항, 테스트 및 확인하여 성능이 계속 향상되도록 자동으로 적용합니다. 이렇게 하면 SQL Database는 제어되고 안전한 방식으로 워크로드에 자동으로 적응합니다. 자동 튜닝은 모든 튜닝 작업 전후에 데이터베이스의 성능을 주의 깊게 모니터링하고 비교한다는 것을 의미합니다. 성능이 향상되지 않으면 튜닝 작업이 되돌아갑니다.

SQL Database 위에 [SaaS 다중 테넌트 앱을](sql-database-design-patterns-multi-tenancy-saas-applications.md) 실행하는 많은 파트너는 응용 프로그램이 항상 안정적이고 예측 가능한 성능을 갖출 수 있도록 자동 성능 튜닝에 의존하고 있습니다. 이 기능은 갑자기 성능 인시던트가 발생하는 위험을 크게 줄여 줍니다. 또한 고객 기반의 일부도 SQL Server를 사용하기 때문에 SQL Server에서 제공하는 것과 동일한 인덱싱 권장 사항을 사용하여 SQL Server 고객을 돕습니다.

SQL 데이터베이스에서 두 가지 자동 조정 측면을 [사용할 수 있습니다.](sql-database-automatic-tuning.md)

- **자동 인덱스 관리**: 데이터베이스에 추가되어야 하는 인덱스 및 제거되어야 하는 인덱스를 식별합니다.
- **자동 계획 수정**: 문제가 있는 계획을 식별하고 SQL 계획 성능 문제를 해결합니다.

### <a name="adaptive-query-processing"></a>적응 쿼리 처리

다중 문 테이블 값 함수에 대한 인터리브 실행을 포함하여 [적응형 쿼리 처리를](/sql/relational-databases/performance/intelligent-query-processing)사용할 수 있으며, 일괄 처리 모드 메모리 부여 피드백 및 일괄 모드 적응 조인을 사용할 수 있습니다. 이러한 각 적응형 쿼리 처리 기능은 유사한 "학습 및 적응" 기술을 적용하여 역사적으로 난치성 쿼리 최적화 문제와 관련된 성능 문제를 추가로 해결하는 데 도움이 됩니다.

## <a name="advanced-security-and-compliance"></a>고급 보안 및 규정 준수

SQL Database는 응용 프로그램이 다양한 보안 및 규정 준수 요구 사항을 충족하는 데 도움이 되는 다양한 [기본 제공 보안 및 규정 준수 기능을](sql-database-security-overview.md) 제공합니다.

> [!IMPORTANT]
> Microsoft는 여러 규정 준수 표준에 대해 Azure SQL Database(모든 배포 옵션)를 인증했습니다. 자세한 내용은 최신 SQL 데이터베이스 준수 인증 목록을 찾을 수 있는 [Microsoft Azure Trust Center를](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)참조하십시오.

### <a name="advance-threat-protection"></a>사전 위협 보호

고급 데이터 보안은 고급 SQL 보안 기능의 통합 패키지입니다. 여기에는 중요한 데이터를 검색 및 분류하고, 데이터베이스 취약점을 관리하고, 데이터베이스에 위협을 나타낼 수 있는 비정상적인 활동을 검색하는 기능이 포함되어 있습니다. 이 패키지를 통해 이러한 기능을 한 곳에서 사용하도록 설정하고 관리할 수 있습니다.

- [데이터 검색 및 분류](sql-database-data-discovery-and-classification.md):

  이 기능은 데이터베이스의 중요한 데이터를 검색, 분류, 레이블 지정 및 보호하기 위해 Azure SQL Database에 기본 제공된 기능을 제공합니다. 데이터베이스 분류 상태에 대한 가시성을 제공하고 데이터베이스 내 및 테두리 를 넘어 중요한 데이터에 대한 액세스를 추적합니다.
- [취약점 평가](sql-vulnerability-assessment.md):

  이 서비스는 잠재적인 데이터베이스 취약성을 검색, 추적 및 수정하는 데 도움이 됩니다. 보안 상태에 대한 가시성을 제공하고, 보안 문제를 해결하고 데이터베이스 보안을 강화하기 위한 실행 가능한 단계를 포함합니다.
- [위협 탐지](sql-database-threat-detection.md):

  이 기능은 데이터베이스에 액세스하거나 악용하려는 비정상적인 시도를 나타내는 비정상적인 활동을 감지합니다. 지속적으로 의심스러운 활동에 대한 데이터베이스를 모니터링하고, 잠재적인 취약점, SQL 삽입 공격 및 비정상 데이터베이스 액세스 패턴에 대한 보안 경고를 즉시 제공합니다. 위협 탐지 경고는 의심스러운 활동에 대한 세부 정보를 제공하고 위협을 조사하고 완화하는 방법에 대한 조치를 권장합니다.

### <a name="auditing-for-compliance-and-security"></a>규정 준수 및 보안에 대한 감사

[감사](sql-database-auditing.md)는 데이터베이스 이벤트를 추적하고 Azure Storage 계정의 감사 로그에 이벤트를 씁니다. 감사는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

### <a name="data-encryption"></a>데이터 암호화.

SQL Database는 암호화를 제공하여 데이터를 보호하는 데 도움이 됩니다. 이동 중인 데이터의 경우 [전송 계층 보안을](https://support.microsoft.com/kb/3135244)사용합니다. 미사용 데이터의 경우 [투명 데이터 암호화를](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)사용합니다. 사용 중 데이터의 경우 [항상 암호화된](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)을 사용합니다.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 Multi-Factor Authentication

SQL Database를 사용하면 [Azure Active Directory 통합](sql-database-aad-authentication.md)에서 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure Active Directory는 단일 로그인 프로세스를 지원하면서 데이터 및 응용 프로그램 보안을 높이기 위해 [다단계 인증을](sql-database-ssms-mfa-authentication.md) 지원합니다.

## <a name="easy-to-use-tools"></a>사용하기 쉬운 도구

SQL Database로 애플리케이션을 빌드하고 관리하는 작업의 편의성과 생산성을 높이세요. SQL Database를 사용하면 멋진 앱을 만드는 데만 집중할 수 있습니다. 이미 가지고 있는 도구와 기술을 사용하여 SQL Database에서 관리하고 개발할 수 있습니다.

- [Azure 포털:](https://portal.azure.com/)

  모든 Azure 서비스를 관리하기 위한 웹 기반 응용 프로그램입니다.
- [SQL 서버 관리 스튜디오](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  SQL Server에서 SQL 데이터베이스에 이르기까지 모든 SQL 인프라를 관리하기 위한 다운로드 가능한 무료 클라이언트 응용 프로그램입니다.
- [Visual Studio의 SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  SQL Server 관계형 데이터베이스, SQL 데이터베이스, 통합 서비스 패키지, 분석 서비스 데이터 모델 및 보고 서비스 보고서를 개발하기 위한 무료 다운로드 가능한 클라이언트 응용 프로그램입니다.
- [비주얼 스튜디오 코드](https://code.visualstudio.com/docs):

  Windows, macOS 및 Linux용 무료 다운로드 가능한 오픈 소스 코드 편집기입니다. Microsoft SQL Server, Azure SQL 데이터베이스 및 Azure SQL 데이터 웨어하우스를 쿼리하기 위한 [mssql 확장을](https://aka.ms/mssql-marketplace) 비롯한 확장을 지원합니다.

SQL Database는 macOS, Linux 및 Windows에서 파이썬, Java, Node.js, PHP, 루비 및 .NET을 사용하여 응용 프로그램 빌드를 지원합니다. SQL Database는 동일한 [연결 라이브러리](sql-database-libraries.md)를 SQL Server로 지원합니다.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL 데이터베이스자주 묻는 질문

### <a name="what-is-the-current-version-of-sql-database"></a>SQL Database의 최신 버전은 무엇인가요?

SQL Database의 현재 버전은 V12입니다. 버전 V11은 만료되었습니다.

### <a name="can-i-control-when-patching-downtime-occurs"></a>패치 가동 중지 시간이 발생하는 경우를 제어할 수 있습니까?

아니요. 일반적으로 패치 적용에 따른 영향은 앱에서 [다시 시도 논리를 사용](sql-database-develop-overview.md#resiliency)하면 두드러지지 않습니다. 자세한 내용은 [Azure SQL 데이터베이스의 Azure 유지 관리 이벤트 계획을](sql-database-planned-maintenance.md)참조하십시오.

### <a name="azure-hybrid-benefit-questions"></a>Azure 하이브리드 혜택 질문

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택에 이중 사용 권한이 있나요?

마이그레이션이 원활하게 실행되도록 하려면 180일간의 이중 사용 권한 라이선스가 있어야 합니다. 180일 기간이 지나면 SQL Database의 클라우드에서만 SQL Server 라이선스를 사용할 수 있습니다. 더 이상 온-프레미스 및 클라우드에서 이중 사용 권한이 없습니다.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server에 대한 Azure 하이브리드 혜택은 라이선스 이동과 어떻게 다른가요?

소프트웨어 보증을 통해 SQL Server 고객에게 라이선스 이동성 혜택을 제공합니다. 이렇게 하면 파트너를 공유 하는 서버에 라이센스를 다시 할당할 수 있습니다. Azure IaaS 및 AWS EC2에서 이 혜택을 사용할 수 있습니다.

SQL Server에 대한 Azure 하이브리드 혜택은 두 가지 주요 영역에서 라이선스 이동과 다릅니다.

- 높은 수준으로 가상화된 워크로드를 Azure로 이동할 수 있는 경제적 이점을 제공합니다. SQL Server 엔터프라이즈 에디션 고객은 고도로 가상화된 응용 프로그램을 위해 온-프레미스에서 소유한 모든 코어에 대해 범용 SKU에서 Azure에서 4개의 코어를 얻을 수 있습니다. 라이선스 모빌리티는 가상화된 워크로드를 클라우드로 이동하기 위한 특별한 비용 이점을 허용하지 않습니다.
- SQL Server 온-프레미스와 매우 호환되는 Azure(SQL Database 관리 인스턴스)에서 PaaS 대상을 제공합니다.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server에 대한 Azure 하이브리드 혜택의 특정 권한은 무엇인가요?

SQL Database 고객은 SQL Server에 대한 Azure 하이브리드 혜택과 관련된 다음 권한을 갖습니다.

|라이센스 발자국|SQL Server에 대한 Azure 하이브리드 이점은 무엇입니까?|
|---|---|
|SA가 있는 SQL Server Enterprise Edition 핵심 고객|<li>범용 또는 비즈니스 크리티컬 SKU에 기본 요율을 지불할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 4개 코어</li><br><li>온-프레미스의 1개 코어 = 중요 비즈니스 SKU의 1개 코어</li>|
|SA가 있는 SQL Server Standard Edition 핵심 고객|<li>범용 SKU에서만 기본 요금을 지불 할 수 있습니다.</li><br><li>온-프레미스의 1개 코어 = 범용 SKU의 1개 코어</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>SQL Server 엔지니어링 팀에 문의

- [DBA 스택 교환](https://dba.stackexchange.com/questions/tagged/sql-server): 데이터베이스 관리 질문을 합니다.
- [스택 오버플로 :](https://stackoverflow.com/questions/tagged/sql-server)개발 질문을 합니다.
- [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): 기술적인 질문을 합니다.
- [피드백](https://aka.ms/sqlfeedback): 버그를 보고하고 기능을 요청합니다.
- [레딧](https://www.reddit.com/r/SQLServer/): SQL 서버에 대해 설명합니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스 및 탄력적 풀에 대한 비용 비교 및 계산기는 [가격 책정 페이지를](https://azure.microsoft.com/pricing/details/sql-database/) 참조하십시오.
- 다음 빠른 시작을 확인하세요.

  - [Azure Portal에서 SQL Database 만들기](sql-database-single-database-get-started.md)  
  - [Azure CLI를 사용하여 SQL Database 만들기](sql-database-get-started-cli.md)
  - [PowerShell을 사용하여 SQL Database 만들기](sql-database-get-started-powershell.md)

- 일련의 Azure CLI 및 PowerShell 샘플은 다음을 참조하세요.
  - [SQL Database에 대한 Azure CLI 샘플](sql-database-cli-samples.md)
  - [SQL Database에 대한 Azure PowerShell 샘플](sql-database-powershell-samples.md)

- 새 기능이 발표될 때의 자세한 내용은 [SQL Database에 대한 Azure 로드맵을](https://azure.microsoft.com/roadmap/?category=databases)참조하십시오.
- SQL Server 제품 팀 구성원이 SQL Database 뉴스 및 기능에 대해 블로그를 작성하는 [Azure SQL Database 블로그를](https://azure.microsoft.com/blog/topics/database)참조하십시오.

