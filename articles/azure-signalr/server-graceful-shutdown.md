---
title: 앱 서버를 정상적으로 중지합니다.
description: 이 문서에서는 SignalR 앱 서버를 정상적으로 종료하는 방법을 설명합니다.
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98201674"
---
# <a name="server-graceful-shutdown"></a>서버 정상 종료
Microsoft Azure SignalR Service는 서버의 정상 종료를 위한 두 가지 모드를 제공합니다. 

이 기능의 주요 이점은 고객이 예기치 않게 연결이 끊기는 경험을 하지 않도록 방지하는 것입니다. 

대신 비즈니스 논리와 관련하여 클라이언트 연결이 닫히기를 기다리거나 데이터 손실 없이 클라이언트 연결을 다른 서버로 마이그레이션할 수도 있습니다. 

## <a name="how-it-works"></a>작동 방법

일반적으로 정상 종료 프로세스에는 네 단계가 있습니다.

1. **서버를 오프라인으로 설정**

    서버에 더 이상 클라이언트 연결이 라우팅되지 않습니다.

2. `OnShutdown` 후크 **트리거**

    서버에 소유하고 있는 각 허브에 대해 종료 후크를 등록할 수 있습니다.
    종료 후크는 Azure SignalR Service에서 **FINACK** 응답을 받은 직후 등록된 순서와 비교하여 호출됩니다. 즉, 서버가 Azure SignalR Service에서 오프라인으로 설정되었음을 의미합니다.

    이 단계에서 메시지를 브로드캐스트하거나 일부 정리 작업을 수행할 수 있습니다. 종료 후크가 모두 실행된 후에는 다음 단계를 진행합니다.

3. **모든 클라이언트 연결 완료 시까지 대기** 아래 모드 중 어느 것을 선택하느냐에 따라 달라집니다.

    **모드를 WaitForClientsToClose로 설정**

    Azure SignalR Service가 기존 클라이언트를 보류합니다.

    종료 메시지를 모든 클라이언트에 브로드캐스트하는 등의 방법으로 디자인한 다음, 클라이언트에서 자체적으로 닫거나 다시 연결할 시기를 결정하도록 해야 할 수 있습니다.

    샘플 사용은 [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample)을 참조하세요. 종료 후크에서 클라이언트 닫기를 트리거하는 ‘종료’ 메시지를 브로드캐스트합니다.

    **모드를 MigrateClients로 설정**

    Azure SignalR Service가 서버의 클라이언트 연결을 다른 유효한 서버로 다시 라우팅하려고 합니다. 
    
    이 시나리오에서는 `OnConnectedAsync`와 `OnDisconnectedAsync`가 `HttpContext`의 `IConnectionMigrationFeature` 집합을 사용하여 각각 새 서버와 기존 서버에서 트리거됩니다. 이 집합은 클라이언트 연결이 내부로 마이그레이션되었는지 아니면 외부로 마이그레이션되었는지의 여부를 파악하는 데 사용될 수 있으며, 특히 상태 저장 시나리오에 유용할 수 있습니다.

    현재 메시지가 전달된 후에 클라이언트 연결이 즉시 마이그레이션됩니다. 즉, 다음 메시지는 새 서버로 라우팅됩니다.

4. **서버 연결 중지**

    모든 클라이언트 연결이 닫히거나 마이그레이션된 후 또는 시간 제한(기본값: 30 초)이 초과된 경우입니다.

    SignalR Server SDK는 이 단계까지 종료 프로세스를 진행하고 모든 서버 연결을 닫습니다.

    클라이언트 연결은 닫힘/마이그레이션에 실패한 않은 경우에도 계속 끊깁니다. 예를 들어 적절한 대상 서버/현재 클라이언트-서버 메시지가 완료되지 않았습니다.

## <a name="sample-codes"></a>샘플 코드

`AddAzureSignalR`일 경우 다음 옵션을 추가합니다.

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>정상 종료 모드를 `MigrateClients`로 설정하는 동안 `OnConnected`와 `OnDisconnected`를 구성합니다.

연결의 내부 또는 마이그레이션 여부를 나타내는 “IConnectionMigrationFeature”의 도입이 완료되었습니다.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```