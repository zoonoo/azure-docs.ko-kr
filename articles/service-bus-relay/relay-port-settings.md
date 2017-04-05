---
title: "Azure Relay 포트 설정 | Microsoft Docs"
description: "Azure Relay 포트 값에 대한 세부 정보입니다."
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
ms.date: 03/24/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 783e797ad318fe926ba9e72e2eea027beb4a5994
ms.lasthandoff: 03/27/2017


---

# <a name="azure-relay-port-settings"></a>Azure Relay 포트 설정

다음 테이블에서는 Azure Relay를 위한 포트 값에 필요한 구성을 설명합니다.

## <a name="hybrid-connections"></a>하이브리드 연결
하이브리드 연결은 기본 전송 메커니즘으로 **HTTPS**만 사용하는 WebSockets을 사용합니다. 

## <a name="wcf-relays"></a>WCF 릴레이
  
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
Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure 릴레이란?](relay-what-is-it.md)
* [릴레이 FAQ](relay-faq.md)
