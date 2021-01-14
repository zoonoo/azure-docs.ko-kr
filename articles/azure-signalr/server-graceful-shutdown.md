---
title: 앱 서버를 정상적으로 중지 합니다.
description: 이 문서에서는 SignalR app 서버를 정상적으로 종료 하는 방법을 설명 합니다.
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201674"
---
# <a name="server-graceful-shutdown"></a>서버 정상 종료
Microsoft Azure SignalR Service는 서버를 정상적으로 종료 하는 두 가지 모드를 제공 합니다. 

이 기능을 사용 하는 경우의 주요 이점은 예기치 않은 연결 삭제가 발생 하지 않도록 하는 것입니다. 

대신, 비즈니스 논리와 관련 하 여 자신을 닫기 위해 클라이언트 연결을 기다리거나 데이터 손실 없이 다른 서버에 대 한 클라이언트 연결을 마이그레이션할 수도 있습니다. 

## <a name="how-it-works"></a>작동 방식

일반적으로 정상 종료 프로세스에는 네 단계가 있습니다.

1. **서버를 오프 라인으로 설정**

    이 서버에 더 이상 클라이언트 연결이 라우팅되지 않습니다.

2. **트리거 `OnShutdown` 후크**

    서버에서 소유 하 고 있는 각 허브에 대해 종료 후크를 등록할 수 있습니다.
    Azure SignalR 서비스에서 **Finack** 응답을 받은 후 등록 된 순서와 관련 하 여 호출 됩니다. 즉,이 서버가 Azure SignalR 서비스에서 오프 라인으로 설정 되었음을 의미 합니다.

    이 단계에서 메시지를 브로드캐스트 하거나 일부 정리 작업을 수행할 수 있습니다. 종료 후크가 모두 실행 된 후에는 다음 단계를 진행 합니다.

3. **모든 클라이언트 연결이 완료 될 때까지 기다렸다가** 선택한 모드에 따라 달라 집니다.

    **모드가 WaitForClientsToClose로 설정 됩니다.**

    Azure SignalR Service는 기존 클라이언트를 보유 합니다.

    종료 메시지를 모든 클라이언트에 브로드캐스트하는 등의 방법으로 디자인 한 다음 클라이언트에서 자체를 닫거나 다시 연결할 시기를 결정 하도록 해야 할 수 있습니다.

    ' 종료 ' 메시지를 브로드캐스팅 하 여 클라이언트 종료 후크가 종료 되는 샘플 사용 [에 대 한 자세한 내용은 샘플을](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) 참조 하세요.

    **모드가 MigrateClients로 설정 됩니다.**

    Azure SignalR Service는이 서버의 클라이언트 연결을 다른 유효한 서버로 다시 라우팅해야 합니다. 
    
    이 시나리오에서 및는 각각의 집합을 사용 하 여 `OnConnectedAsync` `OnDisconnectedAsync` 새 서버와 이전 서버에서 트리거됩니다 `IConnectionMigrationFeature` .이는 `HttpContext` 마이그레이션 시 클라이언트 연결이 마이그레이션 되었는지 여부를 식별 하는 데 사용할 수 있습니다. 상태 저장 시나리오에 특히 유용할 수 있습니다.

    현재 메시지가 전달 된 후에도 클라이언트 연결이 즉시 마이그레이션됩니다. 즉, 다음 메시지가 새 서버로 라우팅됩니다.

4. **서버 연결 중지**

    모든 클라이언트 연결이 닫히거나 마이그레이션된 후 또는 시간 제한 (기본적으로 30 초)이 초과 된 경우

    SignalR 서버 SDK는이 단계로 종료 프로세스를 진행 하 고 모든 서버 연결을 닫습니다.

    클라이언트 연결은 닫힘/마이그레이션되지 않은 경우에도 계속 삭제 됩니다. 예를 들어 적절 한 대상 서버/현재 클라이언트-서버 메시지는 완료 되지 않습니다.

## <a name="sample-codes"></a>샘플 코드.

다음 옵션을 추가 합니다 `AddAzureSignalR` .

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

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>`OnConnected` `OnDisconnected` 정상 종료 모드를로 설정 하는 동안 및를 구성 `MigrateClients` 합니다.

연결이 마이그레이션 되었는지 여부를 나타내는 "IConnectionMigrationFeature"가 도입 되었습니다.

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