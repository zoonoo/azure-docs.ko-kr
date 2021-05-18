---
title: Azure SignalR Service의 서비스 모드
description: Azure SignalR Service의 다양한 서비스 모드 개요에서는 모드 간 차이점과 적용 가능한 사용자 시나리오를 설명합니다.
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143250"
---
# <a name="service-mode-in-azure-signalr-service"></a>Azure SignalR Service의 서비스 모드

서비스 모드는 Azure SignalR Service의 중요한 개념입니다. 새 SignalR 리소스를 만들 때 서비스 모드를 지정하라는 메시지가 표시됩니다.

:::image type="content" source="media/concept-service-mode/create.png" alt-text="만들 때 서비스 모드 선택":::

나중에 설정 메뉴에서 변경할 수도 있습니다.

:::image type="content" source="media/concept-service-mode/update.png" alt-text="서비스 모드 업데이트":::

Azure SignalR Service는 현재 **기본**, **서버리스**, **클래식** 의 세 가지 서비스 모드를 지원합니다. SignalR 리소스는 모드에 따라 다르게 동작합니다. 이 문서에서는 모드 간 차이점과 사용자 시나리오에 따라 올바른 서비스 모드를 선택하는 방법을 알아봅니다.

## <a name="default-mode"></a>기본 모드

기본 모드는 새 SignalR 리소스를 만들 때 서비스 모드의 기본값입니다. 이 모드에서 애플리케이션은 일반적인 ASP.NET Core(또는 ASP.NET) SignalR 애플리케이션으로 작동합니다. 이 경우 허브를 호스트하는 웹 서버(이후 허브 서버라고 함)가 있고 클라이언트는 허브 서버와 이중 실시간으로 통신할 수 있습니다. 유일한 차이점은 클라이언트와 서버를 직접 연결하는 대신 클라이언트와 서버 모두 SignalR 서비스에 연결하고 서비스를 프록시로 사용하는 것입니다. 아래에는 기본 모드의 일반적인 애플리케이션 구조를 보여 주는 다이어그램이 나와 있습니다.

:::image type="content" source="media/concept-service-mode/default.png" alt-text="기본 모드의 애플리케이션 구조":::

따라서 SignalR 애플리케이션이 있고 SignalR 서비스와 통합하려는 경우 기본 모드가 대부분의 경우에 적합합니다.

### <a name="connection-routing-in-default-mode"></a>기본 모드의 연결 라우팅

기본 모드에서는 허브 서버와 SignalR 서비스(서버 연결이라고 함) 간에 WebSocket 연결이 있습니다. 이 연결은 서버와 클라이언트 간에 메시지를 전송하는 데 사용됩니다. 새 클라이언트가 연결되면 SignalR 서비스는 기존 서버 연결을 통해 클라이언트를 하나의 허브 서버(둘 이상의 서버가 있다고 가정)로 라우팅합니다. 클라이언트 연결은 수명이 지속되는 동안 동일한 허브 서버를 사용합니다. 클라이언트는 메시지를 보낼 때 항상 동일한 허브 서버로 이동합니다. 이 동작을 사용하면 허브 서버에서 개별 연결의 상태를 안전하게 유지 관리할 수 있습니다. 예를 들어 서버와 클라이언트 간에 무언가를 스트리밍하려는 경우 데이터 패킷이 다른 서버에 이동하는 경우를 고려하지 않아도 됩니다.

> [!IMPORTANT]
> 기본 모드에서는 서버가 먼저 연결되어야 클라이언트가 연결할 수 있음을 의미하기도 합니다. 네트워크 중단 또는 서버 재부팅으로 인해 모든 허브 서버의 연결이 끊어지면 서버가 연결되어 있지 않다는 오류 메시지가 클라이언트 연결에 표시됩니다. SignalR 서비스에 연결된 허브 서버가 항상 하나 이상 있도록 유지하는 것은 사용자 책임입니다. 예를 들어 허브 서버를 여러 개 구현하고 유지 관리 등을 위해 모든 허브 서버가 동시에 오프라인 상태로 전환되지 않도록 합니다.

또한 이 라우팅 모델은 허브 서버가 오프라인 상태로 전환될 경우 해당 서버를 라우팅한 연결이 삭제됨을 의미합니다. 따라서 허브 서버가 유지 관리를 위해 오프라인 상태로 전환될 경우 연결 삭제를 예상하고 적절하게 다시 연결을 처리하여 애플리케이션에 부정적인 영향을 주지 않도록 해야 합니다.

## <a name="serverless-mode"></a>서버리스 모드

이름에서 알 수 있듯이 서버리스 모드는 허브 서버를 사용할 수 없는 모드입니다. 기본 모드와 비교할 때, 이 모드의 클라이언트는 허브 서버가 연결되도록 요구하지 않습니다. 모든 연결은 “서버리스” 모드로 서비스에 연결되며, 클라이언트 ping 처리와 같은 클라이언트 연결 유지 관리의 책임이 서비스에 있습니다(기본 모드에서는 허브 서버가 처리함).

또한 이 모드에는 서버 연결이 없습니다. 서비스 SDK를 사용하여 서버 연결을 설정하려고 하면 오류가 표시됩니다. 따라서 기본 모드 섹션에 설명된 대로 연결 라우팅과 서버-클라이언트 연결 유지도 없습니다. 그러나 서버 쪽 애플리케이션을 사용하여 메시지를 클라이언트에 푸시하는 기능은 계속 지원됩니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다. 일회성 송신을 위해 또는 WebSocket을 통해 [REST APIi](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)를 사용하여 여러 메시지를 더 효율적으로 보낼 수 있도록 합니다(WebSocket 연결은 서버 연결과 다름).

> [!NOTE]
> REST API와 WebSocket 방법은 SignalR 서비스 [관리 SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)에서 둘 다 지원됩니다. .NET 이외의 언어를 사용하는 경우 [사양](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)에 따라 REST API를 수동으로 호출할 수도 있습니다.
>
> Azure Functions를 사용하는 경우 [Azure Functions의 SignalR 서비스 바인딩](../azure-functions/functions-bindings-signalr-service.md)(이후 함수 바인딩이라고 함)을 사용하여 메시지를 출력 바인딩으로 보낼 수 있습니다.

서버 애플리케이션이 클라이언트에서 메시지 및 연결 이벤트를 받을 수도 있습니다. 서비스는 웹후크를 사용하여 미리 구성된 엔드포인트(업스트림이라고 함)에 메시지와 연결 이벤트를 전달합니다. 기본 모드와 비교할 때, 연결 유지가 보장되지 않으며 HTTP 요청은 WebSocket 연결보다 효율성이 떨어질 수 있습니다.

업스트림을 구성하는 방법에 대한 자세한 내용은 [문서](./concept-upstream.md)를 참조하세요.

아래에는 서버리스 모드의 작동 방식을 보여 주는 다이어그램이 나와 있습니다.

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="서버리스 모드의 애플리케이션 구조":::

> [!NOTE]
> 기본 모드에서 허브 서버를 통과하지 않으려는 경우 REST API/관리 SDK/함수 바인딩을 사용하여 메시지를 클라이언트에 직접 보낼 수도 있습니다. 그러나 기본 모드에서는 허브 서버가 클라이언트 연결을 계속 처리하며 업스트림이 작동하지 않습니다.

## <a name="classic-mode"></a>클래식 모드

클래식은 기본 모드 및 서버리스 모드의 혼합 모드입니다. 이 모드에서 연결 모드는 클라이언트 연결이 설정될 때 연결된 허브 서버가 있는지 여부에 따라 결정됩니다. 허브 서버가 있으면 클라이언트 연결이 허브 서버로 라우팅됩니다. 그렇지 않으면 클라이언트에서 서버로 보내는 메시지를 허브 서버에 배달할 수 없는 서버리스 모드로 전환됩니다. 이 경우 약간의 불일치가 발생합니다. 예를 들어 모든 허브 서버를 짧은 시간 동안 사용할 수 없는 경우 이 시간 동안 생성된 모든 클라이언트 연결이 서버리스 모드가 되며 메시지를 허브 서버에 보낼 수 없습니다.

> [!NOTE]
> 클래식 모드는 기본 모드와 서버리스 모드 이전에 생성된 애플리케이션에서 이전 버전과의 호환성을 제공하는 데 주로 사용됩니다. 이 모드는 더 이상 사용하지 않는 것이 좋습니다. 새 애플리케이션의 경우 시나리오에 따라 기본 또는 서버리스를 선택합니다. 기존 애플리케이션의 경우 사용 사례를 검토하고 적절한 서비스 모드를 선택하는 것도 좋습니다.

또한 클래식 모드에서는 서버리스 모드의 업스트림과 같은 일부 새로운 기능을 지원하지 않습니다.

## <a name="choose-the-right-service-mode"></a>올바른 서비스 모드 선택

지금까지 서비스 모드 간 차이점과 적절하게 선택하는 방법을 알아보았습니다. 이전 섹션에서 이미 설명했듯이 클래식 모드는 권장되지 않으며 기본 모드와 서버리스 모드 중에서만 선택해야 합니다. 다음은 새 애플리케이션에 적합한 모드를 선택하고 기존 애플리케이션에서 클래식 모드 사용을 중단하는 데 도움이 되는 몇 가지 팁입니다.

* SignalR 라이브러리의 작동 방식을 이미 알고 있으며 Azure SignalR Service를 사용하기 위해 자체 호스팅 SignalR에서 이동하려는 경우 기본 모드를 선택합니다. 기본 모드는 자체 호스팅 SignalR과 동일한 방식으로 작동하며(SignalR 라이브러리에서 동일한 프로그래밍 모델을 사용할 수 있음), SignalR 서비스는 클라이언트와 허브 서버 간 프록시 역할만 합니다.

* 새 애플리케이션을 만들고 있으며 허브 서버와 서버 연결을 유지 관리하지 않으려는 경우 서버리스 모드를 선택합니다. 이 모드는 일반적으로 Azure Functions와 함께 작동하므로 서버를 유지 관리할 필요가 없습니다. 이중 통신(REST API/관리 SDK/함수 바인딩 + 업스트림)을 계속 사용할 수 있지만 프로그래밍 모델은 SignalR 라이브러리와 다릅니다.

* 클라이언트 연결을 제공하는 허브 서버와 REST API 등을 통해 클라이언트에 직접 메시지를 푸시하는 백 엔드 애플리케이션이 둘 다 있는 경우에도 기본 모드를 선택해야 합니다. 기본 모드와 서버리스 모드 간의 주요 차이점은 허브 서버가 있는지 여부와 클라이언트 연결이 라우팅되는 방식입니다. 두 모드에서 모두 REST API/관리 SDK/함수 바인딩을 사용할 수 있습니다.

* 예를 들어 동일한 SignalR 리소스에 두 개의 다른 허브가 있으며 하나는 기존 SignalR 허브로 사용되고 다른 하나는 Azure Functions와 함께 사용되고 허브 서버가 없는 경우와 같이 실제로 혼합 시나리오가 있는 경우 하나는 기본 모드이고 다른 하나는 서버리스 모드인 두 개의 SignalR 리소스로 허브를 분리하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

기본 모드와 서버리스 모드를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure SignalR Service 내부 기능](signalr-concept-internals.md)

* [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)