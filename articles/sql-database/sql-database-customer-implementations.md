---
title: "Azure SQL Database 고객 구현 - 기술 | Microsoft Docs"
description: "비즈니스 문제를 해결하기 위한 Azure SQL Database 고객 구현에 대한 기술 정보 알아보기"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: customer implementations
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/03/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b05b24bf9a14570b07c2c1ab3076835c7167b647
ms.lasthandoff: 03/10/2017


---
# <a name="azure-sql-database-customer-implementation-technical-studies"></a>Azure SQL Database 고객 구현 기술 사례

- [Daxko](sql-database-implementation-daxko.md): Daxko/CSI Software는 한 가지 문제에 직면해 있습니다. 이 기업에서는 포괄적인 엔터프라이즈 소프트웨어 솔루션의 성공 덕분에 피트니스 및 레크리에이션 센터의 고객 기반이 빠르게 증가하고 있지만 증가하는 고객 기반의 IT 인프라 요구를 충족하기 위해 회사의 IT 직원을 테스트하고 있습니다. 이 회사는 특히 증가하는 데이터베이스 관리를 위한 운영 오버헤드 증가로 압박을 받고 있습니다. 더 나쁜 점은 운영 오버헤드 때문에 새 이니셔티브(예: 회사 소프트웨어에 대한 새로운 모바일 기능)를 위한 개발 리소스가 감소하고 있다는 것이었습니다.

- [GEP](sql-database-implementation-gep.md): GEP는 전 세계의 조달 책임자가 비즈니스 작업, 전략 및 재무 성능에 미치는 영향을 최대화할 수 있도록 하는 소프트웨어와 서비스를 제공 합니다. 이 회사는 컨설팅 서비스 및 관리되는 서비스 외에도, 클라우드 기반의 포괄적인 조달 소프트웨어 플랫폼인 SMART by GEP®를 제공합니다. 그러나 GEP는 자체 온-프레미스 데이터 센터에서 SMART by GEP와 같은 솔루션을 지원하려고 할 때 한계에 부딪혔습니다. 필요한 투자금은 폭발적으로 증가했으며 다른 국가의 규정 요구 때문에 필요한 투자금을 확보하는 것도 요원했습니다. GEP는 클라우드로 전환하여 IT 리소스를 확보함으로써, IT 운영에 대한 부담을 줄이고, 전 세계 고객을 위해 새로운 가치를 개발하는 데 집중할 수 있게 되었습니다.

- [SnelStart](sql-database-implementation-snelstart.md): SnelStart는 네덜란드에서 인기리에 사용되는 SMB(중소기업)을 위한 재무 및 비즈니스 관리 소프트웨어를 제작합니다. 이 회사의 55,000여 고객은 IT 직원 35명을 포함하여 110명의 직원에게 서비스를 받습니다. SnelStart는 데스크톱 소프트웨어에서 Azure에서 구축되는 SaaS(software-as-a-service) 제품으로 전환하면서, 기본 제공 서비스를 활용하고, C#의 친숙한 환경을 사용하여 관리를 자동화하고, 탄력적 풀을 통해 비즈니스의 과도한 프로비전 또는 부족한 프로비전 없이 성능 및 확장성을 최적화했습니다. SnelStart는 Azure를 사용하여 온-프레미스와 클라우드 간에 고객을 유연하게 이동할 수 있습니다.

- [Umbraco](sql-database-implementation-umbraco.md): 고객 배포를 단순화하기 위해 Umbraco는 UaaS(Umbraco-as a Service)를 추가했습니다. 이 SaaS(software-as-a-service)는 개발자가 솔루션 관리가 아닌 제품 혁신에 집중하도록 함으로써 온-프레미스 배포에 대한 필요성을 없애고, 기본 제공 규모 조정 기능을 제공하고, 관리 오버헤드를 없애줍니다. Umbraco는 Microsoft Azure에서 제공하는 유연한 PaaS(platform-as-a-service) 모델을 활용하여 이러한 모든 혜택을 제공할 수 있습니다.
