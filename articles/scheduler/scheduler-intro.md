---
title: Azure Scheduler 정의 | Microsoft Docs
description: Azure 내부 또는 외부에서 서비스를 호출하는 자동화된 작업을 만들기, 예약 및 실행하는 방법을 알아봅니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: hero-article
ms.date: 09/17/2018
ms.openlocfilehash: fb3fba7fa86027d753be21e028109a5d7ef03efb
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722484"
---
# <a name="what-is-azure-scheduler"></a>Azure Scheduler 정의

> [!IMPORTANT]
> Azure Scheduler는 조만간 사용 중지되고 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)로 대체됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/)는 작업을 선언적으로 설명하여 클라우드에서 실행되는 [작업](../scheduler/scheduler-concepts-terms.md)을 만드는 데 도움이 됩니다. 그런 다음, 해당 작업이 자동으로 예약되고 실행됩니다. 예를 들어, HTTP 또는 HTTPS 엔드포인트 호출과 같이 Azure 내부 및 외부에서 서비스를 호출하고, Azure Storage 큐 및 Azure Service Bus 큐 또는 토픽에 메시지를 게시할 수도 있습니다. 작업은 바로 또는 나중에 실행할 수 있습니다. Scheduler는 [복잡한 일정 및 고급 되풀이](../scheduler/scheduler-advanced-complexity.md)를 손쉽게 지원합니다. Scheduler는 작업을 실행하는 경우를 지정하고, 검토할 수 있는 작업 결과의 기록을 유지한 다음, 예측 가능하고 안정적으로 실행할 워크로드를 예약합니다.

Scheduler를 사용하여 예약된 워크로드를 만들기, 유지 관리 및 실행할 수는 있지만, Scheduler가 워크로드를 호스트하거나 코드를 실행하지는 않습니다. 서비스는 Azure, 온-프레미스 또는 다른 공급자를 통해 다른 곳에서 호스트되는 서비스 또는 코드를 *호출*하기만 합니다. Scheduler는 HTTP, HTTPS, Storage 큐, Service Bus 큐 또는 Service Bus 토픽을 통해 호출합니다. 작업을 만들기, 관리 및 예약하려면 [Azure Portal](../scheduler/scheduler-get-started-portal.md), 코드, [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/) 또는 [Azure Scheduler PowerShell cmdlet 참조](scheduler-powershell-reference.md)를 사용할 수 있습니다. 예를 들어 Azure Portal에서 스크립트를 사용하여 프로그래밍 방식으로 작업 및 [작업 컬렉션](../scheduler/scheduler-concepts-terms.md)을 만들기, 보기, 업데이트, 관리 또는 삭제할 수 있습니다.

[Azure WebJobs](../app-service/webjobs-create.md)(Azure App Service에서 [Web Apps](https://azure.microsoft.com/services/app-service/web/) 기능의 일부)와 같은 다른 Azure 일정 기능은 백그라운드에서 Scheduler를 사용합니다. [Scheduler REST API](https://docs.microsoft.com/rest/api/scheduler/)를 사용하여 이러한 작업에 대한 통신을 관리할 수 있습니다. 이러한 작업에 대한 통신을 관리하는 데 유용합니다.

Scheduler가 유용할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* **앱 작업 되풀이 실행**: 예를 들어 정기적으로 Twitter에서 피드로 데이터를 수집합니다.

* **일별 유지 관리 수행**: 일별 로그 잘라내기, 백업 수행 및 기타 유지 관리 작업입니다. 

  예를 들어 관리자가 향후 9개월 간 매일 오전 1시에 데이터베이스를 백업할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 Scheduler 사용 시작](scheduler-get-started-portal.md)
* [Azure Scheduler의 플랜 및 청구](scheduler-plans-billing.md)에 대해 알아봅니다.
* [Azure Scheduler를 사용하여 복잡한 일정 및 고급 되풀이를 빌드하는 방법](scheduler-advanced-complexity.md)을 알아봅니다.