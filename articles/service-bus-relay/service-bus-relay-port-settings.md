---
title: "Azure Service Bus WCF Relay 포트 설정 | Microsoft Docs"
description: "Service Bus WCF Relay 포트 값에 대한 세부 정보입니다."
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/23/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: f222caa272b86ff42873df09561ca4f6c6e6aa26
ms.openlocfilehash: ba499a98960b0cf2f865e2d349003cd8cb65b526


---

# <a name="azure-relay-port-settings"></a>Azure Relay 포트 설정

다음 표에서는 Service Bus WCF Relay 바인딩을 위한 포트 값에 필요한 구성을 설명합니다.

## <a name="ports"></a>포트
  
|바인딩|전송 보안|포트|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(클라이언트)|예|HTTPS| 
| |" |아니요|HTTP|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(클라이언트)|예|9351/HTTPS|  
||" |아니요|9350/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetTcpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.nettcprelaybinding)(클라이언트/서비스)|여기서는|5671/9352/HTTP(하이브리드를 사용하는 경우 9352/9353)|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(클라이언트)|예|9351/HTTPS|  
||" |아니요|9350/HTTP|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(클라이언트)|예|HTTPS|  
||" |아니요|HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(클라이언트)|예|HTTPS|  
||" |아니요|HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(서비스)|여기서는|9351/HTTP|

## <a name="next-steps"></a>다음 단계
Service Bus 메시징에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [서비스 버스 기본 사항](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus 큐, 토픽 및 구독](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)
* [서비스 버스 큐를 사용하는 방법](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)
* [Service Bus 토픽 및 구독을 사용하는 방법](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)



<!--HONumber=Nov16_HO4-->


