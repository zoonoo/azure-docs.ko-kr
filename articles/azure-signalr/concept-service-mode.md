---
title: Azure SignalR 서비스의 서비스 모드
description: Azure SignalR 서비스의 다양 한 서비스 모드에 대 한 개요에서 이들의 차이점과 적용 가능한 사용자 시나리오를 설명 합니다.
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: kenchen
ms.openlocfilehash: 60f1ab0440120cb9a96e6c05a4fc1987ead29188
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143250"
---
# <a name="service-mode-in-azure-signalr-service"></a>Azure SignalR 서비스의 서비스 모드

서비스 모드는 Azure SignalR 서비스의 중요 한 개념입니다. 새 SignalR 리소스를 만들 때 서비스 모드를 지정 하 라는 메시지가 표시 됩니다.

:::image type="content" source="media/concept-service-mode/create.png" alt-text="만들 때 서비스 모드 선택":::

설정 메뉴에서 나중에 변경할 수도 있습니다.

:::image type="content" source="media/concept-service-mode/update.png" alt-text="만들 때 서비스 모드 선택":::

Azure SignalR Service는 현재 **기본**, **서버** 리스 및 **클래식**의 세 가지 서비스 모드를 지원 합니다. SignalR 리소스는 다른 모드에서 다르게 동작 합니다. 이 문서에서는 사용자의 시나리오에 따라 올바른 서비스 모드를 선택 하는 방법 및 차이점에 대해 알아봅니다.

## <a name="default-mode"></a>기본 모드

기본 모드는 새 SignalR 리소스를 만들 때 서비스 모드의 기본값입니다. 이 모드에서 응용 프로그램은 일반적인 ASP.NET Core (또는 ASP.NET) SignalR 응용 프로그램으로 작동 합니다 .이 응용 프로그램은 허브 (허브 서버이)를 호스트 하는 웹 서버가 있고 클라이언트는 허브 서버와 이중 실시간으로 통신할 수 있습니다. 클라이언트와 서버를 직접 연결 하는 대신 클라이언트와 서버 모두 SignalR 서비스에 연결 하 고 서비스를 프록시로 사용 하는 것이 유일한 차이점입니다. 다음은 기본 모드에서 일반적인 응용 프로그램 구조를 설명 하는 다이어그램입니다.

:::image type="content" source="media/concept-service-mode/default.png" alt-text="만들 때 서비스 모드 선택":::

따라서 SignalR 응용 프로그램이 있고 SignalR service와 통합 하려는 경우 대부분의 경우 기본 모드가 적절 한 선택 이어야 합니다.

### <a name="connection-routing-in-default-mode"></a>기본 모드의 연결 라우팅

기본 모드에서는 허브 서버와 SignalR service (서버 연결) 간에 websocket 연결이 있습니다. 이러한 연결은 서버와 클라이언트 간에 메시지를 전송 하는 데 사용 됩니다. 새 클라이언트가 연결 되 면 SignalR service는 기존 서버 연결을 통해 클라이언트를 하나의 허브 서버 (둘 이상의 서버가 있다고 가정)로 라우팅합니다. 그런 다음 클라이언트 연결은 수명이 지속 되는 동안 동일한 허브 서버에 유지 됩니다. 클라이언트는 메시지를 보낼 때 항상 동일한 허브 서버로 이동 합니다. 이 동작을 사용 하면 허브 서버에서 개별 연결에 대 한 일부 상태를 안전 하 게 유지할 수 있습니다. 예를 들어 서버와 클라이언트 간에 무언가를 스트리밍하려면 데이터 패킷이 다른 서버에 전달 되는 경우를 고려해 야 합니다.

> [!IMPORTANT]
> 이는 기본 모드 클라이언트에서는 서버가 먼저 연결 되지 않은 상태에서 연결할 수 없음을 의미 하기도 합니다. 네트워크 중단 또는 서버 다시 부팅으로 인해 모든 허브 서버의 연결이 끊어지면 클라이언트 연결에 서버가 연결 되어 있지 않다는 오류 메시지가 표시 됩니다. SignalR service에 연결 된 허브 서버가 하나 이상 있는지 확인 하는 것은 사용자의 책임입니다. 예를 들어 여러 허브 서버를 보유 하 고 유지 관리와 같은 작업을 수행 하는 동시에 오프 라인으로 전환 되지 않도록 합니다.

또한이 라우팅 모델은 허브 서버가 오프 라인 상태가 되 면 해당 서버를 라우팅하는 연결을 삭제 함을 의미 합니다. 따라서 허브 서버가 유지 관리를 위해 오프 라인 상태이 고 다시 연결을 적절히 처리 하 여 응용 프로그램에 부정적인 영향을 주지 않도록 하려면 연결을 삭제 해야 합니다.

## <a name="serverless-mode"></a>서버 리스 모드

서버를 사용 하지 않는 모드에서는 이름에서 알 수 있듯이는 허브 서버를 사용할 수 없다는 것입니다. 기본 모드와 비교할 때이 모드에서는 클라이언트에 허브 서버가 연결 되지 않아도 됩니다. 모든 연결은 "서버 리스" 모드에서 서비스에 연결 되 고 서비스는 클라이언트 ping 처리와 같은 클라이언트 연결을 유지 관리 합니다 (기본 모드에서는 허브 서버에서 처리 됨).

또한이 모드에는 서버 연결이 없습니다. service SDK를 사용 하 여 서버 연결을 설정 하려고 하면 오류가 발생 합니다. 따라서 기본 모드 섹션에 설명 된 것 처럼 연결 라우팅 및 서버 간 연결도 없습니다. 그러나 클라이언트에 메시지를 푸시할 서버 쪽 응용 프로그램을 계속 사용할 수 있습니다. 이 작업은 두 가지 방법으로 수행할 수 있습니다. 한 번의 송신에는 [REST api](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) 를 사용 하 고 여러 메시지를 더 효율적으로 보낼 수 있도록 websocket 연결을 통해 수행할 수 있습니다 (이 websocket 연결은 서버 연결과 다름).

> [!NOTE]
> REST API 및 websocket 방식은 SignalR service [MANAGEMENT SDK](https://github.com/Azure/azure-signalr/blob/dev/docs/management-sdk-guide.md)에서 지원 됩니다. .NET 이외의 언어를 사용 하는 경우이 [사양](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)에 따라 REST api를 수동으로 호출할 수도 있습니다.
>
> Azure Functions를 사용 하는 경우 Azure Functions (이 함수 바인딩) [에 대해 SignalR 서비스 바인딩을](../azure-functions/functions-bindings-signalr-service.md) 사용 하 여 메시지를 출력 바인딩으로 보낼 수 있습니다.

또한 서버 응용 프로그램에서 클라이언트의 메시지 및 연결 이벤트를 받을 수 있습니다. 서비스는 웹 후크를 사용 하 여 미리 구성 된 끝점 (업스트림 이라고 함)에 메시지 및 연결 이벤트를 전달 합니다. 기본 모드와 비교 하 여 연결 유지를 보장 하지 않으며 HTTP 요청은 websocket 연결 보다 효율성이 떨어질 수 있습니다.

업스트림을 구성 하는 방법에 대 한 자세한 내용은이 [문서](./concept-upstream.md)를 참조 하세요.

서버를 사용 하지 않는 모드의 작동 방식을 보여 주는 다이어그램은 다음과 같습니다.

:::image type="content" source="media/concept-service-mode/serverless.png" alt-text="만들 때 서비스 모드 선택":::

> [!NOTE]
> 기본 모드에서는 허브 서버를 사용 하지 않을 경우 REST API/관리 SDK/함수 바인딩을 사용 하 여 메시지를 클라이언트에 직접 보낼 수도 있습니다. 하지만 기본 모드에서는 클라이언트 연결이 허브 서버에서 계속 처리 되며 업스트림은이 모드에서 작동 하지 않습니다.

## <a name="classic-mode"></a>클래식 모드

클래식은 기본 모드 및 서버를 사용 하지 않는 모드의 혼합 모드입니다. 이 모드에서 연결 모드는 클라이언트 연결이 설정 될 때 연결 된 허브 서버가 있는지 여부에 따라 결정 됩니다. 허브 서버가 있으면 클라이언트 연결이 허브 서버로 라우팅됩니다. 그렇지 않으면 클라이언트에서 서버 메시지를 허브 서버에 배달할 수 없는 서버를 사용 하지 않는 모드로 전환 됩니다. 이렇게 하면 약간의 불일치를 일으킬 수 있습니다. 예를 들어, 모든 허브 서버를 짧은 시간 동안 사용할 수 없는 경우에는 해당 시간 동안 만들어진 모든 클라이언트 연결이 서버 리스 모드로 되어 메시지를 허브 서버로 보낼 수 없습니다.

> [!NOTE]
> 클래식 모드는 기본 모드 및 서버를 사용 하지 않는 모드 이전에 만든 응용 프로그램에 대해 이전 버전과의 호환성을 위해 주로 사용 됩니다. 이 모드는 더 이상 사용 하지 않는 것이 좋습니다. 새 응용 프로그램의 경우 시나리오에 따라 기본 또는 서버 리스 서버를 선택 하세요. 기존 응용 프로그램의 경우 사용 사례를 검토 하 고 적절 한 서비스 모드를 선택 하는 것도 좋습니다.

또한 클래식 모드에서는 서버 리스 모드의 업스트림 같은 일부 새로운 기능을 지원 하지 않습니다.

## <a name="choose-the-right-service-mode"></a>올바른 서비스 모드 선택

이제 서비스 모드 간의 차이점을 이해 하 고이를 선택 하는 방법을 알고 있어야 합니다. 이전 섹션에서 설명한 것 처럼 클래식 모드는 권장 되지 않으며 기본 서버와 서버 리스 서버 중 하나만 선택 해야 합니다. 새 응용 프로그램에 적합 한 항목을 선택 하 고 기존 응용 프로그램에 대해 클래식 모드를 사용 중지 하는 데 도움이 되는 몇 가지 팁이 있습니다.

* SignalR library가 작동 하는 방식에 대해 잘 알고 있고, Azure SignalR Service를 사용 하기 위해 자체 호스팅 SignalR에서 이동 하려면 기본 모드를 선택 합니다. 기본 모드는 자체 호스트 된 SignalR와 동일 하 게 작동 하며, SignalR library에서 동일한 프로그래밍 모델을 사용할 수 있으며, SignalR service는 클라이언트와 허브 서버 간의 프록시로만 작동 합니다.

* 새 응용 프로그램을 만들고 허브 서버 및 서버 연결을 유지 하지 않으려는 경우 서버를 사용 하지 않는 모드를 선택 합니다. 이 모드는 일반적으로 Azure Functions와 함께 작동 하므로 서버를 유지 관리할 필요가 없습니다. 이중 통신 (REST API/관리 SDK/함수 바인딩 + 업스트림)을 계속 사용할 수 있지만 프로그래밍 모델은 SignalR 라이브러리와 다릅니다.

* 클라이언트 연결을 제공 하는 허브 서버와 백 엔드 응용 프로그램을 사용 하 여 클라이언트에 직접 메시지를 푸시하는 경우 (예: REST API)에도 기본 모드를 선택 해야 합니다. 기본 모드 및 서버를 사용 하지 않는 모드의 주요 차이점은 허브 서버가 있는지 여부와 클라이언트 연결이 라우팅되는 방식입니다. 두 모드 모두에서 REST API/관리 SDK/함수 바인딩을 사용할 수 있습니다.

* 예를 들어, 동일한 SignalR 리소스에서 두 개의 다른 허브를 사용 하는 경우, 하나는 기존 SignalR 허브로 사용 되 고 다른 하나는 허브 서버를 사용 하지 않는 경우 하나는 두 개의 SignalR 리소스로 분리 하는 것입니다. 하나는 기본 모드이 고 Azure Functions 다른 하나는 서버를 사용 하지 않는 모드입니다.

## <a name="next-steps"></a>다음 단계

기본 모드 및 서버를 사용 하지 않는 모드를 사용 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

* [Azure SignalR Service 내부 기능](signalr-concept-internals.md)

* [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](signalr-concept-serverless-development-config.md)