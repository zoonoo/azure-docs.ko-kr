---
title: Azure SignalR Service 질문과 대답
description: 문제 해결 및 일반적인 사용 시나리오를 비롯하여 Azure SignalR Service에 대한 질문과 대답을 확인하세요.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 910de9efbd132fb98a0c4bd596867800f65f5ad5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150965"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service가 프로덕션에 사용할 준비가 되었나요?

예, [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) 및 [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr)에 대한 지원은 모두 일반 공급됩니다.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>애플리케이션 서버가 여러 개 있으면 클라이언트 메시지가 모든 서버에 전송되나요? 아니면 그 중 하나에만 전송되나요?

클라이언트와 애플리케이션 서버 간의 일대일 매핑이 있습니다. 한 클라이언트에서 보낸 메시지는 항상 동일한 애플리케이션 서버로 전송됩니다.

매핑은 클라이언트나 애플리케이션 서버 연결이 끊어질 때까지 유지됩니다.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>애플리케이션 서버 중 하나가 다운되는 경우, 어떤 서버인지 어떻게 알아내고 알림을 받을 수 있나요?

Azure SignalR Service는 애플리케이션 서버의 하트비트를 모니터링합니다.
지정된 기간 동안 하트비트를 수신하지 못하면 해당 애플리케이션 서버는 오프라인으로 간주됩니다. 이 애플리케이션 서버에 매핑된 모든 클라이언트 연결이 끊어집니다.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK에서 Azure SignalR Service SDK로 전환하는 경우 사용자 지정 `IUserIdProvider`에서 예외가 발생하는 이유가 무엇인가요?

`IUserIdProvider`가 호출되는 경우 ASP.NET Core SignalR SDK와 Azure SignalR Service SDK의 매개 변수 `HubConnectionContext context`가 다릅니다.

ASP.NET Core SignalR에서 `HubConnectionContext context`는 모든 속성에 대해 유효한 값이 있는 실제 클라이언트 연결의 컨텍스트입니다.

Azure SignalR Service SDK에서 `HubConnectionContext context`는 논리적 클라이언트 연결의 컨텍스트입니다. 실제 클라이언트는 Azure SignalR Service 인스턴스에 연결되기 때문에 제한된 수의 속성만 제공됩니다.

지금은 `HubConnectionContext.GetHttpContext()`와 `HubConnectionContext.User`만 액세스에 사용할 수 있습니다.
[소스 코드를 확인](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)할 수 있습니다.

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Azure SignalR Service에서 사용할 수 있는 전송을 ASP.NET Core SignalR로 서버 쪽에서 구성할 수 있나요? 예를 들어 WebSocket 전송을 사용하지 않도록 설정할 수 있나요?

아니요.

Azure SignalR Service는 기본적으로 ASP.NET Core SignalR에서 지원하는 세 가지 전송을 모두 제공합니다. 이 사항은 구성할 수 없습니다. Azure SignalR Service는 연결 및 모든 클라이언트에 대한 전송을 처리합니다.

[ASP.NET Core SignalR 구성](/aspnet/core/signalr/configuration#configure-allowed-transports-1)에 설명된 대로 클라이언트 쪽 전송을 구성할 수 있습니다.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure Portal에 표시된 메시지 수 또는 연결 수와 같은 메트릭의 의미는 무엇인가요? 어떤 종류의 집계 유형을 선택해야 하나요?

[Azure SignalR Service의 메시지 및 연결](signalr-concept-messages-and-connections.md)에서 이러한 메트릭 계산에 대한 세부 정보를 찾을 수 있습니다.

Azure SignalR Service 리소스의 개요 창에서 적절한 집계 유형을 이미 선택했습니다. 메트릭 창으로 이동하면 집계 유형을 참조로 [Azure SignalR Service의 메시지 및 연결](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)로 사용할 수 있습니다.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>`Default`, `Serverless` 및 `Classic` 서비스 모드의 의미는 무엇인가요? 어떻게 선택할 수 있나요?

새 애플리케이션의 경우에는 기본 및 서버리스 모드만 사용해야 합니다. 주요 차이점은 서비스에 대한 서버 연결을 설정하는 애플리케이션 서버가 있는지 여부(즉, `AddAzureSignalR()`을 사용하여 서비스에 연결)를 사용하는 것입니다. 예인 경우 기본 모드를 사용하고 그렇지 않으면 서버리스 모드를 사용합니다.

클래식 모드는 기존 애플리케이션에 대해 이전 버전과의 호환성을 위해 설계되었으므로 새 애플리케이션에 사용하면 안 됩니다.

서비스 모드에 대한 자세한 내용은 [이 문서](concept-service-mode.md)를 참조하세요.

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>서버리스 모드로 클라이언트에서 메시지를 보낼 수 있나요?

SignalR 인스턴스에서 업스트림을 구성하는 경우 클라이언트에서 메시지를 보낼 수 있습니다. 업스트림은 SignalR 서비스에서 메시지 및 연결 이벤트를 받을 수 있는 엔드포인트 세트입니다. 업스트림이 구성되지 않은 경우 클라이언트의 메시지가 무시됩니다.

업스트림에 대한 자세한 내용은 [이 문서](concept-upstream.md)를 참조하세요.

업스트림은 현재 공개 미리 보기로 제공됩니다.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>ASP.NET SignalR과 함께 Azure SignalR Service를 사용하는 경우 기능 차이점이 있나요?

Azure SignalR Service를 사용하는 경우 ASP.NET SignalR의 일부 API 및 기능이 지원되지 않습니다.
- 클라이언트와 허브 간에 임의 상태를 전달하는 기능(종종 `HubState`라고 함)은 지원되지 않습니다.
- `PersistentConnection` 클래스는 지원되지 않습니다.
- *Forever Frame 전송*은 지원되지 않습니다.
- 클라이언트가 오프라인인 경우 Azure SignalR Service가 클라이언트로 전송된 메시지를 더 이상 재생하지 않습니다.
- Azure SignalR Service를 사용하는 경우 한 클라이언트 연결에 대한 트래픽은 항상 연결 기간 동안 하나의 앱 서버 인스턴스로 라우팅됩니다(*고정*이라고도 함).

ASP.NET SignalR에 대한 지원은 호환성에 중점을 두므로 ASP.NET Core SignalR의 모든 새로운 기능이 지원되지 않습니다. 예를 들어 *MessagePack* 및 *Streaming*은 ASP.NET Core SignalR 애플리케이션에만 사용할 수 있습니다.

서로 다른 서비스 모드(`Classic`, `Default` 및 `Serverless`)에 대해 Azure SignalR Service를 구성할 수 있습니다. ASP.NET에는 `Serverless` 모드가 지원되지 않습니다. 데이터 평면 REST API도 지원되지 않습니다.

## <a name="where-does-my-data-reside"></a>데이터가 어디에 상주하나요?

Azure SignalR Service는 데이터 프로세서 서비스로 작동합니다. 고객 콘텐츠를 저장하지 않으며 데이터 상주는 디자인에 의해 포함됩니다. 진단에 대해 Azure Storage와 같은 다른 Azure 서비스와 Azure SignalR Service를 함께 사용하는 경우 Azure 지역에서 데이터 상주를 유지하는 방법에 대한 지침은 [이 백서](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)를 참조하세요.