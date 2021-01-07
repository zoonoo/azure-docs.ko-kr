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
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022146"
---
AMQP over Websocket 프로토콜 옵션은 HTTP/REST API와 마찬가지로 포트 TCP 443을 통해 실행 되지만 그렇지 않은 경우 일반 AMQP와 기능적으로 동일 합니다. 이 옵션은 추가 핸드셰이크 왕복 및 HTTPS 포트 공유의 절충으로 약간 더 많은 오버 헤드로 인해 초기 연결 대기 시간이 약간 더 높습니다. 이 모드를 선택 하면 TCP 포트 443이 통신에 충분 합니다. 다음 옵션을 사용 하 여 일반 AMQP 또는 AMQP Websocket 모드를 선택할 수 있습니다.

| 언어 | 옵션   |
| -------- | ----- |
| .NET     | [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) 또는 TransportType TransportType 속성을 사용 하 여 [Servicebuspwebsocket](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) [TransportType.AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [servicebus](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) 를 사용 하 여 servicebus를 사용 하 여 [com.microsoft.azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) [TransportType](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) 를 사용 하는 것이 좋습니다. |
| 노드  | [Servicebusclientoptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) 에는 `webSocket` 생성자 인수가 있습니다. |
| Python | [TransportType](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) 또는 TransportType를 사용 하 여 [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) [. amqpoverwebsocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |