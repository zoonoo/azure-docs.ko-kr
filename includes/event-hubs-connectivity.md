---
title: 파일 포함
description: 파일 포함
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98689615"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>방화벽에서 열어야 하는 포트는 어느 것인가요? 
Azure Event Hubs에서 다음 프로토콜을 사용 하 여 이벤트를 보내고 받을 수 있습니다.

- 고급 메시지 큐 프로토콜 1.0 (AMQP)
- TLS (HTTPS)를 사용한 하이퍼텍스트 전송 프로토콜 1.1
- Apache Kafka

이러한 프로토콜을 사용하여 Azure Event Hubs와 통신하기 위해 열어야 하는 아웃바운드 포트는 다음 표를 참조하세요. 

| 프로토콜 | 포트 | 세부 정보 | 
| -------- | ----- | ------- | 
| AMQP | 5671 및 5672 | [AMQP 프로토콜 가이드](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md)를 참조하세요. | 
| HTTPS | 443 | 이 포트는 HTTP/REST API 및 AMQP over Websocket에 사용 됩니다. |
| Kafka | 9093 | [Kafka 애플리케이션에서 Event Hubs 사용](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)을 참조하세요.

클라이언트 Sdk에서 수행 하는 여러 관리 작업 및 Azure Active Directory (사용 되는 경우)에서 토큰을 획득 하는 것이 HTTPS를 통해 실행 되기 때문에 포트 5671을 통해 AMQP를 사용 하는 경우에도 HTTPS 포트가 아웃 바운드 통신에 필요 합니다. 

공식 Azure Sdk는 일반적으로 Event Hubs에서 이벤트를 보내고 받기 위한 AMQP 프로토콜을 사용 합니다. AMQP over Websocket 프로토콜 옵션은 HTTP API와 마찬가지로 포트 TCP 443을 통해 실행 되지만 그렇지 않은 경우에는 일반 AMQP와 기능적으로 동일 합니다. 이 옵션은 추가 핸드셰이크 왕복으로 인해 초기 연결 대기 시간이 더 높고 HTTPS 포트를 공유 하기 위한 절충으로 약간 더 많은 오버 헤드가 발생 합니다. 이 모드를 선택 하면 TCP 포트 443이 통신에 충분 합니다. 다음 옵션을 사용 하 여 일반 AMQP 또는 AMQP Websocket 모드를 선택할 수 있습니다.

| 언어 | 옵션   |
| -------- | ----- |
| .NET     | EventHubsTransportType [EventHubConnectionOptions TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) 속성- [AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) 또는 [EventHubsTransportType](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [Eventhubs EventProcessorClientBuilder. transporttype](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) 와 [AmqpTransportType. amqp](/java/api/com.azure.core.amqp.amqptransporttype) 또는 [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| 노드  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) 에는 `webSocketOptions` 속성이 있습니다. |
| Python | [TransportType](/python/api/azure-eventhub/azure.eventhub.transporttype) 또는 TransportType를 사용 하 여 [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) [. amqpoverwebsocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>허용 해야 하는 IP 주소는 무엇 인가요?
Azure를 사용 하는 경우 회사 방화벽이 나 프록시에서 특정 IP 주소 범위 또는 Url을 사용 하 여 사용 하거나 사용 하려는 모든 Azure 서비스에 액세스 해야 하는 경우가 있습니다. Event Hubs에서 사용 하는 IP 주소에서 트래픽이 허용 되는지 확인 합니다. Azure Event Hubs에서 사용 하는 IP 주소에 대해서는 [AZURE IP 범위 및 서비스 태그-공용 클라우드](https://www.microsoft.com/download/details.aspx?id=56519)를 참조 하세요.

또한 네임 스페이스에 대 한 IP 주소가 허용 되는지 확인 합니다. 연결에 사용할 올바른 IP 주소를 찾으려면 다음 단계를 수행 합니다.

1. 명령 프롬프트에서 다음 명령을 실행합니다. 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. `Non-authoritative answer`에서 반환된 IP 주소를 적어 둡니다. 

네임 스페이스에 **영역 중복성** 을 사용 하는 경우 몇 가지 추가 단계를 수행 해야 합니다. 

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
    > 명령에서 반환 된 IP 주소는 `nslookup` 고정 ip 주소가 아닙니다. 그러나 기본 배포가 삭제 되거나 다른 클러스터로 이동 될 때까지 일정 하 게 유지 됩니다.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>네임 스페이스에서 이벤트를 보내거나 받는 클라이언트 Ip는 무엇 인가요?
먼저 네임 스페이스에서 [IP 필터링](../articles/event-hubs/event-hubs-ip-filtering.md) 을 사용 하도록 설정 합니다. 

그런 다음 [진단 로그 사용](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)의 지침에 따라 [가상 네트워크 연결 이벤트 Event Hubs](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) 에 대 한 진단 로그를 사용 하도록 설정 합니다. 연결이 거부 된 IP 주소가 표시 됩니다.

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
> 가상 네트워크 로그는 네임 스페이스에서 **특정 ip 주소** (ip 필터 규칙)의 액세스를 허용 하는 경우에만 생성 됩니다. 이러한 기능을 사용 하 여 네임 스페이스에 대 한 액세스를 제한 하지 않고, Event Hubs 네임 스페이스에 연결 하는 클라이언트의 IP 주소를 추적 하기 위해 가상 네트워크 로그를 가져오려는 경우 다음 해결 방법을 사용할 수 있습니다. IP 필터링을 사용 하도록 설정 하 고 주소 지정 가능한 총 IPv4 범위 (1.0.0.0/1-255.0.0.0/1)를 추가 합니다. Event Hubs은 IPv6 주소 범위를 지원 하지 않습니다. 

> [!NOTE]
> 현재 개별 메시지 또는 이벤트의 원본 IP를 확인할 수 없습니다. 