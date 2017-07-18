
---
title: "Service Fabric의 서비스 원격 호출 | Microsoft Docs"
description: "서비스 패브릭 원격 호출을 사용하면 클라이언트와 서비스가 원격 프로시저 호출을 사용하여 서비스와 통신할 수 있도록 합니다."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 92a8894f24c234fbf38eda086531b524cceccfc1
ms.contentlocale: ko-kr
ms.lasthandoff: 06/29/2017


---
# <a name="service-remoting-with-reliable-services"></a>Reliable Services로 서비스 원격 호출
특정한 통신 프로토콜 또는 스택에 얽매여 있지 않는 서비스(예: WebAPI, WCF(Windows Communication Foundation) 등)의 경우, Reliable Services 프레임워크가 원격 메커니즘을 제공하여 서비스에 대한 원격 프로시저 호출을 신속하고 간편하게 설정합니다.

## <a name="set-up-remoting-on-a-service"></a>서비스에 원격 호출 설정
서비스에 대한 원격 호출은 간단한 두 가지 단계로 수행됩니다.

1. 서비스로 구현할 인터페이스를 만듭니다. 이 인터페이스는 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의합니다. 메서드는 작업을 반환하는 비동기 메서드여야 합니다. 인터페이스는 서비스에 원격 호출 인터페이스가 있다는 것을 신호하기 위해 `Microsoft.ServiceFabric.Services.Remoting.IService` 를 구현해야 합니다.
2. 서비스에서 원격 수신기를 사용합니다. 이것은 원격 호출 기능을 제공하는 `ICommunicationListener` 구현입니다. `Microsoft.ServiceFabric.Services.Remoting.Runtime` 네임스페이스는 기본 원격 전송 프로토콜을 사용하여 원격 수신기를 만드는 데 사용할 수 있는 상태 비저장 및 상태 저장 서비스에 대해 확장 메서드인 `CreateServiceRemotingListener`를 포함합니다.

참고: `Remoting` 네임스페이스는 `Microsoft.ServiceFabric.Services.Remoting`이라는 별도의 NuGet 패키지로 제공됩니다. 

예를 들어 다음의 상태 비저장 서비스는 단일 메서드를 노출하여 원격 프로시저 호출에 "Hello World"를 가져옵니다.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] { new ServiceInstanceListener(context =>            this.CreateServiceRemotingListener(context)) };
    }
}
```
> [!NOTE]
> 서비스 인터페이스의 인수 및 반환 형식은 간단한 형식, 복합 형식 또는 사용자 지정 형식이 가능하지만 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)에 의한 직렬화가 가능해야 합니다.
>
>

## <a name="call-remote-service-methods"></a>원격 서비스 메서드 호출
원격 스택을 사용하여 서비스에서 메서드를 호출하는 작업은 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 클래스를 통해 서비스에 대한 로컬 프록시를 사용하여 수행됩니다. `ServiceProxy` 메서드는 서비스가 구현하는 것과 동일한 인터페이스를 사용하여 로컬 프록시를 만듭니다. 이 프록시를 통해 원격으로 인터페이스의 메서드를 간단하게 호출할 수 있습니다.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

원격 호출 프레임워크는 서비스에 throw된 예외를 클라이언트에 전파합니다. 따라서 `ServiceProxy` 을 사용하여 클라이언트에서 논리를 예외 처리하는 작업은 서비스가 throw하는 예외를 직접 처리할 수 있습니다.

## <a name="service-proxy-lifetime"></a>서비스 프록시 수명
ServiceProxy 만들기는 가벼운 작업이므로 사용자가 원하는 만큼 만들 수 있습니다. 서비스 프록시는 사용자가 필요할 때까지 다시 사용할 수 있습니다. 사용자는 예외 발생 시 동일한 프록시를 다시 사용할 수 있습니다. 각 서비스 프록시는 유선으로 메시지를 보내는 데 사용되는 통신 클라이언트를 포함합니다. API를 호출하는 동안 사용된 통신 클라이언트가 유효한지 내부적으로 확인합니다. 그 결과에 따라 통신 클라이언트를 다시 만듭니다. 따라서 사용자는 예외 발생 시 serviceproxy를 다시 작성할 필요가 없습니다.

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory 수명
[ServiceProxyFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory)는 다른 원격 인터페이스를 위한 프록시를 만드는 팩터리입니다. 프록시를 만드는 데 API ServiceProxy.Create를 사용하면 프레임워크는 singelton ServiceProxyFactory를 만듭니다.
[IServiceRemotingClientFactory](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.remoting.client.iserviceremotingclientfactory) 속성을 재정의해야 하는 경우 수동으로 만드는 것이 유용합니다.
팩터리는 비용이 많이 드는 작업입니다. ServiceProxyFactory는 통신 클라이언트의 캐시를 유지 관리합니다.
ServiceProxyFactory를 가능한 한 오랫동안 캐시하는 것이 가장 좋습니다.

## <a name="remoting-exception-handling"></a>원격 예외 처리
서비스 API에 의해 throw되는 모든 원격 예외는 AggregateException으로 클라이언트에 다시 전송됩니다. RemoteExceptions는 DataContract 직결화가 가능해야 합니다. 그렇지 않으면[ServiceException](https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.serviceexception)이 직렬화 오류가 있는 프록시 API에 throw됩니다.

ServiceProxy는 만들어진 서비스 파티션에 대한 모든 장애 조치(failover) 예외를 처리합니다. 장애 조치(Failover) 예외(영구적인 예외)가 있는 경우 끝점을 다시 확인하고 올바른 끝점으로 호출을 다시 시도합니다. 장애 조치(Failover) 예외에 대한 재시도 횟수는 무한합니다.
TransientExceptions의 경우에는 호출만 다시 시도합니다.

기본 재시도 매개 변수는 [OperationRetrySettings]에서 제공됩니다. (https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings) 사용자는 OperationRetrySettings 개체를 ServiceProxyFactory 생성자에 전달하여 이러한 값을 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services와 WCF 통신](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services에 대한 통신 보안 유지](service-fabric-reliable-services-secure-communication.md)

