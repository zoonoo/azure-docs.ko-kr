---
title: 이벤트 경로
titleSuffix: Azure Digital Twins
description: Azure Digital Twins 내에서 이벤트를 라우팅하는 방법 및 다른 Azure 서비스에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b7d62777f73a92768b1ede3fb9b7e0cb97951823
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537518"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Azure Digital Twins 내부 및 외부에서 이벤트 라우팅

Azure Digital 쌍는 **이벤트 경로** 를 사용 하 여 서비스 외부의 소비자에 게 데이터를 보냅니다. 

미리 보기 중에는 Azure Digital Twins 데이터를 전송 하는 두 가지 주요 사례가 있습니다.
* Azure Digital Twins 그래프의 한 쌍에서 다른 쌍으로 데이터 전송 예를 들어 하나의 디지털 쌍에서 속성이 변경 되 면 다른 디지털 쌍을 적절 하 게 통보 하 고 업데이트할 수 있습니다.
* 추가 저장 또는 처리를 위해 다운스트림 데이터 서비스로 데이터 전송 ( *데이터 송신*이 라고도 함) 예를 들어
  - 병원은 대량 분석을 위해 handwashing 관련 이벤트의 시계열 데이터를 기록 하기 위해 Azure Digital Twins 이벤트 데이터를 [Time Series Insights (TWINS)](../time-series-insights/time-series-insights-update-overview.md)로 보낼 수 있습니다.
  - 이미 [Azure Maps](../azure-maps/about-azure-maps.md) 를 사용 하 고 있는 비즈니스에서는 Azure Digital twins를 사용 하 여 솔루션을 향상 시킬 수 있습니다. Azure digital 쌍를 설정 하 고, [쌍으로 된](concepts-twins-graph.md) azure map 엔터티를 쌍으로 된 azure 디지털 쌍으로 가져오거나, Azure Maps 및 Azure Digital 쌍 데이터를 함께 활용 하는 강력한 쿼리를 실행 한 후 azure map을 신속 하 게 사용 하도록 설정할 수 있습니다.

이벤트 경로는 이러한 두 시나리오에 모두 사용 됩니다.

## <a name="about-event-routes"></a>이벤트 경로 정보

이벤트 경로를 사용 하면 Azure digital 쌍의 디지털 쌍에서 구독의 사용자 지정 된 끝점으로 이벤트 데이터를 보낼 수 있습니다. 3 개의 Azure 서비스는 현재 끝점, 즉 [Event Hub](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)및 [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)지원 됩니다. 이러한 각 Azure 서비스는 다른 서비스에 연결 될 수 있으며, 매개자 역할을 하며, 필요한 처리를 위해 TSI 또는 Azure Maps와 같은 최종 대상으로 데이터를 전송 합니다.

다음 다이어그램은 Azure Digital Twins 측면에서 더 큰 IoT 솔루션을 통해 이벤트 데이터의 흐름을 보여 줍니다.

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="여러 다운스트림 서비스에 끝점을 통해 데이터를 라우팅하는 Azure Digital Twins" border="false":::

이벤트 경로에 대 한 일반적인 다운스트림 대상은 TSI, Azure Maps, storage 및 analytics 솔루션과 같은 리소스입니다.

### <a name="event-routes-for-internal-digital-twin-events"></a>내부 디지털 쌍 이벤트에 대 한 이벤트 경로

현재 미리 보기 릴리스에서는 쌍으로 된 쌍 내에서 이벤트를 처리 하 고 디지털 쌍에서 디지털 쌍으로 데이터를 전송 하는 데에도 이벤트 경로가 사용 됩니다. 이 작업은 Event Grid를 통해 [Azure Functions](../azure-functions/functions-overview.md)와 같은 계산 리소스에 이벤트 경로를 연결 하 여 수행 됩니다. 그런 다음 이러한 함수는 쌍이 이벤트를 수신 하 고 응답 하는 방법을 정의 합니다. 

계산 리소스가 이벤트 경로를 통해 받은 이벤트에 따라 쌍 그래프를 수정 하려는 경우 미리 수정할 쌍을 파악 하는 것이 좋습니다. 

또는 이벤트 메시지에는 메시지를 보낸 원본 쌍의 ID도 포함 되므로 계산 리소스는 쿼리 또는 트래버스 관계를 사용 하 여 원하는 작업에 대 한 대상 쌍을 찾을 수 있습니다. 

또한 계산 리소스는 보안 및 액세스 권한을 독립적으로 설정 해야 합니다.

디지털 쌍 이벤트를 처리 하도록 Azure 함수를 설정 하는 프로세스를 안내 하려면 [*방법: 데이터 처리를 위한 azure 함수 설정*](how-to-create-azure-function.md)을 참조 하세요.

## <a name="create-an-endpoint"></a>엔드포인트 만들기

개발자는 이벤트 경로를 정의 하기 위해 먼저 끝점을 정의 해야 합니다. **끝점** 은 경로 연결을 지 원하는 Azure Digital twins 외부의 대상입니다. 현재 미리 보기 릴리스에서 지원 되는 대상은 다음과 같습니다.
* Event Grid 사용자 지정 항목
* 이벤트 허브
* Service Bus

끝점은 [Azure Digital Twins CLI](how-to-use-cli.md)에서 지원 되는 제어 평면 api를 사용 하 여 설정 하거나 Azure Portal을 통해 설정 됩니다. 끝점 정의는 다음을 제공 합니다.
* 끝점의 이름입니다.
* 끝점 유형 (Event Grid, Event Hub 또는 Service Bus)
* 인증할 기본 연결 문자열 및 보조 연결 문자열 
* 끝점의 토픽 경로 (예: *your-topic.westus2.eventgrid.azure.net* )

제어 평면에서 사용할 수 있는 끝점 Api는 다음과 같습니다.
* 엔드포인트 만들기
* 끝점 목록 가져오기
* 이름으로 끝점 가져오기
* 이름으로 끝점 삭제

## <a name="create-an-event-route"></a>이벤트 경로 만들기
 
다음 [.net (c #) SDK](how-to-use-apis-sdks.md) 호출을 사용 하 여 클라이언트 응용 프로그램에서 이벤트 경로를 만듭니다. 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-name>"));
```

* 는 `endpoint-name` 이벤트 허브, Event Grid 또는 Service Bus 등의 끝점을 식별 합니다. 이러한 끝점은 구독에서 생성 되 고이 등록을 호출 하기 전에 제어 평면 Api를 사용 하 여 Azure Digital Twins에 연결 되어야 합니다.

또한에 전달 되는 이벤트 경로 개체는 `EventRoutes.Add` [ **필터** 매개 변수](./how-to-manage-routes.md#filter-events)를 사용 하 여이 경로를 따르는 이벤트 유형을 제한 하는 데 사용할 수 있습니다.

[Azure Digital Twins CLI](how-to-use-cli.md)를 사용 하 여 경로를 만들 수도 있습니다.

### <a name="types-of-event-messages"></a>이벤트 메시지 유형

IoT Hub의 다양 한 이벤트 유형 및 Azure Digital Twins는 아래에 설명 된 대로 서로 다른 유형의 알림 메시지를 생성 합니다.

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>다음 단계

이벤트 경로를 설정 하 고 관리 하는 방법을 참조 하세요.
* [*방법: 끝점과 경로 관리*](how-to-manage-routes.md)

또는 Azure Functions를 사용 하 여 Azure Digital Twins 내에서 이벤트를 라우팅하는 방법을 참조 하세요.
* [*방법: 데이터 처리를 위한 Azure 함수 설정*](how-to-create-azure-function.md)