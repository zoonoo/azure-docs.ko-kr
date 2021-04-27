---
title: Azure Functions 지역 재해 복구 및 고가용성
description: 중복성을 위해 지리적 지역을 사용하고 Azure Functions에서 장애 조치(failover)를 수행하는 방법입니다.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86506284"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions 지리적 재해 복구

Azure 지역 또는 데이터 센터에서 가동 중지 시간이 발생하는 경우 컴퓨터가 다른 지역에서 계속 처리해야 합니다.  이 문서에서는 재해 복구를 허용하는 기능을 배포하는 데 사용할 수 있는 몇 가지 전략을 설명합니다.

## <a name="basic-concepts"></a>기본 개념

Azure Functions는 특정한 지역에서 실행됩니다.  더 높은 가용성을 얻기 위해 동일한 기능을 여러 지역에 배포할 수 있습니다.  여러 지역에 있는 경우 *활성/활성* 패턴 또는 *활성/수동* 패턴에서 함수를 실행할 수 있습니다.  

* 활성/활성. 두 지역 모두 활성 상태이며 이벤트를 수신합니다(중복 또는 회전). Azure Front Door에 조합하여 HTTPS 함수에 활성/활성인 것이 좋습니다.
* 활성/수동 한 지역은 활성 상태이고 이벤트를 수신하며 보조 복제본은 유휴 상태입니다.  장애 조치(failover)가 필요한 경우 보조 지역은 활성화되고 처리를 수행합니다.  Service Bus 및 Event Hubs와 같은 HTTP가 아닌 함수에 권장됩니다.

다중 지역 배포에 대한 자세한 내용은 [여러 지역에서 앱을 실행](/azure/architecture/reference-architectures/app-service-web-app/multi-region)하는 방법을 참조하세요.

## <a name="activeactive-for-https-functions"></a>HTTPS 함수에 대해 활성/활성

함수에 대한 활성/활성 배포를 수행하려면 두 지역 간에 이벤트를 조정할 수 있는 일부 구성 요소가 필요합니다.  HTTPS 함수의 경우 [Azure Front Door](../frontdoor/front-door-overview.md)를 사용하여 이러한 조정을 수행합니다.  Azure Front Door는 여러 지역 함수 간에 HTTPS 요청을 라우팅하고 라운드 로빈할 수 있습니다.  또한 각 엔드포인트의 상태를 정기적으로 확인합니다.  지역 함수에서 상태 검사에 대한 응답을 중지하는 경우 Azure Front Door는 회전을 수행하지 않으며 정상 함수로 트래픽을 전달합니다.  

![Azure Front Door 및 함수에 대한 아키텍처](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>HTTPS가 아닌 함수에 대해 활성/활성

HTTPS가 아닌 함수에 대해 활성/활성 배포를 계속 수행할 수 있습니다.  그러나 두 지역이 상호 작용하는 방법을 고려해야 합니다.  동일한 Service Bus 큐에서 각각 트리거되는 동일한 함수 앱을 두 지역에 배포하는 경우 함수 앱은 큐를 제거하는 경쟁 소비자 역할을 수행합니다.  즉, 각 메시지가 인스턴스 중 하나에서만 처리되지만 단일 Service Bus에 단일 실패 지점이 아직 있음을 의미하기도 합니다.  두 개의 Service Bus 큐(주 지역에 하나, 보조 지역에 하나)를 배포하여 두 개의 함수 앱이 지역 큐를 가리키는 경우 이제 두 지역 간에 큐 메시지를 분산하는 방법을 찾아야 합니다.  이는 일반적으로 각 게시자가 *두* 지역에 메시지를 게시하려고 시도하고, 각 메시지가 활성 함수 앱에서 처리되는 것을 의미합니다.  이는 활성/활성 패턴을 만들지만 계산의 중복 및 데이터 통합 시기 또는 방법에 대한 다른 문제를 만듭니다.  이러한 이유로 HTTPS가 아닌 트리거에서는 활성/수동 패턴을 사용하는 것이 좋습니다.

## <a name="activepassive-for-non-https-functions"></a>HTTPS가 아닌 함수를 위한 활성/수동

활성/수동은 단일 함수에서 각 메시지를 처리하는 방법을 제공하지만 재해가 발생한 경우 보조 지역으로 장애 조치(failover)하는 메커니즘을 제공합니다.  Azure Functions는 [Azure Service Bus 지역 복구](../service-bus-messaging/service-bus-geo-dr.md) 및 [Azure Event Hubs 지역 복구](../event-hubs/event-hubs-geo-dr.md)와 함께 작동합니다.

예를 들어 Azure Event Hubs 트리거를 사용하는 경우 활성/수동 패턴에는 다음이 포함됩니다.

* 기본 및 보조 지역에 모두 배포되는 Azure Event Hub입니다.
* 지역 재해를 사용하도록 설정하여 기본 및 보조 이벤트 허브를 페어링합니다.  또한 이벤트 허브에 연결하고 연결 정보를 변경하지 않고 기본에서 보조로 전환하는 데 사용할 수 있는 "별칭"을 만듭니다.
* 함수 앱은 기본 및 보조 지역 모두에 배포됩니다.
* 함수 앱은 해당 이벤트 허브에 대한 *직접*(별칭 아님) 연결 문자열에서 트리거합니다. 
* 이벤트 허브에 대한 게시자는 별칭 연결 문자열에 게시해야 합니다. 

![활성-수동 아키텍처 예](media/functions-geo-dr/active-passive.png)

장애 조치(failover) 전에 공유 별칭으로 보내는 게시자는 기본 이벤트 허브로 라우팅됩니다.  기본 함수 앱은 기본 이벤트 허브만을 대상으로 수신 대기합니다.  보조 함수 앱은 수동 및 유휴 상태가 됩니다.  장애 조치(failover)가 시작되는 즉시 공유 별칭으로 보내는 게시자는 이제 보조 이벤트 허브로 라우팅됩니다.  이제 보조 함수 앱이 활성 상태가 되고 자동으로 트리거를 시작합니다.  보조 지역에 대한 효과적인 장애 조치(failover)는 이벤트 허브에서 전적으로 구동될 수 있으며, 함수는 해당 이벤트 허브가 활성 상태인 경우에만 활성화됩니다.

자세한 내용은 [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) 및 [이벤트 허브](../event-hubs/event-hubs-geo-dr.md)의 장애 조치(failover)에 대한 정보 및 고려 사항을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Front Door 만들기](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs 장애 조치(failover) 고려 사항](../event-hubs/event-hubs-geo-dr.md#considerations)
