---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304423"
---
WebSocket을 통한 AMQP 프로토콜 옵션은 HTTP/REST API와 마찬가지로 포트 TCP 443을 통해 실행되지만 그렇지 않은 경우 일반 AMQP와 기능적으로 동일합니다. 이 옵션은 추가 핸드셰이크 왕복으로 인해 초기 연결 대기 시간이 더 길고, HTTPS 포트 공유에 대한 절충으로 약간 더 많은 오버헤드가 있습니다. 이 모드를 선택한 경우 TCP 포트 443이면 통신에 충분합니다. 다음 옵션을 사용하면 AMQP WebSockets 모드를 선택할 수 있습니다. 

| 언어 | 옵션   |
| -------- | ----- |
| .NET(Azure.Messaging.ServiceBus)    | [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions)를 매개 변수로 사용하는 기여자를 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor)를 만듭니다. [ServiceBusClientOptions.TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype)을 [ServiceBusTransportType.AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype)로 설정합니다. |
| .NET(Microsoft.Azure.ServiceBus)    | 클라이언트 개체를 만들 때 [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection) 또는 [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder)를 매개 변수로 사용하는 기여자를 사용합니다. <p>`transportType`을 매개 변수로 사용하는 구성의 경우 매개 변수를 [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)로 설정합니다.</p> <p>`ServiceBusConnection`을 매개 변수로 사용하는 기여자의 경우 [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype)을 [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)로 설정합니다.</p> <p>`ServiceBusConnectionStringBuilder`를 사용하는 경우 `transportType`을 지정하는 옵션을 제공하는 기여자를 사용합니다.</p> |
| Java(com.azure.messaging.servicebus)     | 클라이언트를 만들 때 [ServiceBusClientBuilder.transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype)을 [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype)로 설정합니다. |
| Java(com.microsoft.azure.servicebus)    | 클라이언트를 만들 때 [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)의 `transportType`을 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype)로 설정합니다. |
| JavaScript  | Service Bus 클라이언트 개체를 만들 때 [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions)의 `webSocketOptions` 속성을 사용합니다. |
| Python | Service Bus 클라이언트를 만들 때 [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient)을 [TransportType.AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype)으로 설정합니다. |

