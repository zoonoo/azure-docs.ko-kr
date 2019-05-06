---
title: Azure SignalR Service 질문과 대답
description: Azure SignalR Service에 대한 FAQ입니다.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: e9e41ffa335aa95b139a5d5658424c1c5915b569
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64914951"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service가 프로덕션에 사용할 준비가 되었나요?

예.
일반 공급에 대한 공지는 [Azure SignalR Service가 이제 일반 공급됩니다](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)를 참조하세요. 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)이 완전히 지원됩니다.

ASP.NET SignalR에 대한 지원은 아직 *공개 미리 보기* 상태입니다. 여기에 [코드 예제](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)가 있습니다.

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>"서버를 사용할 수 없음" 오류 메시지가 표시되면서 클라이언트 연결이 닫힙니다. 무엇을 의미하나요?

이 오류는 클라이언트가 SignalR Service에 메시지를 보내는 경우에만 발생합니다.

다른 애플리케이션 서버가 없고 SignalR Service REST API만 사용하는 경우, 이 동작은 **의도적인** 동작입니다.
서버리스 아키텍처에서 클라이언트 연결은 수신 대기(**LISTEN**) 모드이며 SignalR Service에 어떠한 메시지도 보내지 않습니다.
자세한 내용은 [REST API](./signalr-quickstart-rest-api.md)를 참조하세요.

애플리케이션 서버가 있는 경우 이 오류 메시지가 표시되면 SignalR Service 인스턴스에 연결되어 있는 애플리케이션 서버가 없다는 의미입니다.

가능한 원인:
- SignalR Service에 연결되어 있는 애플리케이션 서버가 없습니다. 애플리케이션 서버 로그에서 가능한 연결 오류를 확인하세요. 둘 이상의 애플리케이션 서버가 있는 고가용성 설정에서는 이러한 경우가 드뭅니다.
- SignalR Service 인스턴스와 연결 문제가 있습니다. 이 문제는 일시적이며 자동으로 복구됩니다.
문제가 한 시간 넘게 지속되면 [GitHub에서 문제를 제기](https://github.com/Azure/azure-signalr/issues/new)하거나 [Azure에서 지원 요청을 생성](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)하십시오.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>애플리케이션 서버가 여러 개 있으면 클라이언트 메시지가 모든 서버에 전송되나요? 아니면 그 중 하나에만 전송되나요?

이것은 클라이언트와 애플리케이션 서버 간의 일대일 매핑입니다. 한 클라이언트에서 보낸 메시지는 항상 동일한 애플리케이션 서버로 전송됩니다.

클라이언트와 애플리케이션 서버 간의 매핑은 클라이언트나 애플리케이션 서버 연결이 끊어질 때까지 유지됩니다.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>애플리케이션 서버 중 하나가 다운되는 경우, 어떤 서버인지 어떻게 알아내고 알림을 받을 수 있나요?

SignalR Service는 애플리케이션 서버의 하트비트를 모니터링합니다.
지정된 기간 동안 하트비트를 수신하지 못하면 해당 애플리케이션 서버는 오프라인으로 간주됩니다. 이 애플리케이션 서버에 매핑된 모든 클라이언트 연결이 끊어집니다.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK에서 Azure SignalR Service SDK로 전환하는 경우 사용자 지정 `IUserIdProvider`에서 예외가 발생하는 이유가 무엇인가요?

`IUserIdProvider`가 호출되는 경우 ASP.NET Core SignalR SDK와 Azure SignalR Service SDK의 매개 변수 `HubConnectionContext context`가 다릅니다.

ASP.NET Core SignalR에서 `HubConnectionContext context`는 모든 속성에 대해 유효한 값이 있는 실제 클라이언트 연결의 컨텍스트입니다.

Azure SignalR Service SDK에서 `HubConnectionContext context`는 논리적 클라이언트 연결의 컨텍스트입니다. 실제 클라이언트 연결은 SignalR Service 인스턴스에 연결되기 때문에 제한된 수의 속성만 제공됩니다.

지금은 `HubConnectionContext.GetHttpContext()`와 `HubConnectionContext.User`만 액세스에 사용할 수 있습니다.
소스 코드는 [여기](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs)에서 확인할 수 있습니다.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>SignalR Service에서 사용할 수 있는 전송을 ASP.NET Core SignalR로 서버 쪽에서 구성하도록 구성할 수 있나요? 예를 들어 WebSocket 전송을 사용하지 않도록 설정할 수 있나요?

 아니요.

Azure SignalR Service는 기본적으로 ASP.NET Core SignalR에서 지원하는 세 가지 전송을 모두 제공합니다. 이 사항은 구성할 수 없습니다. SignalR Service는 연결 및 모든 클라이언트에 대한 전송을 처리합니다.

클라이언트 쪽 전송은 [여기](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports)의 설명대로 구성하면 됩니다.
