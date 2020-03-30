---
title: 여러 인스턴스로 확장 - Azure SignalR 서비스
description: 많은 확장 시나리오에서 고객은 대규모 배포를 만들기 위해 여러 인스턴스를 프로비전하고 함께 사용하도록 구성해야 하는 경우가 많습니다. 예를 들어 샤딩에는 여러 인스턴스가 지원해야 합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: 43d703312cbc1fc067a2d51d5623ed028ba01405
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158167"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>여러 인스턴스로 SignalR 서비스를 확장하는 방법은 무엇입니까?
최신 SignalR 서비스 SDK는 SignalR 서비스 인스턴스에 대해 여러 엔드포인트를 지원합니다. 이 기능을 사용하여 동시 연결을 확장하거나 지역 간 메시징에 사용할 수 있습니다.

## <a name="for-aspnet-core"></a>ASP.NET 코어용

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가하는 방법은 무엇입니까?

키 또는 `Azure:SignalR:ConnectionString:` `Azure:SignalR:ConnectionString` SignalR 서비스 연결 문자열에 대한 구성입니다.

키로 시작하는 `Azure:SignalR:ConnectionString:`경우 형식이어야 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`합니다. `Name` `EndpointType` `ServiceEndpoint`

다음 `dotnet` 명령을 사용하여 여러 인스턴스 연결 문자열을 추가할 수 있습니다.

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가하는 방법은 무엇입니까?

Azure `ServicEndpoint` SignalR 서비스 끝점의 속성을 설명 하기 위해 클래스가 도입 됩니다.
다음을 통해 Azure SignalR 서비스 SDK를 사용할 때 여러 인스턴스 끝점을 구성할 수 있습니다.
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>엔드 포인트 라우터를 사용자 정의하는 방법?

기본적으로 SDK는 [DefaultEndpointRouter를](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) 사용하여 끝점을 선택합니다.

#### <a name="default-behavior"></a>기본 동작 
1. 클라이언트 요청 라우팅

    때 `/negotiate` 응용 프로그램 서버와 클라이언트. 기본적으로 SDK는 사용 가능한 서비스 끝점 집합에서 하나의 끝점을 **임의로 선택합니다.**

2. 서버 메시지 라우팅

    *특정 **connection***로 메시지를 보내고 대상 연결이 현재 서버로 라우팅되면 메시지가 연결된 끝점으로 직접 이동합니다. 그렇지 않으면 메시지가 모든 Azure SignalR 끝점으로 브로드캐스트됩니다.

#### <a name="customize-routing-algorithm"></a>라우팅 알고리즘 사용자 지정
메시지가 이동해야 하는 끝점을 식별하는 특별한 지식이 있는 경우 고유한 라우터를 만들 수 있습니다.

사용자 지정 라우터는 `east-` 항상 다음 이름으로 `east`시작하는 그룹이 끝점으로 이동하는 경우 예제로 정의됩니다.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

아래의 또 다른 예로, 기본 협상 동작을 재정의하여 끝점을 선택하는 것은 앱 서버가 있는 위치에 따라 다릅니다.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

다음을 사용하여 라우터를 DI 컨테이너에 등록하는 것을 잊지 마십시오.

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>For ASP.NET

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가하는 방법은 무엇입니까?

키 또는 `Azure:SignalR:ConnectionString:` `Azure:SignalR:ConnectionString` SignalR 서비스 연결 문자열에 대한 구성입니다.

키로 시작하는 `Azure:SignalR:ConnectionString:`경우 형식이어야 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`합니다. `Name` `EndpointType` `ServiceEndpoint`

여러 인스턴스 연결 문자열을 `web.config`다음에 추가할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가하는 방법은 무엇입니까?

Azure `ServicEndpoint` SignalR 서비스 끝점의 속성을 설명 하기 위해 클래스가 도입 됩니다.
다음을 통해 Azure SignalR 서비스 SDK를 사용할 때 여러 인스턴스 끝점을 구성할 수 있습니다.

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>라우터를 사용자 정의하는 방법?

ASP.NET 시그널R과 ASP.NET 코어 신호R의 유일한 `GetNegotiateEndpoint`차이점은 에 대한 http 컨텍스트 유형입니다. ASP.NET 시그널R의 경우 [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 형식입니다.

다음은 ASP.NET SignalR에 대한 사용자 지정 협상 예제입니다.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

다음을 사용하여 라우터를 DI 컨테이너에 등록하는 것을 잊지 마십시오.

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>지역 간 시나리오의 구성

`ServiceEndpoint` 개체에는 값 `EndpointType` `primary` 또는 `secondary`가 있는 속성이 있습니다.

`primary`끝점은 클라이언트 트래픽을 수신하는 데 선호되는 끝점이며 보다 안정적인 네트워크 연결을 가진 것으로 간주됩니다. `secondary` 끝점은 네트워크 연결의 신뢰성이 떨어지는 것으로 간주되며 클라이언트를 서버 트래픽으로 가져가는 것이 아니라 메시지를 브로드캐스트하는 것과 같은 클라이언트 트래픽에 서버를 가져가는 데만 사용됩니다.

지역 간 경우 네트워크가 불안정할 수 있습니다. *미국 동부에*위치한 하나의 앱 서버의 경우 동일한 *미국 동부* 지역에 있는 SignalR 서비스 끝점을 `primary` `secondary`로 표시된 다른 지역의 끝점으로 구성할 수 있습니다. 이 구성에서 다른 지역의 서비스 끝점은 이 *미국 동부* 앱 서버에서 메시지를 **받을** 수 있지만 이 앱 서버로 라우팅되는 지역 **간** 클라이언트는 없습니다. 아키텍처는 아래 다이어그램에 나와 있습니다.

![크로스 지오 인프라](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

클라이언트가 기본 `/negotiate` 라우터를 사용 하 고 앱 서버를 사용 하 고 시도 하는 `primary` 경우 SDK 는 사용 가능한 끝점 집합에서 하나의 끝점을 **임의로 선택** 합니다. 기본 끝점을 사용할 수 없는 경우 SDK는 사용 `secondary` 가능한 모든 끝점에서 **임의로 선택합니다.** 서버와 서비스 끝점 간의 연결이 활성 상태로 있을 때 끝점이 **사용 가능한** 것으로 표시됩니다.

지역 간 시나리오에서 클라이언트가 `/negotiate` *미국 동부에서*호스팅되는 앱 서버를 사용하려고 `primary` 하면 기본적으로 항상 동일한 지역에 있는 끝점을 반환합니다. 모든 *미국 동부* 끝점을 사용할 수 없는 경우 클라이언트는 다른 지역의 끝점으로 리디렉션됩니다. 아래 장애 조치 섹션에서는 시나리오를 자세히 설명합니다.

![일반 협상](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>장애 조치

모든 `primary` 끝점을 사용할 수 없는 `/negotiate` 경우 사용 `secondary` 가능한 끝점에서 클라이언트의 선택을 받습니다. 이 장애 조치 메커니즘을 사용하려면 각 `primary` 끝점이 하나 이상의 앱 서버에 끝점으로 사용되어야 합니다.

![장애 조치](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 크기 조정, 샤딩 및 지역 간 시나리오에 대해 동일한 응용 프로그램에서 여러 인스턴스를 구성하는 방법에 대해 배웠습니다.

고가용성 및 재해 복구 시나리오에서도 여러 엔드포인트 지원을 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [재해 복구 및 고가용성을 위한 SignalR 서비스 설정](./signalr-concept-disaster-recovery.md)
