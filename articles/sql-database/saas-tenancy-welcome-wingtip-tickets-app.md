---
title: "Wingtips 앱 시작 - Azure SQL Database | Microsoft Docs"
description: "클라우드 환경의 Azure SQL Database에 대한 샘플 Wingtips SaaS 응용 프로그램 및 데이터베이스 테넌시 모델에 대해 알아봅니다."
keywords: "SQL Database 자습서"
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Wingtip Tickets 샘플 SaaS Azure SQL Database 테넌시 앱 시작

Wingtip Tickets 샘플 SaaS Azure SQL Database 테넌시 응용 프로그램 및 해당 자습서 시작 데이터베이스 테넌시는 앱을 통해 응용 프로그램에서 호스트하는 비용을 지급하는 클라이언트에 제공하는 데이터 격리 모드를 나타냅니다. 지금은 간단하게 설명하기 위해 각 클라이언트에 전체 전용 데이터베이스가 있거나 클라이언트가 다른 클라이언트와 데이터베이스를 공유합니다.

## <a name="wingtip-tickets-app"></a>Wingtip Tickets 앱

Wingtip Tickets 응용 프로그램 예제는 다중 테넌트 SaaS 응용 프로그램의 디자인 및 관리에 미치는 다양한 데이터베이스 테넌시 모델의 효과를 설명합니다. 함께 제공되는 자습서는 동일한 효과를 직접 설명합니다. Wingtip Tickets는 Azure SQL Database에 빌드됩니다.

Wingtip Tickets는 실제 SaaS 클라이언트에서 사용되는 다양한 디자인 및 관리 시나리오를 처리하도록 설계되었습니다. Wingtip Tickets에서는 새롭게 등장한 사용 패턴이 파악됩니다.

5분 내에 Azure 구독으로 Wingtip Tickets 앱을 설치할 수 있습니다. 설치에는 여러 테넌트에 대한 샘플 데이터 삽입이 포함됩니다. 설치는 서로 상호 작용하거나 충돌하지 않으므로 모든 모델에 대한 응용 프로그램 및 관리 스크립트를 안전하게 설치할 수 있습니다.

#### <a name="code-in-github"></a>Github의 코드

응용 프로그램 코드 및 관리 스크립트는 GitHub에서 모두 사용할 수 있습니다.

- **독립 실행형 앱** 모델: [WingtipTicketsSaaS-StandaloneApp 리포지토리](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- **테넌트당 데이터베이스** 모델: [WingtipTicketsSaaS-DbPerTenant 리포지토리](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- **분할된 다중 테넌트** 모델: [WingtipTicketsSaaS-MultiTenantDB 리포지토리](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

Wingtip Tickets 앱에 대한 동일한 코드베이스가 나열된 이전 모델에 모두 다시 사용됩니다. Github의 코드를 사용하여 자체 SaaS 프로젝트를 시작할 수 있습니다.



## <a name="major-database-tenancy-models"></a>주요 데이터베이스 테넌시 모델

Wingtip Tickets는 SaaS 응용 프로그램을 나열하고 티켓팅하는 이벤트입니다. Wingtip은 장소별로 필요한 서비스를 제공합니다. 다음 항목은 모두 각 장소에 적용됩니다.

- 응용 프로그램에서 호스트하는 만큼만 요금을 결제합니다.
- Wingtip의 *테넌트*입니다.
- 이벤트를 호스트합니다. 다음 이벤트가 관련됩니다.
    - 티켓 가격.
    - 티켓 판매.
    - 티켓을 구매하는 고객.

관리 스크립트 및 자습서와 함께 앱을 통해 전체 SaaS 시나리오를 보여 줍니다. 시나리오에는 다음 활동이 포함됩니다.

- 테넌트 프로비저닝.
- 성능 모니터링 및 관리.
- 스키마 관리입니다.
- 교차 테넌트 보고 및 분석.

이러한 모든 활동이 필요한 크기에 관계없이 제공됩니다.



## <a name="code-samples-for-each-tenancy-model"></a>각 테넌시 모델에 대한 코드 샘플

응용 프로그램 모델 집합이 강조됩니다. 그러나 다른 구현은 두 개 이상 모델의 요소를 혼합할 수 있습니다.

#### <a name="standalone-app-model"></a>독립 실행형 앱 모델

![독립 실행형 앱 모델][standalone-app-model-62s]

이 모델은 단일 테넌트 응용 프로그램을 사용합니다. 따라서 이 모델에는 하나의 데이터베이스만 필요하며, 하나의 테넌트에 대한 데이터만 저장됩니다. 테넌트는 데이터베이스의 다른 테넌트에서 완전한 격리를 활용할 수 있습니다.

각 클라이언트가 자동으로 실행되려면 다양한 클라이언트에 앱 인스턴스를 판매할 경우 이 모델을 사용할 수 있습니다. 이렇게 하면 클라이언트가 유일한 테넌트가 됩니다. 데이터베이스는 한 클라이언트에 대한 데이터만 저장하지만 클라이언트의 많은 고객에 대한 데이터도 저장합니다.

#### <a name="database-per-tenant"></a>테넌트당 데이터베이스

![테넌트당 데이터베이스 모델][database-per-tenant-model-35d]

이 모델의 경우 응용 프로그램 인스턴스에 여러 테넌트가 있습니다. 각각의 새 테넌트에 대해 또 다른 데이터베이스가 새 테넌트에서만 사용하도록 할당됩니다.

이 모델은 각 테넌트에 대한 전체 데이터베이스 격리를 제공합니다. Azure SQL Database 서비스에는 이 모델을 타당하게 만드는 정교함이 있습니다.

- [SQL Database 다중 테넌트 SaaS 앱 예제 소개][saas-dbpertenant-wingtip-app-overview-15d] - 이 모델에 대한 자세한 정보를 제공합니다.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>분할된 다중 테넌트 데이터베이스, 하이브리드

![분할된 다중 테넌트 데이터베이스 모델, 하이브리드][sharded-multitenantdb-model-hybrid-79m]

이 모델의 경우 응용 프로그램 인스턴스에 여러 테넌트가 있습니다. 또한 이 모델의 일부 또는 모든 데이터베이스에 여러 개의 테넌트가 있습니다. 이 모델은 값이 전체 데이터베이스 격리인 경우 클라이언트가 더 많은 비용을 지급할 수 있도록 다양한 서비스 계층을 제공하는 데 좋습니다.

모든 데이터베이스의 스키마에 테넌트 식별자가 포함됩니다. 테넌트 식별자는 테넌트를 하나만 저장하는 데이터베이스에도 있습니다.

- [SQL Database 다중 테넌트 SaaS 앱 예제 소개][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>각 테넌시 모델에 대한 자습서

각 테넌시 모델은 다음을 사용하여 설명됩니다.

- 자습서 문서 집합.
- 모델 전용 Github 리포지토리에 저장된 소스 코드:
    - Wingtip Tickets 응용 프로그램에 대한 코드.
    - 관리 시나리오에 대한 스크립트 코드.

#### <a name="tutorials-for-management-scenarios"></a>관리 시나리오에 대한 자습서

각 모델에 대한 자습서 문서에서는 다음 관리 시나리오를 다룹니다.

- 테넌트 프로비저닝.
- 성능 모니터링 및 관리.
- 스키마 관리입니다.
- 교차 테넌트 보고 및 분석.
- 하나의 테넌트를 이전 시점으로 복원.
- 재해 복구.



## <a name="next-steps"></a>다음 단계

- [SQL Database 다중 테넌트 SaaS 앱 예제 소개][saas-dbpertenant-wingtip-app-overview-15d] - 이 모델에 대한 자세한 정보를 제공합니다.

- [다중 테넌트 SaaS 데이터베이스 테넌시 패턴][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "독립 실행형 앱 모델"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "테넌트당 데이터베이스 모델"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "분할된 다중 테넌트 데이터베이스 모델, 하이브리드"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


