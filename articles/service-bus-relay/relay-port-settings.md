---
title: Azure Relay 포트 설정 | Microsoft Docs
description: 이 문서에는 Azure 릴레이에 필요한 포트 값에 필요한 구성을 설명하는 테이블이 포함되어 있습니다.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 55bb6b620c7ba9ee13068213cbc867b165acc185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514887"
---
# <a name="azure-relay-port-settings"></a>Azure Relay 포트 설정

다음 표에서는 Azure Relay의 포트 값에 필요한 구성을 설명합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

하이브리드 연결은 SSL로 포트 443에서 기본 전송 메커니즘으로 **HTTPS**만 사용하는 WebSockets을 사용합니다. 

## <a name="wcf-relays"></a>WCF 릴레이
  
|바인딩|전송 보안|포트|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(클라이언트)|yes|HTTPS| 
|" |예|HTTP|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(클라이언트)|yes|9351/HTTPS|  
|" |예|9350/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetTcpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.nettcprelaybinding)(클라이언트/서비스)|여기서는|5671/9352/HTTP(하이브리드를 사용하는 경우 9352/9353)|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(클라이언트)|yes|9351/HTTPS|  
|" |예|9350/HTTP|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(클라이언트)|yes|HTTPS|  
|" |예|HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(클라이언트)|yes|HTTPS|  
|" |예|HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(서비스)|여기서는|9351/HTTP|

## <a name="next-steps"></a>다음 단계
Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure 릴레이란?](relay-what-is-it.md)
* [릴레이 FAQ](relay-faq.md)