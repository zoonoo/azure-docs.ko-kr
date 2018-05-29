---
title: 온-프레미스 데이터센터를 Azure로 마이그레이션 | Microsoft Docs
description: 온-프레미스 데이터센터를 Azure로 마이그레이션하는 방법에 대한 개요 백서를 읽을 수 있습니다.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/21/2018
ms.author: raynew
ms.openlocfilehash: 8ba490998ea5f20efca591327716a6e39e9c1ba8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167758"
---
# <a name="migrating-your-on-premises-workloads-to-azure"></a>온-프레미스 워크로드를 Azure로 마이그레이션


Microsoft Azure는 개발자 및 IT 전문가가 데이터센터의 글로벌 네트워크를 통해 다양한 도구 및 프레임워크에서 응용 프로그램을 작성, 배포 및 관리하는 데 사용할 수 있는 포괄적인 클라우드 서비스 집합에 대한 액세스를 제공합니다. 비즈니스가 디지털 전환과 관련된 문제에 직면하면서 Azure 클라우드를 사용하면 리소스 및 운영을 최적화하고, 고객 및 직원과 소통하고, 제품을 혁신하는 방법을 파악하는 데 도움이 됩니다.

하지만 속도와 유연성, 최소화된 비용, 성능 및 안정성 측면에서 클라우드가 제공하는 모든 장점에도 불구하고, 앞으로 얼마간은 많은 조직에서 온-프레미스 데이터센터를 사용할 필요가 있습니다. 클라우드 채택 장벽에 대한 대응으로 Azure는 온-프레미스 데이터센터와 Azure 공용 클라우드 간의 브리지를 구축하는 하이브리드 클라우드 전략을 제공합니다. 예를 들면, 백업과 같은 Azure 클라우드 리소스를 사용하여 온-프레미스 리소스를 보호하거나 Azure 분석을 사용하여 온-프레미스 워크로드에 대한 인사이트를 얻을 수 있습니다. 

하이브리드 클라우드 전략의 일환으로 Azure는 온-프레미스 앱 및 워크로드를 클라우드로 마이그레이션할 수 있는 성장하는 솔루션을 제공합니다. 간단한 단계를 통해 온-프레미스 리소스를 종합적으로 평가하여 Azure 클라우드에서 어떻게 실행될지를 파악할 수 있습니다. 그런 다음, 심층적인 평가를 통한 확신을 가지고 리소스를 Azure로 마이그레이션할 수 있습니다. Azure에서 리소스가 시작되어 실행되면 리소스를 최적화하여 액세스, 유연성, 보안 및 안정성을 유지하고 향상시킬 수 있습니다.

마이그레이션 문서 시리즈에는 회사에 대한 마이그레이션 전략을 계획하고 구축하는 방법이 설명되어 있습니다. 시간이 지나면서 점점 더 복잡해지는 여러 가지 시나리오가 문서에 설명되어 있습니다. 이러한 시나리오는 아래 표에 요약되어 있습니다. 각 시나리오마다 개요, 마이그레이션 아키텍처가 제공되고 마이그레이션 프로세스 관련 단계가 설명됩니다. 

**시나리오** | **해결 방법** | **서비스** | **문서** 
--- | --- | --- | ---
[시나리오 1: 검색 및 평가](migrate-scenarios-assessment.md) | Azure로 마이그레이션을 위한 온-프레미스 앱, 데이터 및 인프라 검색 및 평가 | Data Migration Assistant, Azure Migrate 서비스  | 지금 사용 가능
**[시나리오 2: 앱 다시 호스트](migrate-scenarios-lift-and-shift.md)** | Azure로 응용 프로그램을 전환합니다. | Azure Site Recovery, Azure Database Migration Service, Azure SQL 관리되는 인스턴스 | 지금 사용 가능
**시나리오 3: 앱 리팩터링** | Azure로 마이그레이션하는 동안 앱을 리팩터링합니다. | 계획 중 | 계획
**시나리오 4: 앱 아키텍처 변경** | Azure로 마이그레이션하는 동안 앱의 아키텍처를 변경합니다. | 계획 중 | 계획
**시나리오 5: 앱 다시 빌드** |Azure로 마이그레이션하는 동안 앱을 다시 빌드합니다. | 계획 중 | 계획




