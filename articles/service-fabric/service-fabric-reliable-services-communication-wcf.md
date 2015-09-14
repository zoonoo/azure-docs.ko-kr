<properties
   pageTitle="신뢰할 수 있는 서비스 API가 제공하는 WCF 기반 통신 스택"
	description="이 문서에서는 신뢰할 수 있는 서비스의 API에서 제공되는 WCF 기반 통신 스택을 설명합니다."
	services="service-fabric"
	documentationCenter=".net"
	authors="BharatNarasimman"
	manager="vipulm"
	editor=""/>

<tags
   ms.service="service-fabric"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="required"
	ms.date="08/27/2015"
	ms.author="bharatn@microsoft.com"/>

# 신뢰할 수 있는 서비스에 대한 WCF 기반 통신 스택
신뢰할 수 있는 서비스 프레임워크는 서비스 작성자가 서비스에 사용할 통신 스택을 결정하도록 지원합니다. [`CreateCommunicationListener`](../service-fabric-reliable-service-communication.md) 메서드에서 반환된 `ICommunicationListener`를 통해 선택한 통신 스택을 연결할 수 있습니다. 이 프레임워크는 WCF 기반 통신을 사용하려는 서비스 작성자를 위한 WCF 기반 통신 스택 구현을 제공합니다.

## WCF 통신 수신기
`ICommunicationListener`의 WCF 특정 구현은 `WcfCommunicationListener` 클래스에서 제공합니다.

```csharp

public WcfCommunicationListener(
    Type communicationInterfaceType,
    Type communicationImplementationType);

protected override ICommunicationListener CreateCommunicationListener()
    {
        WcfCommunicationListener communicationListener = new WcfCommunicationListener(typeof(ICalculator), this)
        {
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // which identifies the endpoint that the wcf servicehost should listen on.
            //
            EndpointResourceName = "ServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            Binding = this.CreateListenBinding()
        };

        return communicationListener;
    }

```

## WCF 통신 스택에 대한 클라이언트 작성
WCF를 사용하여 서비스와 통신하는 클라이언트를 작성할 수 있도록 이 프레임워크에서는 [`ClientCommunicationFactoryBase`](../service-fabric-reliable-service-communication.md)의 WCF 특정 구현인 `WcfClientCommunicationFactory`를 제공합니다.

```csharp

public WcfCommunicationClientFactory(
    ServicePartitionResolver servicePartitionResolver = null,
    Binding binding = null,
    object callback = null,
    IList<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null)

```

WCF 통신 채널은 `WcfCommunicationClient`에서 생성한 `WcfCommunicationClientFactory`에서 액세스할 수 있습니다.

```csharp

public class WcfCommunicationClient<TChannel> : ICommunicationClient where TChannel : class
{
    public TChannel Channel { get; }
    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}

```

클라이언트 코드는 `ServicePartitionClient`와 `WcfCommunicationClientFactory`를 함께 사용하여 서비스 끝점을 결정하고 서비스와 통신을 설정할 수 있습니다.

```csharp

//
// Create a service resolver for resolving the endpoints of the calculator service.
//
ServicePartitionResolver serviceResolver = new ServicePartitionResolver(() => new FabricClient());

//
// Create the binding.
//
NetTcpBinding binding = CreateClientConnectionBinding();

var clientFactory = new WcfCommunicationClientFactory<ICalculator>(
    serviceResolver,// ServicePartitionResolver
    binding,        // Client binding
    null,           // Callback object
    null);          // donot retry Exception types


//
// Create a client for communicating with the calc service which has been created with
// Singleton partition scheme.
//
var calculatorServicePartitionClient = new ServicePartitionClient<WcfCommunicationClient<ICalculator>>(
    clientFactory,
    ServiceName);

//
// Call the service to perform the operation.
//
var result = calculatorServicePartitionClient.InvokeWithRetryAsync(
    client => client.Channel.AddAsync(2, 3)).Result;


```
 

<!---HONumber=September15_HO1-->