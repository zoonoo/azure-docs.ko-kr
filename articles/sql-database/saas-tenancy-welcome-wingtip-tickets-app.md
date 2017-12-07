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
ms.date: 11/17/2017
ms.author: billgib
ms.openlocfilehash: 2a36df0e45af5bcce5338d04b7e1ba44221ae964
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS 응용 프로그램

동일한 *Wingtip Tickets* 응용 프로그램이 각 3개 샘플에서 구현됩니다. 이 앱은 극장, 클럽 등 작은 장소를 대상으로 간단한 이벤트를 나열하며 티켓팅하는 SaaS 앱입니다. 각 장소는 앱의 테넌트이며, 장소 세부 정보, 고객, 티켓 주문, 이벤트 목록 등의 자체 데이터를 갖습니다.  관리 스크립트 및 자습서와 함께 앱을 통해 종단 간 SaaS 시나리오를 보여 줍니다. 여기에는 테넌트 프로비전, 성능 모니터링 및 관리, 스키마 관리, 테넌트 간 보고 및 분석이 포함됩니다.

## <a name="three-saas-application-patterns"></a>세 가지 SaaS 응용 프로그램 패턴

세 버전의 앱을 사용할 수 있으며, 각각 Azure SQL Database에서 다른 데이터베이스 테넌트 패턴을 보여 줍니다.  첫 번째는 단일 테넌트 응용 프로그램과 격리된 단일 테넌트 데이터베이스를 사용합니다. 두 번째는 다중 테넌트 앱과 테넌트별 데이터베이스를 사용합니다. 세 번째 샘플은 다중 테넌트 앱과 분할된 다중 테넌트 데이터베이스를 사용합니다.

![세 가지 테넌트 패턴][image-three-tenancy-patterns]

 각 샘플에는 자체 응용 프로그램에서 사용할 수 있는 다양한 디자인 및 관리 패턴을 보여 주는 관리 스크립트 및 자습서가 있습니다.  각 샘플은 5분 내에 배포됩니다.  세 패턴은 모두 나란히 배포되어 디자인 및 관리의 차이를 비교할 수 있습니다.

## <a name="standalone-application-pattern"></a>독립 실행형 응용 프로그램 패턴

독립 실행형 앱 패턴은 단일 테넌트 응용 프로그램과 각 테넌트에 대해 단일 테넌트 데이터베이스를 사용합니다. 각 테넌트의 앱은 별도의 Azure 리소스 그룹에 배포됩니다. 이는 서비스 공급자의 구독 또는 테넌트의 구독에 있을 수 있으며 테넌트를 대신하여 공급자가 관리할 수 있습니다. 이 패턴은 가장 크게 테넌트를 격리할 수 있지만 여러 테넌트 간에 리소스를 공유할 수 없기 때문에 비용이 가장 높은 편입니다.

[자습서][docs-tutorials-for-wingtip-sa] 및 GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa]의 코드를 확인합니다.

## <a name="database-per-tenant-pattern"></a>테넌트별 데이터베이스 패턴

테넌트별 데이터베이스 패턴은 테넌트 격리에 관심이 있고 공유 리소스를 비용 효율적으로 사용할 수 있는 중앙 집중식 서비스를 실행하려는 서비스 공급자에게 효과적입니다. 데이터베이스는 각 장소(또는 테넌트)에 대해 만들어지며, 모든 데이터베이스가 중앙에서 관리됩니다. 데이터베이스가 탄력적 풀에 호스트되어 비용 효율적이고 쉽게 성능을 관리할 수 있으며, 이는 테넌트의 예측할 수 없는 워크로드 패턴을 활용합니다. 카탈로그 데이터베이스는 테넌트와 해당 데이터베이스 간의 매핑을 포함합니다. 이 매핑은 [Elastic Database 클라이언트 라이브러리](sql-database-elastic-database-client-library.md)의 분할 맵 관리 기능을 사용하여 관리되며, 이를 통해 응용 프로그램에 대한 연결을 효율적으로 관리할 수 있습니다.

GitHub[.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt]에서 [자습서][docs-tutorials-for-wingtip-dpt] 및 코드를 확인하세요.

## <a name="sharded-multi-tenant-database-pattern"></a>분할된 다중 테넌트 데이터베이스 패턴

다중 테넌트 데이터베이스는 테넌트당 낮은 비용을 원하며 테넌트 격리를 줄여도 되는 서비스 공급자에게 효과적입니다. 이 패턴을 사용하면 많은 테넌트를 단일 데이터베이스로 압축할 수 있으며 테넌트당 비용을 낮춥니다. 여러 데이터베이스에서 테넌트를 분할하여 거의 무제한으로 확장할 수 있습니다.  카탈로그 데이터베이스에서 테넌트를 데이터베이스로 다시 매핑합니다.  

또한 이 패턴을 통해 여러 테넌트를 하나의 데이터베이스에서 사용하여 비용을 최적화하거나 단일 테넌트를 해당 자체 데이터베이스에서 사용하여 격리를 최적화하는 하이브리드 모델을 사용할 수 있습니다. 테넌트가 프로비전될 때 또는 그 후에 테넌트별로 응용 프로그램에 영향을 미치지 않고 적용할 수 있습니다.

GitHub[.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt]에서 [자습서][docs-tutorials-for-wingtip-mt] 및 코드를 확인하세요.

## <a name="next-steps"></a>다음 단계

#### <a name="conceptual-descriptions"></a>개념적 설명

- [다중 테넌트 SaaS 데이터베이스 테넌트 패턴][saas-tenancy-app-design-patterns-md]에 응용 프로그램 테넌트 패턴에 대해 자세히 설명되어 있습니다.

#### <a name="tutorials-and-code"></a>자습서 및 코드

- 독립 실행형 앱:
    - [독립 실행형 앱에 대한 자습서][docs-tutorials-for-wingtip-sa].
    - [독립 실행형에 대한 코드, GitHub][github-code-for-wingtip-sa].

- 테넌트당 데이터베이스:
    - [테넌트별 데이터베이스에 대한 자습서][docs-tutorials-for-wingtip-dpt].
    - [테넌트별 데이터베이스에 대한 코드, GitHub][github-code-for-wingtip-dpt].

- 분할된 다중 테넌트:
    - [분할된 다중 테넌트에 대한 자습서][docs-tutorials-for-wingtip-mt].
    - [분할된 다중 테넌트에 대한 코드, GitHub][github-code-for-wingtip-mt].



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "세 가지 테넌트 패턴."

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: https://aka.ms/wingtipticketssaas-sa
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: https://aka.ms/wingtipticketssaas-dpt
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: https://aka.ms/wingtipticketssaas-mt
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb

