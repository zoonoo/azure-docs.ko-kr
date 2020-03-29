---
title: Azure Scheduler 정의
description: 일정을 만들고 Azure 내부 또는 외부에서 서비스를 호출하는 자동화된 작업을 실행합니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898548"
---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler 정의

> [!IMPORTANT]
> [Azure 논리 앱이](../logic-apps/logic-apps-overview.md) [사용 중지되는](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure 스케줄러를 대체합니다. 스케줄러에서 설정한 작업을 계속 작업하려면 가능한 한 빨리 [Azure Logic Apps로 마이그레이션하십시오.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> 스케줄러는 Azure 포털에서 더 이상 사용할 수 없지만 작업 및 작업 컬렉션을 관리할 수 있도록 [현재 REST API](/rest/api/scheduler) 및 Azure [스케줄러 PowerShell cmdlet을](scheduler-powershell-reference.md) 사용할 수 있습니다.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/)는 작업을 선언적으로 설명하여 클라우드에서 실행되는 [작업](../scheduler/scheduler-concepts-terms.md)을 만드는 데 도움이 됩니다. 그런 다음, 해당 작업이 자동으로 예약되고 실행됩니다. 예를 들어, HTTP 또는 HTTPS 엔드포인트 호출과 같이 Azure 내부 및 외부에서 서비스를 호출하고, Azure Storage 큐 및 Azure Service Bus 큐 또는 토픽에 메시지를 게시할 수도 있습니다. 작업은 바로 또는 나중에 실행할 수 있습니다. Scheduler는 [복잡한 일정 및 고급 되풀이](../scheduler/scheduler-advanced-complexity.md)를 손쉽게 지원합니다. Scheduler는 작업을 실행하는 경우를 지정하고, 검토할 수 있는 작업 결과의 기록을 유지한 다음, 예측 가능하고 안정적으로 실행할 워크로드를 예약합니다.

[Azure WebJobs](../app-service/webjobs-create.md)(Azure App Service에서 [Web Apps](https://azure.microsoft.com/services/app-service/web/) 기능의 일부)와 같은 다른 Azure 일정 기능은 백그라운드에서 Scheduler를 사용합니다. 이러한 작업에 대한 통신을 관리하는 데 도움이 되는 [Scheduler REST API를](https://docs.microsoft.com/rest/api/scheduler/)사용하여 이러한 작업에 대한 통신을 관리할 수 있습니다.

Scheduler가 유용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* 앱 작업 되풀이 실행: 예를 들어 정기적으로 Twitter에서 피드로 데이터를 수집합니다.

* 일별 유지 관리 수행: 일별 로그 잘라내기, 백업 수행 및 기타 유지 관리 작업입니다.

  예를 들어 관리자가 향후 9개월 간 매일 오전 1시에 데이터베이스를 백업할 수도 있습니다.

Scheduler를 사용하여 예약된 워크로드를 만들기, 유지 관리 및 실행할 수는 있지만, Scheduler가 워크로드를 호스트하거나 코드를 실행하지는 않습니다. 서비스는 Azure, 온-프레미스 또는 다른 공급자를 통해 다른 곳에서 호스트되는 서비스 또는 코드를 *호출*하기만 합니다. Scheduler는 HTTP, HTTPS, Storage 큐, Service Bus 큐 또는 Service Bus 토픽을 통해 호출합니다.

작업 및 [작업 컬렉션을](../scheduler/scheduler-concepts-terms.md)만들거나, 예약, 관리, 업데이트 또는 삭제하려면 코드, [스케줄러 REST API](https://docs.microsoft.com/rest/api/scheduler/)또는 Azure [스케줄러 PowerShell cmdlets를](scheduler-powershell-reference.md)사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler의 플랜 및 청구 방식](scheduler-plans-billing.md)
* [Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이 빌드](scheduler-advanced-complexity.md)
* [Azure Scheduler REST API 참조](/rest/api/scheduler)
* [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)
