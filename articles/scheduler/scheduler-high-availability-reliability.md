---
title: 고가용성 및 안정성 - Azure Scheduler
description: Azure Scheduler의 고가용성 및 안정성 알아보기
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533386"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Azure Scheduler의 고가용성 및 안정성

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려는 경우 [Azure Logic Apps를 대신 사용해 보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler는 작업에 [고가용성](https://docs.microsoft.com/azure/architecture/guide/pillars#availability)과 안정성을 제공합니다. 자세한 내용은 [Scheduler에 대한 SLA](https://azure.microsoft.com/support/legal/sla/scheduler)를 참조하세요.

## <a name="high-availability"></a>고가용성

Azure Scheduler는 [고가용성]을 제공하며 지리적 중복 서비스 배포와 지리적 지역 작업 복제를 모두 제공합니다.

### <a name="geo-redundant-service-deployment"></a>지리적 중복 서비스 배포

Azure Scheduler는 [현재 Azure에서 지원하는 거의 모든 지리적 지역](https://azure.microsoft.com/global-infrastructure/regions/#services)에서 Azure Portal을 통해 사용할 수 있습니다. 즉, 호스트되는 지역의 Azure 데이터 센터를 사용할 수 없게 되면 서비스의 장애 조치(failover) 기능이 다른 데이터 센터에서 Scheduler를 사용할 수 있게 해주기 때문에 Azure Scheduler를 계속 사용할 수 있습니다.

### <a name="geo-regional-job-replication"></a>지리적 지역 작업 복제

Azure Scheduler에 있는 사용자 고유의 작업은 모든 zure 지역에 복제됩니다. 따라서 한 지역에서 정전이 발생할 경우 Azure Scheduler가 장애 조치(failover)되고 작업이 쌍을 이루는 지리적 지역에 있는 다른 데이터 센터에서 실행됩니다.

예를 들어 미국 중남부에서 작업을 만들면 Azure Scheduler가 자동으로 작업을 미국 중북부에 복제합니다. 미국 중남부에서 장애가 발생하면 Azure Scheduler는 미국 중북부에서 작업을 실행합니다. 

![지리적 지역 작업 복제](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

또한 Azure Scheduler는 Azure에서 장애가 발생할 경우 같은 지역이지만 좀 더 넓은 지리적 지역 내에 데이터를 유지합니다. 따라서 고가용성만을 원하는 경우 작업을 복제할 필요가 없습니다. Azure Scheduler는 자동으로 작업에 대한 고가용성을 제공합니다.

## <a name="reliability"></a>안정성

Azure Scheduler는 자체 고가용성을 보장하지만 사용자가 만든 작업에는 다른 방식을 적용합니다. 예를 들어 사용자 작업이 사용 불가능한 HTTP 엔드포인트를 호출한다고 가정해 봅시다. Azure Scheduler는 여전히 오류를 처리하기 위한 대안을 제공하여 작업을 성공적으로 실행하려고 시도합니다. 

* 재시도 정책 설정.
* 대체 엔드포인트 설정.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>재시도 정책

Azure Scheduler를 사용하여 재시도 정책을 설정할 수 있습니다. 작업이 실패하면 Scheduler는 기본적으로 30초 간격으로 4회 더 작업을 재시도합니다. 이 재시도 정책을 보다 공격적으로(예: 30초 간격으로 10회) 설정할 수도 있고, 보다 덜 공격적으로(예: 하루에 2회) 설정할 수도 있습니다.

예를 들어 HTTP 엔드포인트를 호출하는 주간 작업을 만든다고 가정해 봅시다. 작업이 실행되는 몇 시간 동안 HTTP 엔드포인트를 사용할 수 없게 된 경우 작업이 다시 실행될 때까지 일주일을 더 기다려야 하는 상황을 원하는 분은 없을 것입니다. 이와 같은 상황은 기본 재시도 정책이 작동하지 않아서 발생합니다. 이와 같은 경우 30초 대신 3시간마다 재시도가 발생하도록 표준 재시도 정책을 변경하는 것이 좋습니다. 

재시도 정책을 설정하는 방법은 [retryPolicy](scheduler-concepts-terms.md#retrypolicy)를 참조하세요.

### <a name="alternate-endpoints"></a>대체 엔드포인트

재시도 정책을 따른 이후에도 연결이 불가능한 엔드포인트를 Azure Scheduler 작업에서 호출하는 경우 Scheduler는 이러한 오류를 처리할 수 있는 대체 엔드포인트로 대체합니다. 이 엔드포인트를 설정하면 Scheduler는 해당 엔드포인트를 호출하여 오류가 발생할 때 사용자 작업의 고가용성을 보장합니다.

예를 들어 이 다이어그램은 뉴욕에서 웹 서비스를 호출할 때 Scheduler가 재시도 정책을 따르는 원리를 보여줍니다. 재시도가 실패하면 Scheduler는 대체 엔드포인트를 확인합니다. 엔드포인트가 있으면 Scheduler는 대체 엔드포인트로 요청을 보내기 시작합니다. 원래 작업과 대체 작업 모두에 동일한 재시도 정책이 적용됩니다.

![재시도 정책 및 대체 엔드포인트를 사용하는 Scheduler 동작](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

대체 작업의 작업 유형은 원래 작업과 다를 수 있습니다. 예를 들어 원래 작업은 HTTP 엔드포인트를 호출하지만 대체 작업은 Storage 큐, Service Bus 큐 또는 Service Bus 토픽 작업을 사용하여 오류를 기록할 수 있습니다.

대체 엔드포인트를 설정하는 방법은 [errorAction](scheduler-concepts-terms.md#error-action)을 참조하세요.

## <a name="see-also"></a>참고 항목

* [Azure Scheduler란?](scheduler-intro.md)
* [개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [복잡한 일정 및 고급 되풀이 빌드](scheduler-advanced-complexity.md)
* [한도, 할당량, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
