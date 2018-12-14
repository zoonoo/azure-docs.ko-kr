---
title: Azure IoT Hub 메시지 라우팅 이해하기 | Microsoft Docs
description: 개발자 가이드 - 메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 보내는 방법입니다. 원격 분석 데이터 및 비원격 분석 데이터를 전송하는 방법에 대한 정보가 포함됩니다.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 8424fa721d87467de70b4365578d196b3145b4b0
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50024276"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>메시지 라우팅을 사용하여 디바이스-클라우드 메시지를 다른 엔드포인트에 보내기

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

메시지 라우팅을 사용하면 확장 가능하고 신뢰할 수 있는 자동화된 방식으로 디바이스에서 클라우드 서비스로 메시지를 보낼 수 있습니다. 메시지 라우팅은 다음 용도로 사용할 수 있습니다. 

* **장치 원격 분석 메시지는 물론 이벤트** 즉, 장치 수명 주기 이벤트 및 장치 쌍 변경 이벤트를 기본 제공 엔드포인트 및 사용자 지정 엔드포인트로 보냅니다. [엔드포인트 라우팅](#routing-endpoints)에 대해 알아보세요.

* 다양한 쿼리를 적용하여 **다양한 엔드포인트로 데이터를 라우팅하기 전에 데이터를 필터링**합니다. 메시지 라우팅을 사용하면 메시지 속성 및 메시지 본문뿐만 아니라 디바이스 쌍 태그 및 디바이스 쌍 속성에 대해서도 쿼리할 수 있습니다. [메시지 라우팅에 쿼리](iot-hub-devguide-routing-query-syntax.md)를 사용하는 방법에 대해 자세히 알아보세요.

IoT Hub는 메시지 라우팅을 작동하기 위해 이러한 서비스 엔드포인트에 대한 쓰기 액세스가 필요합니다. Azure Portal을 통해 엔드포인트를 구성하는 경우 필요한 권한이 추가됩니다. 예상된 처리량을 지원하도록 서비스를 구성해야 합니다. IoT 솔루션을 처음 구성하는 경우 추가 엔드포인트를 모니터링하고 실제 부하에 맞게 조정해야 할 수 있습니다.

IoT Hub는 프로토콜 전체에서의 상호 운용성을 위해 모든 디바이스-클라우드 메시징에 대해 [일반적인 형식](iot-hub-devguide-messages-construct.md)을 정의합니다. 메시지가 동일한 엔드포인트를 가리키는 여러 경로와 일치하는 경우 IoT Hub는 메시지를 해당 엔드포인트에 한 번만 전달합니다. 따라서 Service Bus 큐 또는 항목에 대해 중복 제거를 구성할 필요가 없습니다. 분할된 큐에서 파티션 선호도는 메시지 순서를 보장합니다. [메시지 라우팅을 구성](tutorial-routing.md)하는 방법을 알아보려면 이 자습서를 사용하세요.

## <a name="routing-endpoints"></a>라우팅 엔드포인트

IoT Hub에는 Event Hubs와 호환되는 기본 제공 엔드포인트(**메시지/이벤트**)가 있습니다. 구독의 다른 서비스를 IoT Hub에 연결하여 메시지를 라우팅할 [사용자 지정 엔드포인트](iot-hub-devguide-endpoints.md#custom-endpoints)를 만들 수 있습니다. IoT Hub는 현재 다음과 같은 서비스를 사용자 지정 엔드포인트로 지원합니다.

### <a name="built-in-endpoint"></a>기본 제공 엔드포인트

표준 [Event Hubs 통합 및 SDK](iot-hub-devguide-messages-read-builtin.md)를 사용하여 기본 제공 엔드포인트(**메시지/이벤트**)에서 디바이스-클라우드 메시지를 수신할 수 있습니다. 일단 경로가 생성되면 경로가 해당 엔드포인트에 대해 생성되어 있지 않는 한, 데이터가 기본 제공 엔드포인트로 흐르는 것을 멈춥니다.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub는 [Apache Avro](http://avro.apache.org/) 형식으로 Azure Blob Storage에 데이터 쓰기만 지원합니다. IoT Hub는 메시지를 일괄 처리하고, 일괄 처리가 특정 크기에 도달하거나 일정 시간이 경과할 때마다 Blob에 데이터를 씁니다.

IoT Hub는 기본적으로 다음 파일 명명 규칙을 따릅니다.

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

원하는 파일 명명 규칙을 사용할 수 있지만, 나열된 토큰은 모두 사용해야 합니다. 쓸 데이터가 없으면 IoT Hub가 빈 Blob을 작성합니다.

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus 큐 및 Service Bus 토픽

IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 엔드포인트가 **연결할 수 없음**으로 표시됩니다.

### <a name="event-hubs"></a>Event Hubs

기본 제공 Event Hubs 호환 엔드포인트 외에, Event Hubs 유형의 사용자 지정 엔드포인트로 데이터를 라우팅할 수도 있습니다. 

라우팅 및 사용자 지정 엔드포인트를 사용할 때 메시지에 일치하는 규칙이 없으면 기본 제공 엔드포인트에만 메시지가 전달됩니다. 메시지를 기본 제공 엔드포인트와 사용자 지정 엔드포인트에 전달하려면 이벤트 엔드포인트에 메시지를 보내는 경로를 추가합니다.

## <a name="reading-data-that-has-been-routed"></a>라우팅된 데이터 읽기

이 [자습서](tutorial-routing.md)에 따라 경로를 구성할 수 있습니다.

엔드포인트에서 메시지를 읽는 방법을 알아보려면 다음 자습서를 사용하세요.

* [기본 제공 엔드포인트](quickstart-send-telemetry-node.md)에서 읽기

* [Blob 저장소](../storage/blobs/storage-blob-event-quickstart.md)에서 읽기

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)에서 읽기

* [Service Bus 큐](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)에서 읽기

* [Service Bus 토픽](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)에서 읽기

## <a name="fallback-route"></a>대체(fallback) 경로

대체(fallback) 경로는 기존 경로에서 쿼리 조건을 충족하지 않는 모든 메시지를 [Event Hubs](/azure/event-hubs/)와 호환되는 기본 제공 Event Hubs(**messages/events**)로 보냅니다. 메시지 라우팅이 설정되어 있으면 대체 경로 기능을 사용하도록 설정할 수 있습니다. 일단 경로가 생성되면 경로가 해당 엔드포인트에 대해 생성되어 있지 않는 한 데이터가 기본 제공 엔드포인트로 흐르는 것을 멈춥니다. 기본 제공 엔드포인트에 대한 경로가 없고 대체(fallback) 경로를 사용할 수 있는 경우 경로의 모든 쿼리 조건을 충족하지 않는 메시지만 기본 제공 엔드포인트로 전송됩니다. 또한 기존 경로가 모두 삭제된 경우에는 대체(fallback) 경로가 기본 제공 엔드포인트에서 모든 데이터를 수신하도록 설정해야 합니다. 

대체(fallback) 경로는 Azure Portal->Message Routing 블레이드에서 사용하도록/사용하지 않도록 설정할 수 있습니다. [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties)에 Azure Resource Manager를 사용하여 대체(fallback) 경로에 대해 사용자 지정 엔드포인트를 사용할 수도 있습니다.

## <a name="non-telemetry-events"></a>비-원격 분석 이벤트

디바이스 원격 분석 외에, 메시지 라우팅을 통해 디바이스 쌍 변경 이벤트와 디바이스 수명 주기 이벤트를 전송할 수 있습니다. 예를 들어, **디바이스 쌍 변경 이벤트**로 설정된 데이터 원본으로 경로가 생성되면, IoT Hub는 디바이스 쌍의 변경 사항을 포함하는 엔드포인트로 메시지를 전송합니다. 마찬가지로 **디바이스 수명 주기 이벤트**로 설정된 데이터 원본으로 경로가 만들어지면 IoT Hub는 디바이스가 삭제되었거나 생성되었는지 여부를 나타내는 메시지를 보냅니다. 

[IoT Hub는 Azure Event Grid와 통합](iot-hub-event-grid.md)되어 장치 이벤트를 게시하여 이러한 이벤트를 기반으로 워크플로의 실시간 통합 및 자동화를 지원합니다. [메시지 라우팅과 Event Grid 간의 주요 차이점](iot-hub-event-grid-routing-comparison.md)을 확인하고 내 시나리오에 무엇이 가장 적합한지 알아보세요.

## <a name="testing-routes"></a>경로 테스트

새 경로를 만들거나 기존 경로를 편집하는 경우에는 샘플 메시지로 경로 쿼리를 테스트해야 합니다. 개별 경로를 테스트하거나 모든 경로를 한 번에 테스트할 수 있으며 테스트 중에는 엔드포인트에 메시지가 라우팅되지 않습니다. Azure Portal, Azure Resource Manager, Azure PowerShell 및 Azure CLI를 테스트에 사용할 수 있습니다. 결과는 샘플 메시지가 쿼리와 일치하는지, 메시지가 쿼리와 일치하지 않는지 또는 샘플 메시지나 쿼리 구문이 올바르지 않아서 테스트를 실행할 수 없는지 여부를 식별하는 데 도움이 됩니다. 자세히 알아보려면 [경로 테스트](/rest/api/iothub/iothubresource/testroute) 및 [모든 경로 테스트](/rest/api/iothub/iothubresource/testallroutes)를 참조하세요.

## <a name="latency"></a>대기 시간

기본 제공 엔드포인트를 사용하여 디바이스-클라우드 간 원격 분석 메시지를 라우팅할 경우 첫 번째 경로를 만든 후 종단 간 대기 시간이 약간 증가합니다.

대부분의 경우 평균적인 대기 시간 증가는 500ms 미만입니다. **라우팅: 메시지/이벤트에 대한 메시지 대기 시간** 또는 **d2c.endpoints.latency.builtIn.events** IoT Hub 메트릭을 사용하여 대기 시간을 모니터링할 수 있습니다. 첫 번째 경로 이후에 다른 경로를 만들거나 삭제해도 종단 간 대기 시간에는 영향을 주지 않습니다.

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

IoT Hub는 허브 및 메시지 전송 상태에 대한 개요를 제공하는 여러 가지 라우팅 및 엔드포인트 관련 메트릭을 제공합니다. 여러 메트릭의 정보를 결합하여 문제의 근본 원인을 식별할 수 있습니다. 예를 들어 **라우팅: 삭제된 원격 분석 메시지** 또는 **d2c.telemetry.egress.dropped** 메트릭을 사용하면 모든 경로의 쿼리와 일치하지 않고 대체(fallback) 경로가 사용되지 않는 경우 손실된 메시지의 수를 식별할 수 있습니다. [IoT Hub 메트릭](iot-hub-metrics.md)에는 기본적으로 IoT Hub에 대해 활성화된 모든 메트릭이 나열됩니다.

Azure Monitor [진단 설정](../iot-hub/iot-hub-monitor-resource-health.md)에서 **경로** 진단 로그를 사용하면, IoT Hub가 인식하는 라우팅 쿼리 및 엔드포인트 상태를 평가하는 동안 발생하는 오류를 추적할 수 있습니다(예 : 엔드포인트가 종료됨). 이러한 진단 로그는 사용자 지정 처리를 위해 Log Analytics, Event Hubs 또는 Azure Storage로 보낼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 메시지 경로를 만드는 방법에 대한 자세한 내용은 [경로를 사용하여 IoT Hub 디바이스-클라우드 메시지 처리](tutorial-routing.md)를 참조하세요.

* [장치-클라우드 메시지를 보내는 방법](quickstart-send-telemetry-node.md)

* 디바이스-클라우드 메시지를 보내는 데 사용할 수 있는 SDK에 대한 자세한 내용은 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
