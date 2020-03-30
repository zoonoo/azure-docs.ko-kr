---
title: Azure Functions 지리적 재해 복구 및 고가용성
description: 중복성을 위해 지리적 영역을 사용하고 Azure Functions에서 장애 조치하는 방법
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 481a716bd6ced5c304da41c70fdcfc687b76661d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080223"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure 함수 지리적 재해 복구

전체 Azure 지역 또는 데이터 센터가 가동 중지 시간을 경험하는 경우 다른 리전에서 계산을 계속 처리하는 것이 중요합니다.  이 문서에서는 재해 복구를 허용하는 함수를 배포하는 데 사용할 수 있는 몇 가지 전략에 대해 설명합니다.

## <a name="basic-concepts"></a>기본 개념

Azure 함수는 특정 지역에서 실행됩니다.  가용성을 높이려면 동일한 기능을 여러 지역에 배포할 수 있습니다.  여러 지역에서 *활성/활성* 패턴 또는 *능동/수동* 패턴으로 함수를 실행할 수 있습니다.  

* 활성/활성. 두 지역 모두 활성 상태이고 수신 이벤트(중복 또는 회전)입니다. Azure 정문과 함께 HTTPS 함수에 활성/활성을 권장합니다.
* 액티브/패시브. 한 리전이 활성 상태이고 이벤트를 수신하는 반면 보조 영역은 유휴 상태입니다.  장애 조치(failover)가 필요한 경우 보조 영역이 활성화되고 처리가 수행됩니다.  서비스 버스 및 이벤트 허브와 같은 HTTP가 아닌 함수에 권장됩니다.

다중 [지역 배포에](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) 대한 자세한 내용은 여러 지역에서 앱을 실행하는 방법을 읽어보십시오.

## <a name="activeactive-for-https-functions"></a>HTTPS 기능에 대한 활성/활성

함수의 활성/활성 배포를 얻으려면 두 지역 간의 이벤트를 조정할 수 있는 일부 구성 요소가 필요합니다.  HTTPS 함수의 경우 이 조정은 [Azure 정문 문을](../frontdoor/front-door-overview.md)사용하여 수행됩니다.  Azure 정문은 여러 지역 함수 간에 HTTPS 요청을 라우팅하고 라운드 로빈할 수 있습니다.  또한 각 끝점의 상태를 주기적으로 검사합니다.  지역 함수가 상태 확인에 응답하지 않으면 Azure 정문은 회전에서 벗어나 트래픽을 정상 함수로만 전달합니다.  

![Azure 정문 및 기능을 위한 아키텍처](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>HTTPS가 아닌 함수에 대한 활성/활성

HTTPS가 아닌 함수에 대해 활성/활성 배포를 수행할 수 있습니다.  그러나 두 영역이 서로 상호 작용하거나 조정하는 방법을 고려해야 합니다.  동일한 함수 앱을 두 리전에 배포한 경우 각 리전은 동일한 Service Bus 큐에서 트리거되는 경우 해당 큐의 큐를 줄이면 경쟁 소비자역할을 합니다.  즉, 각 메시지는 인스턴스 중 하나에서만 처리되지만 단일 서비스 버스에 여전히 단일 실패 지점이 있음을 의미합니다.  두 개의 Service Bus 큐(기본 리전, 보조 리전에 하나씩)를 배포하고 두 함수 앱이 해당 지역 큐를 가리키는 경우 이제 큐 메시지가 두 리전 간에 배포되는 방식에 문제가 발생합니다.  이는 종종 각 게시자가 *두* 지역에 메시지를 게시하려고 시도하고 각 메시지가 두 활성 함수 앱에서 처리된다는 것을 의미합니다.  이렇게 하면 활성/활성 패턴이 생성되지만 계산 중복 및 데이터 통합 시기 또는 방법에 대한 다른 문제가 발생합니다.  이러한 이유로 HTTPS가 아닌 트리거는 활성/수동 패턴을 사용하는 것이 좋습니다.

## <a name="activepassive-for-non-https-functions"></a>HTTPS가 아닌 함수에 대한 능동/수동

Active/passive는 단일 함수만 각 메시지를 처리할 수 있는 방법을 제공하지만 재해발생 시 보조 지역으로 장애 조치하는 메커니즘을 제공합니다.  Azure 함수는 [Azure Service 버스 지리적 복구](../service-bus-messaging/service-bus-geo-dr.md) 및 Azure 이벤트 허브 지리적 복구와 함께 [작동합니다.](../event-hubs/event-hubs-geo-dr.md)

Azure Event Hubs 트리거를 예로 들어 사용 하 여 활성/수동 패턴에는 다음이 포함 됩니다.

* 기본 및 보조 지역에 배포된 Azure 이벤트 허브입니다.
* 지오 재해를 사용하여 기본 및 보조 이벤트 허브를 페어링할 수 있습니다.  또한 연결 정보를 변경하지 않고 이벤트 허브에 연결하고 기본 에서 보조로 전환하는 데 사용할 수 있는 "별칭"도 생성됩니다.
* 기본 및 보조 지역에 배포된 기능 앱입니다.
* 함수 앱은 해당 이벤트 *direct* 허브에 대한 직접(별칭이 아닌) 연결 문자열에서 트리거됩니다. 
* 이벤트 허브에 게시자는 별칭 연결 문자열에 게시해야 합니다. 

![능동 수동 예제 아키텍처](media/functions-geo-dr/active-passive.png)

장애 조치 전에 공유 별칭으로 보내는 게시자는 기본 이벤트 허브로 라우팅됩니다.  기본 함수 앱은 기본 이벤트 허브에서만 수신 대기하고 있습니다.  보조 함수 앱은 수동 및 유휴 상태가 됩니다.  장애 조치(failover)가 시작되는 즉시 공유 별칭으로 보내는 게시자는 이제 보조 이벤트 허브로 라우팅됩니다.  이제 보조 함수 앱이 활성화되어 자동으로 트리거됩니다.  보조 지역으로의 유효 장애 조치(failover)는 이벤트 허브에서 전적으로 구동될 수 있으며, 함수는 각 이벤트 허브가 활성화된 경우에만 활성화됩니다.

[서비스 버스](../service-bus-messaging/service-bus-geo-dr.md) 및 [이벤트 허브를](../event-hubs/event-hubs-geo-dr.md)통해 장애 조치(failover)에 대한 정보 및 고려 사항에 대해 자세히 읽어보십시오.

## <a name="next-steps"></a>다음 단계

* [Azure 정문 만들기](../frontdoor/quickstart-create-front-door.md)
* [이벤트 허브 장애 조치 고려 사항](../event-hubs/event-hubs-geo-dr.md#considerations)
