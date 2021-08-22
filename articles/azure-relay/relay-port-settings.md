---
title: Azure Relay 포트 설정 | Microsoft Docs
description: 이 문서에는 Azure Relay에 대한 포트 값에 필요한 구성을 설명하는 표가 포함되어 있습니다.
ms.topic: article
ms.date: 06/23/2021
ms.openlocfilehash: b293d58d2e555b7e0d01ea1a9e421f78cf23105c
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668685"
---
# <a name="azure-relay-port-settings"></a>Azure Relay 포트 설정

다음 표에서는 Azure Relay의 포트 값에 필요한 구성을 설명합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

하이브리드 연결은 TLS로 포트 443에서 기본 전송 메커니즘으로 **HTTPS** 만 사용하는 WebSockets을 사용합니다. 

## <a name="wcf-relays"></a>WCF 릴레이
  
|바인딩|전송 보안|포트|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(클라이언트)|Yes|HTTPS| 
|" |예|HTTP|  
|[BasicHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.basichttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(클라이언트)|예|9351/HTTPS|  
|" |예|9350/HTTP|  
|[NetEventRelayBinding 클래스](/dotnet/api/microsoft.servicebus.neteventrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[NetTcpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.nettcprelaybinding)(클라이언트/서비스)|여기서는|5671/9352/HTTP(하이브리드를 사용하는 경우 9352/9353)|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(클라이언트)|예|9351/HTTPS|  
|" |예|9350/HTTP|  
|[NetOnewayRelayBinding 클래스](/dotnet/api/microsoft.servicebus.netonewayrelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(클라이언트)|Yes|HTTPS|  
|" |예|HTTP|  
|[WebHttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.webhttprelaybinding)(서비스)|여기서는|9351/HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(클라이언트)|Yes|HTTPS|  
|" |예|HTTP|  
|[WS2007HttpRelayBinding 클래스](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding)(서비스)|여기서는|9351/HTTP|

## <a name="next-steps"></a>다음 단계
Azure Relay에 대한 자세한 내용은 다음 링크를 방문하세요.
* [Azure Relay란?](relay-what-is-it.md)
* [릴레이 FAQ](relay-faq.yml)