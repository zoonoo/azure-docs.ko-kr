---
title: "Azure SQL Database 서비스란? | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "sql 소개, sql 소개, sql 데이터베이스란"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/17/2017
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a139b97e20685a5ced513c1fd180a74e91c497a7
ms.lasthandoff: 04/15/2017

---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>SQL 데이터베이스 정의 SQL 데이터베이스 소개
SQL Database는 시장을 선도하는 Microsoft SQL Server 엔진을 기반으로 하고 중요한 업무용 워크로드를 처리할 수 있는 Microsoft 클라우드의 관계형 데이터베이스 서비스입니다. SQL Database는 여러 서비스 수준에서 예측 가능한 성능, 가동 중지 시간이 없는 확장성, 기본 제공 비즈니스 연속성 및 데이터 보호 기능을 제공하며 관리할 필요가 거의 없습니다. 이러한 기능을 사용하면 가상 컴퓨터와 인프라를 관리하는 데 귀중한 시간과 리소스를 할당하는 대신 빠른 앱 개발에 집중하고 시장 출시 시간을 단축할 수 있습니다. SQL Database는 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 엔진을 기반으로 하므로 기존 SQL Server 도구, 라이브러리 및 API를 지원합니다. 결과적으로 새로운 솔루션을 개발하고 기존 SQL Server 솔루션을 이전하며 새로운 기술을 배우지 않고도 기존 SQL Server 솔루션을 Microsoft 클라우드로 확장하는 것이 쉽습니다.

이 문서에서는 세부 정보를 찾는 링크를 통해 성능, 확장성 및 관리 효율성과 관련된 SQL 데이터베이스의 핵심 개념과 기능을 소개합니다. 이러한 빠른 시작을 참조하여 다음 항목을 시작하세요.
 - [Azure Portal에서 SQL Database 만들기](sql-database-get-started-portal.md)  
 - [Azure CLI를 사용하여 SQL Database 만들기](sql-database-get-started-cli.md)
 - [PowerShell을 사용하여 SQL Database 만들기](sql-database-get-started-powershell.md)

일련의 Azure CLI 및 PowerShell 샘플은 다음을 참조하세요.
 - [Azure SQL Database에 대한 Azure CLI 샘플](sql-database-cli-samples.md)
 - [Azure SQL Database에 대한 Azure PowerShell 샘플](sql-database-powershell-samples.md)

## <a name="adjust-performance-and-scale-without-downtime"></a>가동 중지 시간 없이 성능 및 규모 조정
SQL Database 서비스는 네 가지 서비스 계층, 즉 Basic, Standard, Premium 및 Premium RS을 제공합니다. 각 서비스 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 [다양한 수준의 성능 및 기능](sql-database-service-tiers.md) 을 제공합니다. 한 달에 몇 달러의 작은 데이터베이스에 첫 번째 앱을 빌드한 다음 언제든지 수동 또는 프로그래밍 방식으로 [서비스 계층을 변경](sql-database-service-tiers.md)하여 솔루션의 요구 사항을 충족시킬 수 있습니다. 앱이나 고객에게 가동 중지 시간 없이 이를 수행할 수 있습니다. 동적 확장성을 통해 데이터베이스는 급변하는 리소스 요구 사항에 투명하게 대응할 수 있으며, 필요할 때 필요한 리소스에 대해서만 비용을 지불할 수 있습니다.

## <a name="elastic-pools-to-maximize-resource-utilization"></a>리소스 사용률 극대화를 위한 탄력적 풀
많은 업무와 앱에서, 특히 사용 패턴이 비교적 예측 가능한 경우 단일 데이터베이스를 만들고 필요에 따라 성능을 확장하거나 축소할 수 있으면 충분합니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다. [탄력적 풀](sql-database-elastic-pool.md)은 이 문제를 해결하도록 설계되었습니다. 개념은 간단합니다. 개별 데이터베이스가 아니라 풀에 성능 리소스를 할당하고 단일 데이터베이스 성능이 아니라 풀의 집합적 성능 리소스에 대해 요금을 지불합니다. 탄력적 풀을 사용하면 리소스에 대한 요구가 변동함에 따라 데이터베이스 성능을 높이거나 낮추는 데 집중할 필요가 없습니다. 풀링된 데이터베이스는 필요에 따라 탄력적 풀의 성능 리소스를 사용합니다. 풀링된 데이터베이스는 풀의 한도를 사용하지만 초과하지 않으므로 개별 데이터베이스 사용량을 예측할 수 없는 경우에도 비용을 계속 예측할 수 있습니다. 뿐만 아니라 [풀에 데이터베이스를 추가 및 제거](sql-database-elastic-pool-manage-portal.md)하여 소수의 데이터베이스에서 수천 개의 데이터베이스까지 자신이 관리하는 예산 범위 내에서 앱의 규모를 조정할 수 있습니다. 마지막으로 풀의 데이터베이스에서 사용할 수 있는 최소 및 최대 리소스를 제어하여 풀의 어떤 데이터베이스도 풀 리소스 전체를 사용하지 못하도록 하고 풀링된 모든 데이터베이스에 최소한의 리소스를 보장할 수 있습니다. 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

## <a name="blend-single-databases-with-pooled-databases"></a>단일 데이터베이스와 풀링된 데이터베이스의 혼합
사용자는 단일 데이터베이스와 탄력적 풀 중 어느 한 쪽으로만 한정되지는 않습니다. 즉 단일 데이터베이스와 탄력적 풀을 혼합하고, 단일 데이터베이스와 탄력적 풀의 서비스 계층을 상황에 맞게 빠르고 쉽게 변경할 수 있습니다. 또한 Azure의 성능 및 도달 범위를 바탕으로 다른 Azure 서비스를 SQL Database와 조합하여 사용하면 고유한 앱 설계 요구를 충족시키고, 비용 및 리소스를 효율적으로 운용하고, 새로운 비즈니스 기회를 만들 수 있습니다.

## <a name="monitoring-and-alerting"></a>모니터링 및 경고
그러나 단일 데이터베이스와 탄력적 풀의 상대적 성능을 비교하려면 어떻게 해야 합니까? 확장 및 축소할 때 마우스 오른쪽 단추 클릭이 정지하는 것을 어떻게 알 수 있습니까? [단일 데이터베이스의 DTU(데이터베이스 트랜잭션 단위) 및 탄력적 풀의 eDTU(탄력적 DTU)](sql-database-what-is-a-dtu.md)에 기반한 성능 등급과 결합된 [기본 제공 성능 모니터링](sql-database-performance.md) 및 [경고](sql-database-insights-alerts-portal.md) 도구를 사용합니다. 이 도구를 사용하면 현재 또는 프로젝트의 성능 요구에 기반하여 확장 또는 축소의 영향을 신속하게 평가할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해](sql-database-service-tiers.md) 를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 [SLA](http://azure.microsoft.com/support/legal/sla/)(서비스 수준 계약)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 구입 또는 설계, 구축 및 관리해야 할 수도 있는 기본 제공 보안, 내결함성 및 [데이터 보호](sql-database-automated-backups.md)를 모든 SQL 데이터베이스에서 이용합니다. SQL Database와 함께 각 서비스 계층은 이런 방식으로 시작하고 실행하고 계속하는 데 사용할 수 있는 포괄적인 비즈니스 연속성 기능 및 옵션 집합을 제공합니다. [특정 시점 복원](sql-database-recovery-using-backups.md)을 사용하면 데이터베이스를 최대 35일 전의 상태로 복원할 수 있습니다. [장기 백업 보존](sql-database-long-term-retention.md)을 구성하면 최대 10년 동안 보안 자격 증명 모음에 백업을 저장할 수 있습니다. 또한 데이터베이스를 호스팅하는 데이터 센터에서 중단이 발생하면 [최신 백업의 지역 중복 복사본](sql-database-recovery-using-backups.md)에서 데이터베이스를 복원할 수 있습니다. 데이터 센터가 중단된 경우 신속한 장애 조치를 위해 필요에 따라 하나 이상의 지역에서[읽을 수 있는 지역 중복 복제본](sql-database-geo-replication-overview.md)을 구성할 수도 있습니다. 또는 이러한 복제본을 사용하여 여러 지리적 영역에서 더 빠른 읽기 성능을 제공하거나 [가동 중지 시간 없이 응용 프로그램 업그레이드](sql-database-manage-application-rolling-upgrade.md)할 수도 있습니다. 

![SQL 데이터베이스 지역에서 복제](./media/sql-database-technical-overview/azure_sqldb_map.png)

서로 다른 서비스 계층에 사용할 수 있는 다양한 무중단 업무 방식 기능에 대한 자세한 내용은 [무중단 업무 방식](sql-database-business-continuity.md) 을 참조하세요.

## <a name="secure-your-data"></a>데이터 보호
SQL Server는 액세스를 제한하고 데이터를 보호하며 활동을 모니터링하는 기능으로 SQL Database에서 지지하는 데이터 보안의 전통을 계승하고 있습니다. SQL 데이터베이스의 보안 옵션에 대한 빠른 요약은 [SQL 데이터베이스 보안](sql-database-security-overview.md) 을 참조하세요. 보안 기능을 더 광범위하게 보려면 [SQL Server 데이터베이스 엔진 및 SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589) 를 참조하세요. 그리고 Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/security/) 를 방문하세요.

## <a name="next-steps"></a>다음 단계
SQL 데이터베이스에 대한 소개를 읽고 질문 "SQL 데이터베이스란?"에 답변했으므로 다음에 대한 준비가 되었습니다.

* 단일 데이터베이스 및 탄력적 풀의 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.
* [탄력적 풀](sql-database-elastic-pool.md)에 대해 알아보세요.
* [첫 번째 데이터베이스 만들기](sql-database-get-started-portal.md)로 시작합니다.
* C#, Java, Node.js, PHP, Python 또는 Ruby에 첫 번째 앱 빌드: [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)

