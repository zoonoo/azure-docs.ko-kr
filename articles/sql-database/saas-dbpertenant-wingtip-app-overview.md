---
title: "Azure SQL Database 다중 테넌트 앱 예제 - Wingtip SaaS | Microsoft Docs"
description: "Azure SQL Database를 사용하는 샘플 다중 테넌트 응용 프로그램인 Wingtip SaaS 예제 사용에 대해 알아봅니다."
keywords: "SQL Database 자습서"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: 2871d2b1208013808958e8a5b0c62fce31af86ec
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/22/2018
---
# <a name="introduction-to-a-multi-tenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>SQL Database를 사용하여 테넌트별 데이터베이스 패턴을 사용하는 멀티 테넌트 SaaS 앱 소개

멀티 테넌트 앱의 예로 *Wingtip SaaS* 응용 프로그램을 들 수 있습니다. 이 앱은 테넌트별 데이터베이스 SaaS 응용 프로그램 패턴을 사용하여 여러 테넌트에 서비스를 제공합니다. 이 앱을 통해 몇 가지 SaaS 디자인과 관리 패턴을 사용하여 SaaS 시나리오를 지원하는 Azure SQL Database의 기능을 살펴볼 수 있습니다. 신속하게 가동 및 실행하기 위해 Wingtip SaaS 앱에서 5분 내에 배포합니다.

응용 프로그램 소스 코드 및 관리 스크립트는 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub 리포지토리에서 사용할 수 있습니다. 시작하기 전에 먼저 [일반 지침](saas-tenancy-wingtip-app-guidance-tips.md)에서 Wingtip Tickets 관리 스크립트를 다운로드하고 차단을 해제하는 방법을 확인합니다.

## <a name="application-architecture"></a>응용 프로그램 아키텍처

Wingtip SaaS 앱은 테넌트별 데이터베이스 모델을 사용하며 효율을 극대화하기 위해 SQL 탄력적 풀을 사용합니다. 테넌트를 해당 데이터에 매핑하고 프로비전하는 데 카탈로그 데이터베이스가 사용됩니다. 핵심 Wingtip SaaS 응용 프로그램은 세 가지 샘플 테넌트가 있는 풀 및 카탈로그 데이터베이스를 사용합니다. 다양한 Wingtip SaaS 자습서를 완료하면 분석 데이터베이스, 데이터베이스 간 스키마 관리 등을 도입하여 초기 배포에 대한 추가 기능이 제공됩니다.


![Wingtip SaaS 아키텍처](media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


자습서를 진행하고 앱을 사용하는 동안 데이터 계층과 관련된 SaaS 패턴에 중점을 두어야 합니다. 즉, 데이터 계층에 집중하고 앱 자체를 과도하게 분석하지 마세요. 이러한 SaaS 패턴의 구현 방식을 이해하는 것은 특정 비즈니스 요구 사항에 필요한 모든 수정을 고려하면서 응용 프로그램에서 이러한 패턴을 구현하기 위한 열쇠입니다.

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL Database Wingtip SaaS 자습서

앱을 배포한 후 초기 배포 시 작성된 다음 자습서를 탐색합니다. 이러한 자습서에서는 SQL Database, SQL Data Warehouse 및 기타 Azure 서비스의 기본 제공 기능을 활용하는 일반적인 SaaS 패턴을 살펴봅니다. 자습서에는 PowerShell 스크립트와 함께 응용 프로그램에서 동일한 SaaS 관리 패턴에 대한 이해 및 구현을 간소화해주는 상세한 설명이 포함됩니다.


| 자습서 | 설명 |
|:--|:--|
| [Azure SQL Database 다중 테넌트 SaaS 앱 예제에 대한 지침 및 팁](saas-tenancy-wingtip-app-guidance-tips.md) | **여기에서 시작하세요.** PowerShell 스크립트를 다운로드한 후 실행하여 응용 프로그램 부분을 준비하세요. |
|[Wingtip SaaS 응용 프로그램 배포 및 탐색](saas-dbpertenant-get-started-deploy.md)|  Azure 구독에 Wingtip SaaS 응용 프로그램을 배포하고 탐색합니다. |
|[프로비전 및 카탈로그 테넌트](saas-dbpertenant-provision-and-catalog.md)| 카탈로그 데이터베이스를 사용하여 응용 프로그램을 테넌트에 연결하는 방법 및 카탈로그가 테넌트를 해당 데이터에 매핑하는 방법을 알아봅니다. |
|[성능 모니터링 및 관리](saas-dbpertenant-performance-monitoring.md)| SQL Database의 모니터링 기능을 사용하는 방법 및 성능 임계값이 초과된 경우 경고를 설정하는 방법을 알아봅니다. |
|[Log Analytics(OMS)로 모니터링](saas-dbpertenant-log-analytics.md) | [Log Analytics](../log-analytics/log-analytics-overview.md)를 사용하여 여러 풀에 걸쳐 있는 많은 양의 리소스를 모니터링하는 방법을 알아봅니다. |
|[단일 테넌트 복원](saas-dbpertenant-restore-single-tenant.md)| 테넌트 데이터베이스를 이전 시점으로 복원하는 방법을 알아봅니다. 기존 테넌트 데이터베이스를 온라인으로 유지한 상태에서 병렬 데이터베이스를 복원하는 단계도 포함됩니다. |
|[테넌트 데이터베이스 스키마 관리](saas-tenancy-schema-management.md)| 모든 테넌트 데이터베이스를 대상으로 스키마와 참조 데이터를 업데이트하는 방법을 알아봅니다. |
|[교차 테넌트 분산 쿼리 실행](saas-tenancy-cross-tenant-reporting.md) | 임시 분석 데이터베이스를 만들고 모든 테넌트에서 실시간 분산 쿼리를 실행합니다.  |
|[추출된 테넌트 데이터를 이용하여 분석 실행](saas-tenancy-tenant-analytics.md) | 오프라인 분석 쿼리를 실행하기 위해 테넌트 데이터를 분석 데이터베이스 또는 데이터 웨어하우스로 추출합니다. |


## <a name="next-steps"></a>다음 단계

- [Wingtip Tickets SaaS 앱 예제를 배포하고 사용하기 위한 일반 지침과 팁](saas-tenancy-wingtip-app-guidance-tips.md)

- [Wingtip SaaS 응용 프로그램 배포](saas-dbpertenant-get-started-deploy.md)
