---
title: "SQL 데이터베이스 정의 SQL Database 소개 | Microsoft Docs"
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: 2d0792046bf55d691df21e26f2df23235318665c


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>SQL 데이터베이스 정의 SQL 데이터베이스 소개
SQL 데이터베이스는 시장 선도적인 Microsoft SQL Server 엔진을 기반으로 한 클라우드 환경의 관계형 데이터베이스 서비스로, 업무 수행에 중요한 기능을 제공합니다. SQL 데이터베이스는 예측 가능한 성능, 가동 중지 시간이 없는 확장성, 비즈니스 연속성, 데이터 보호 기능을 제공하며 관리할 필요가 거의 없습니다. 개발자는 가상 컴퓨터와 인프라 관리가 아닌 빠른 앱 개발 및 제품 출시 기간 단축에 전념할 수 있습니다. SQL 데이터베이스는 [SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) 엔진을 기반으로 하여 기존 SQL Server 도구, 라이브러리 및 API를 지원하므로 클라우드로 더 쉽게 전환 및 확장할 수 있습니다.

이 문서에서는 세부 정보를 찾는 링크를 통해 성능, 확장성 및 관리 효율성과 관련된 SQL 데이터베이스의 핵심 개념과 기능을 소개합니다. 이동할 준비가 된 경우 바로 [먼저 SQL 데이터베이스를 만들거나](sql-database-get-started.md) [탄력적 풀을 만들 수](sql-database-elastic-pool-create-portal.md) 있습니다. 자세히 알아보려면 30분짜리 비디오를 보세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Database-create-DBs-in-seconds/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>가동 중지 시간 없이 성능 및 규모 조정
SQL 데이터베이스는 Basic, Standard 및 Premium *서비스 계층*에서 사용할 수 있습니다. 각 서비스 계층은 경량급에서 중량급까지 데이터베이스 워크로드를 지원하기 위해 [다양한 수준의 성능 및 기능](sql-database-service-tiers.md) 을 제공합니다. 한 달에 몇 달러로 작은 데이터베이스용 첫 번째 앱을 만든 다음 앱을 가상 환경으로 전환할 때 언제든지 앱 또는 고객에 대한 가동 중지 시간 없이 수동으로 또는 프로그램에 의해 [서비스 계층을 변경](sql-database-scale-up.md) 할 수 있습니다.

많은 업무와 앱에서, 특히 사용 패턴이 비교적 예측 가능한 경우 단일 데이터베이스를 만들고 필요에 따라 성능을 확장하거나 축소할 수 있으면 충분합니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다.

[탄력적 풀](sql-database-elastic-pool.md) 로 이 문제를 해결합니다. 개념은 간단합니다. 성능을 풀에 할당하고 단일 데이터베이스 성능이 아닌 풀의 집합적 성능에 대해 지불합니다. 데이터베이스 성능을 확장하거나 축소할 필요가 없습니다. *탄력적 데이터베이스*라는 풀의 데이터베이스가 요구 사항에 맞게 자동으로 확장 및 축소됩니다. 탄력적 데이터베이스는 풀의 한계를 사용하지만 초과하지 않으므로 데이터베이스 사용량을 예측할 수 없는 경우에도 비용을 계속 예측할 수 있습니다. 뿐만 아니라 [풀에 데이터베이스를 추가 및 제거](sql-database-elastic-pool-manage-portal.md)하여 소수의 데이터베이스에서 수천 개의 데이터베이스까지 자신이 관리하는 예산 범위 내에서 앱의 규모를 조정할 수 있습니다. 탄력적 풀을 사용하여 SaaS 응용 프로그램에 대한 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL 데이터베이스와 다중 테넌트 SaaS 응용 프로그램에 대한 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

— 단일이든 또는 탄력적이든 — 구애 받지 않습니다. 단일 데이터베이스와 탄력적 풀을 혼합하고 단일 데이터베이스와 풀의 서비스 계층을 신속하고 쉽게 변경하여 상황에 맞게 맞출 수 있습니다. 또한 Azure의 성능 및 도달 범위를 바탕으로 다른 Azure 서비스를 SQL Database와 조합하여 사용하면 고유한 앱 설계 요구를 충족시키고, 비용 및 리소스를 효율적으로 운용하고, 새로운 비즈니스 기회를 만들 수 있습니다.

하지만 데이터베이스와 데이터베이스 풀의 상대적 성능을 비교하려면 어떻게 해야 합니까? 확장 및 축소할 때 마우스 오른쪽 단추 클릭이 정지하는 것을 어떻게 알 수 있습니까? 답은 단일 데이터베이스용 DTU(Database Transaction Unit) 및 탄력적 데이터베이스 및 데이터베이스 풀용 eDTU(탄력적 DTU) 기반 성능 등급과 결합한 기본 제공 성능 모니터링 및 경고 도구에 있습니다. 이를 통해 현재 또는 프로젝트 성능 요구를 기반으로 확장 또는 축소의 영향을 빠르게 평가할 수 있습니다. 자세한 내용은 [SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해](sql-database-service-tiers.md) 를 참조하세요.

## <a name="keep-your-app-and-business-running"></a>앱 및 비즈니스 운영 유지
Azure의 업계 선도적인 99.99% 가용성 [SLA](http://azure.microsoft.com/support/legal/sla/)(서비스 수준 계약)를 Microsoft에서 관리되는 전 세계 데이터 센터 네트워크의 지원을 받아 앱을 연중 무휴(24/7)로 실행할 수 있습니다. 다른 방법으로 구입 또는 설계, 구축 및 관리해야 할 수 있는 기본 제공 보안, 내결함성 및 데이터 보호를 모든 SQL 데이터베이스에서 이용합니다. 그러한 경우 비즈니스 요구 사항에 따라 추가적인 보호 계층을 요구하여 재해, 오류 또는 다른 장애가 발생한 경우 앱 및 비즈니스를 신속하게 복구할 수 있습니다. SQL Database와 함께 각 서비스 계층은 이런 방식으로 시작하고 실행하고 계속하는 데 사용할 수 있는 포괄적인 비즈니스 연속성 기능 및 옵션 집합을 제공합니다. 시점 복원을 사용하여 데이터베이스를 최대 35일 전의 상태로 복원할 수 있습니다. 또한 데이터베이스를 호스팅하는 데이터 센터가 가동 중지하면 최근 백업의 지역 중복 복사본에서 데이터베이스를 복원하거나 여러 지역의 데이터베이스 복제본으로 장애 조치할 수 있습니다. 업그레이드 또는 다른 지역에 다시 배치하기 위해 복제본을 사용할 수도 있습니다.

![SQL 데이터베이스 지역에서 복제](./media/sql-database-technical-overview/azure_sqldb_map.png)

서로 다른 서비스 계층에 사용할 수 있는 다양한 무중단 업무 방식 기능에 대한 자세한 내용은 [무중단 업무 방식](sql-database-business-continuity.md) 을 참조하세요.

## <a name="secure-your-data"></a>데이터 보호
SQL Server에는 SQL Database가 액세스 제한, 데이터 보호 및 작업 모니터링 도움을 수행하는 기능을 통해 지원하는 확실한 데이터 보안의 전통을 이어갑니다. SQL 데이터베이스의 보안 옵션에 대한 빠른 요약은 [SQL 데이터베이스 보안](sql-database-security.md) 을 참조하세요. 보안 기능을 더 광범위하게 보려면 [SQL Server 데이터베이스 엔진 및 SQL 데이터베이스 보안 센터](https://msdn.microsoft.com/library/bb510589) 를 참조하세요. 그리고 Azure의 플랫폼 보안에 대한 자세한 내용을 보려면 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/security/) 를 방문하세요.

## <a name="next-steps"></a>다음 단계
SQL 데이터베이스에 대한 소개를 읽고 질문 "SQL 데이터베이스란?"에 답변했으므로 다음에 대한 준비가 되었습니다.

* 단일 데이터베이스 및 탄력적 풀의 비용 비교 및 계산기는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 참조하세요.
* [탄력적 풀](sql-database-elastic-pool.md)에 대해 알아보세요.
* [첫 번째 데이터베이스 만들기](sql-database-get-started.md)로 시작합니다.
* [SSMS를 사용하여 연결 및 쿼리](sql-database-connect-query-ssms.md)
* C#, Java, Node.js, PHP, Python 또는 Ruby에 첫 번째 앱 빌드: [SQL 데이터베이스 및 SQL Server에 대한 연결 라이브러리](sql-database-libraries.md)



<!--HONumber=Dec16_HO3-->


