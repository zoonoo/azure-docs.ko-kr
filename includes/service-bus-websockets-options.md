---
title: 포함 파일
description: 포함 파일
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693405"
---
WebSocket을 통한 AMQP 프로토콜 옵션은 HTTP/REST API와 마찬가지로 포트 TCP 443을 통해 실행되지만 그렇지 않은 경우 일반 AMQP와 기능적으로 동일합니다. 이 옵션은 추가 핸드셰이크 왕복으로 인해 초기 연결 대기 시간이 다소 더 길고 HTTPS 포트 공유에 대한 절충안으로 약간 더 많은 오버 헤드가 있습니다. 이 모드를 선택한 경우 TCP 포트 443이면 통신에 충분합니다. 다음 옵션을 사용하면 일반 AMQP 또는 AMQP Websocket 모드를 선택할 수 있습니다.

| 언어 | 옵션   |
| -------- | ----- |
| .NET     | [TransportType.Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype) 또는 [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)가 있는 [ServiceBusConnection.TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) 속성 |
| Java     | [com.microsoft.azure.servicebus.primitives.TransportType.AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) 또는 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype)가 있는 [com.microsoft.azure.servicebus.ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) |
| 노드  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions)에는 `webSocket` 생성자 인수가 있습니다. |
| Python | [TransportType.Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) 또는 [TransportType.AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType)이 있는 [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) |