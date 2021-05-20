---
title: 포함 파일
description: 포함 파일
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2970a7271761082a358b50c069da82ed785df8b0
ms.sourcegitcommit: aba63ab15a1a10f6456c16cd382952df4fd7c3ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107990477"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>방화벽에서 열어야 하는 포트는 어느 것인가요? 
Azure Event Hubs에서 다음 프로토콜을 사용하여 이벤트를 보내고 받을 수 있습니다.

- AMQP(고급 메시지 큐 프로토콜) 1.0
- TLS를 사용하는 HTTPS(Hypertext Transfer Protocol) 1.1
- Apache Kafka

이러한 프로토콜을 사용하여 Azure Event Hubs와 통신하기 위해 열어야 하는 아웃바운드 포트는 다음 표를 참조하세요. 

| 프로토콜 | 포트 | 세부 정보 | 
| -------- | ----- | ------- | 
| AMQP | 5671 및 5672 | [AMQP 프로토콜 가이드](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조하세요. | 
| HTTPS | 443 | 이 포트는 HTTP/REST API 및 AMQP-over-WebSockets에 사용됩니다. |
| Kafka | 9093 | [Kafka 애플리케이션에서 Event Hubs 사용](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)을 참조하세요.

클라이언트 SDK에서 수행하는 여러 관리 작업 및 Azure Active Directory(사용되는 경우)에서의 토큰 획득은 HTTPS를 통해 실행되므로 AMQP가 5671 포트를 통해 사용되는 경우에도 HTTPS 포트가 아웃바운드 통신에 필요합니다. 

공식 Azure SDK는 일반적으로 Event Hubs에서 이벤트를 보내고 받는 데 AMQP 프로토콜을 사용합니다. AMQP-over-WebSockets 프로토콜 옵션은 HTTP API와 마찬가지로 443 TCP 포트를 통해 실행되지만, 그 밖의 기능은 일반 AMQP와 동일합니다. 이 옵션은 추가 핸드셰이크 왕복으로 인해 초기 연결 대기 시간이 더 길고, HTTPS 포트 공유에 대한 절충으로 약간 더 많은 오버헤드가 있습니다. 이 모드를 선택하는 경우 443 TCP 포트가 통신에 충분합니다. 다음 옵션을 사용하면 일반 AMQP 또는 AMQP WebSockets 모드를 선택할 수 있습니다.

| 언어 | 옵션   |
| -------- | ----- |
| .NET     | [EventHubsTransportType.AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) 또는 [EventHubsTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype)가 있는 [EventHubConnectionOptions.TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) |
| Java     | [AmqpTransportType.AMQP](/java/api/com.azure.core.amqp.amqptransporttype) 또는 [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype)가 있는 [com.microsoft.azure.eventhubs.EventProcessorClientBuilder.transporttype](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/main/java/com/azure/messaging/eventhubs/EventProcessorClientBuilder.java) |
| 노드  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions)에는 `webSocketOptions` 속성이 있습니다. |
| Python | [TransportType.Amqp](/python/api/azure-eventhub/azure.eventhub.transporttype) 또는 [TransportType.AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype)이 있는 [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>허용해야 하는 IP 주소는 무엇인가요?
Azure를 사용하는 경우 회사 방화벽 또는 프록시의 특정 IP 주소 범위 또는 URL에서 사용 중이거나 사용하려는 모든 Azure 서비스에 액세스하도록 허용해야 하는 경우가 있습니다. Event Hubs에서 사용하는 IP 주소에서 트래픽이 허용되는지 확인합니다. Azure Event Hubs에서 사용하는 IP 주소는 [Azure IP 범위 및 서비스 태그 - 퍼블릭 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조하세요.

또한 네임스페이스의 IP 주소가 허용되는지 확인합니다. 연결을 허용하는 적합한 IP 주소를 찾으려면 다음 단계를 수행합니다.

1. 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer`에서 반환된 IP 주소를 적어 둡니다. 

네임스페이스에 대해 **영역 중복성** 을 사용하는 경우 몇 가지 추가 단계를 수행해야 합니다. 

1. 먼저 네임스페이스에서 nslookup을 실행합니다.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **신뢰할 수 없는 응답** 섹션에서 다음 형식 중 하나로 표시되는 이름을 적어 둡니다. 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. 접미사 s1, s2 및 s3를 포함하는 각 이름에 대해 nslookup을 실행하여 세 개의 가용성 영역에서 실행되는 세 인스턴스의 IP 주소를 모두 가져옵니다. 

    > [!NOTE]
    > `nslookup` 명령에서 반환된 IP 주소는 고정 IP 주소가 아닙니다. 그러나 이 주소는 기본 배포가 삭제되거나 다른 클러스터로 이동될 때까지 일정하게 유지됩니다.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>내 네임스페이스에 이벤트를 보내거나 받는 클라이언트 IP는 무엇인가요?
먼저 네임스페이스에서 [IP 필터링](../articles/event-hubs/event-hubs-ip-filtering.md)을 사용하도록 설정합니다. 

그런 다음, [진단 로그 사용](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)의 지침에 따라 진단 로그를 [Event Hubs 가상 네트워크 연결 이벤트](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema)에 사용하도록 설정합니다. 연결이 거부된 IP 주소가 표시됩니다.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> 가상 네트워크 로그는 네임스페이스에서 **특정 IP 주소**(IP 필터 규칙)의 액세스를 허용하는 경우에만 생성됩니다. 이러한 기능을 사용하여 네임스페이스에 대한 액세스를 제한하지 않고 Event Hubs 네임스페이스에 연결하는 클라이언트의 IP 주소를 추적하기 위해 가상 네트워크 로그를 계속 가져오려는 경우 IP 필터링을 사용하도록 설정하고 총 주소 지정 가능한 IPv4 범위(1.0.0.0/1~255.0.0.0/1)를 추가하여 해결합니다. Event Hubs는 IPv6 주소 범위를 지원하지 않습니다. 

> [!NOTE]
> 현재 개별 메시지 또는 이벤트의 원본 IP는 확인할 수 없습니다. 
