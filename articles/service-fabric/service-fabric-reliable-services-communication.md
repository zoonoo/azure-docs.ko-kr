<properties
   pageTitle="서비스 통신 모델 개요"
   description="이 문서에서는 신뢰할 수 있는 서비스의 API에서 지원하는 통신 모델의 기본 사항을 설명합니다."
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
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# 서비스 통신 모델

신뢰할 수 있는 서비스 프로그래밍 모델은 서비스 작성자가 서비스 끝점을 노출하는 데 사용하려는 통신 메커니즘을 지정하고 클라이언트가 서비스 끝점을 검색하고 통신하는 데 사용할 수 있는 추상화를 제공합니다.

## 서비스 통신 스택 설정

신뢰할 수 있는 서비스 API는 서비스 작성자가 서비스에 다음 메서드를 구현하여 선택한 통신 스택을 서비스에 연결할 수 있게 지원합니다.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

`ICommunicationListener` 인터페이스는 서비스의 통신 수신기에서 구현해야 하는 인터페이스를 정의합니다.

```csharp

public interface ICommunicationListener
{
    void Initialize(ServiceInitializationParameters serviceInitializationParameters);

    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```
서비스에 필요한 끝점은 끝점 섹션의 [서비스 매니페스트](service-fabric-application-model.md)를 통해 설명됩니다.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

통신 수신기는 `ServiceInitializationParameters`의 `CodePackageActivationContext`에서 할당된 끝점 리소스에 액세스하고 해당 끝점이 열릴 때 요청 수신 대기를 시작할 수 있습니다.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]끝점 리소스는 전체 서비스 패키지에 공통되며, 서비스 패키지가 활성화될 때 서비스 패브릭에서 할당합니다.(자세한 내용은 [서비스 패브릭 ServiceModel](../service-fabric-service-model.md) 확인). 따라서 동일한 ServiceHost에서 호스팅되는 모든 복제본은 동일한 포트를 공유합니다. 이는 통신 수신기가 포트 공유를 지원해야 한다는 의미입니다. 이에 대한 권장 방법은 통신 수신기가 수신 주소를 생성할 때 파티션 ID 및 복제본/인스턴스 ID를 사용하도록 하는 것입니다.

```csharp

var replicaOrInstanceId = 0;
var parameters = this.serviceInitializationParameters as StatelessServiceInitializationParameters;
if (parameters != null)
{
  replicaOrInstanceId = parameters.InstanceId;
}
else
{
  replicaOrInstanceId = ((StatefulServiceInitializationParameters) this.serviceInitializationParameters).ReplicaId;
}

var nodeContext = FabricRuntime.GetNodeContext();

var listenAddress = new Uri(
    string.Format(
        CultureInfo.InvariantCulture,
        "{0}://{1}:{2}/{5}/{3}-{4}",
        scheme,
        nodeContext.IPAddressOrFQDN,
        port,
        this.serviceInitializationParameters.PartitionId,
        replicaOrInstanceId,
        Guid.NewGuid().ToString()));

```

## 클라이언트-서비스 통신
신뢰할 수 있는 서비스 API는 서비스와 쉽게 통신하기 위한 클라이언트를 작성하도록 지원하는 다음과 같은 추상화를 제공합니다.

## ServicePartitionResolver
ServicePartitionResolver 클래스는 클라이언트가 런타임에 서비스 패브릭 서비스의 끝점을 확인하는 데 도움이 됩니다.

> [AZURE.TIP]서비스의 끝점을 결정하는 프로세스를 서비스 패브릭 용어로 서비스 끝점 확인이라고 합니다.

```csharp

public delegate FabricClient CreateFabricClientDelegate();

// ServicePartitionResolver methods

public ServicePartitionResolver(CreateFabricClientDelegate createFabricClient);

Task<ResolvedServicePartition> ResolveAsync(Uri serviceName,
    long partitionKey,
    CancellationToken cancellationToken);

Task<ResolvedServicePartition> ResolveAsync(ResolvedServicePartition previousRsp,
    CancellationToken cancellationToken);


```
> [AZURE.NOTE]FabricClient는 서비스 패브릭 클러스터의 다양한 관리 작업에서 서비스 패브릭 클러스터와 통신하는 데 사용되는 개체입니다.

일반적으로 클라이언트 코드는 `ServicePartitionResolver`와 직접 연동할 필요가 없습니다. 신뢰할 수 있는 서비스의 API에서 생성되고 다른 도우미 클래스에 전달되며, 내부적으로 확인 프로그램을 사용하여 클라이언트가 서비스와 통신하도록 지원합니다.

## CommunicationClientFactory
`ICommunicationClientFactory`는 ServiceFabric 서비스와 통신할 수 있는 클라이언트를 생성하는 통신 클라이언트 팩터리에서 구현하는 기본 인터페이스를 정의합니다. CommunicationClientFactory의 구현은 클라이언트가 통신하려고 하는 서비스 패브릭 서비스에서 사용하는 통신 스택에 따라 달라집니다. 신뢰할 수 있는 서비스의 API는 이 `ICommunicationClientFactory` 인터페이스의 기본 구현을 제공하고 모든 통신 스택에 공통된 작업을 수행하는 CommunicationClientFactoryBase<TCommunicationClient>를 제공합니다(`ServicePartitionResolver`를 사용하여 서비스 끝점을 결정하는 것처럼). 클라이언트는 일반적으로 추상 CommunicationClientFactoryBase 클래스를 구현하여 통신 스택 관련 논리를 처리합니다.

```csharp

protected CommunicationClientFactoryBase(
    ServicePartitionResolver servicePartitionResolver = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IEnumerable<Type> doNotRetryExceptionTypes = null);


public class MyCommunicationClient : ICommunicationClient
{
    public MyCommunicationClient(MyCommunicationChannel communicationChannel)
    {
      this.CommunicationChannel = communicationChannel;
    }
    public MyCommunicationChannel CommunicationChannel { get; private set; }
    public ResolvedServicePartition ResolvedServicePartition;

}

public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient1 client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(ResolvedServiceEndpoint endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(ResolvedServiceEndpoint endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

## ServicePartitionClient
`ServicePartitionClient`는 CommunicationClientFactory(및 내부적으로 ServicePartitionResolver)를 사용하며 일반적인 통신 및 서비스 패브릭의 일시적 예외에 대한 재시도를 처리하여 서비스와 통신하도록 도와줍니다.

```csharp

public ServicePartitionClient(
    ICommunicationClientFactory<TCommunicationClient> factory,
    Uri serviceName,
    long partitionKey);

public async Task<TResult> InvokeWithRetryAsync<TResult>(
    Func<TCommunicationClient, Task<TResult>> func,
    CancellationToken cancellationToken,
    params Type[] doNotRetryExceptionTypes)

```

일반적인 사용 패턴은 다음과 같습니다.

```csharp

public MyCommunicationClientFactory myCommunicationClientFactory;
public Uri myServiceUri;

... other client code ..

// Call the service corresponding to the partitionKey myKey.

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myKey);

  var result = await myServicePartitionClient.InvokeWithRetryAsync(
      client =>
      {
        // Communicate with the service using the client.
        throw new NotImplementedException();
      },
      CancellationToken.None);


... other client code ...

```

## 다음 단계
* [신뢰할 수 있는 서비스 프레임워크가 제공하는 기본 통신 스택](service-fabric-reliable-services-communication-default.md)

* [신뢰할 수 있는 서비스 프레임워크가 제공하는 WCF 기반 통신 스택](service-fabric-reliable-services-communication-wcf.md)

* [WebAPI 통신 스택을 사용하는 신뢰할 수 있는 서비스 API를 사용하여 서비스 작성](service-fabric-reliable-services-communication-webapi.md)
 

<!---HONumber=August15_HO6-->