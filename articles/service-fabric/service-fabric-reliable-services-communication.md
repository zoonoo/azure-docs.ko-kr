<properties
   pageTitle="Reliable Services 통신 개요 | Microsoft Azure"
   description="서비스에서 수신기 열기, 끝점 확인 및 서비스 간 통신을 비롯한 Reliable Services 통신 모델의 개요입니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="11/17/2015"
   ms.author="vturecek@microsoft.com"/>

# Reliable Services 통신 모델

플랫폼인 Azure 서비스 패브릭은 서비스 간에 이루어지는 통신을 전혀 알 수 없습니다. UDP에서 HTTP까지 모든 프로토콜 및 스택이 허용됩니다. 서비스 개발자가 서비스가 통신하는 방법을 선택합니다. Reliable Services 응용 프로그램 프레임워크는 사용자 지정 통신 스택을 롤아웃하는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 클라이언트가 검색하고 서비스 끝점과 통신하는 데 사용할 수 있는 추상화가 포함됩니다.

## 서비스 통신 설정

Reliable Services API는 서비스 통신을 위해 간단한 인터페이스를 사용합니다. 서비스에 대한 끝점을 열려면 다음 인터페이스를 구현하기만 하면 됩니다.

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

그런 다음 서비스 기본 클래스 메서드 재정의에서 통신 수신기를 반환하여 통신 수신기 구현을 추가합니다.

상태 비저장 서비스의 경우:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
```

상태 저장 서비스의 경우:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
```

두 경우 모두 수신기의 컬렉션을 반환합니다. 이렇게 하면 서비스에서 쉽게 여러 수신기를 사용할 수 있습니다. 예를 들어 HTTP 수신기 및 별도의 WebSocket 수신기가 있을 수 있습니다. 각 수신기는 이름을 가져오며 이름 : 주소 쌍의 결과 콜렉션은 클라이언트가 서비스 인스턴스 또는 파티션에 대한 수신 주소를 요청하는 경우 JSON 개체로 표현됩니다.

상태 비저장 서비스에서는 재정의가 ServiceInstanceListeners의 컬렉션을 반환합니다. ServiceInstanceListener는 ICommunicationListener를 만들 함수를 포함하고 이름을 지정합니다. 상태 저장 서비스에서는 재정의가 ServiceReplicaListeners의 컬렉션을 반환합니다. 이는 ServiceReplicaListener에 보조 복제본에서 ICommunicationListener를 여는 옵션이 있기 때문에 해당 상태 비저장에 상응하는 것과는 약간 다릅니다. 서비스에서 여러 통신 수신기를 사용할 수 있을 뿐만 아니라 보조 복제본에서 요청을 수락하는 수신기와 주 복제본에서만 수신하는 수신기를 지정할 수도 있습니다.

예를 들어 주 복제본에서는 RPC만 호출하는 ServiceRemotingListener와 보조 복제본에서는 읽기 요청을 수행하는 두 번째 사용자 지정 수신기가 있을 수 있습니다.

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(initParams =>
            new MyCustomListener(initParams),
            "customReadonlyEndpoint",
            true),

        new ServiceReplicaListener(initParams =>
            new ServiceRemotingListener<IMyStatefulInterface2>(initParams, this),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

마지막으로 끝점의 섹션에 있는 [서비스 매니페스트](service-fabric-application-model.md)에서 서비스에 필요한 끝점을 설명할 수 있습니다.

```xml

<Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http" Type="Input" />
    <Endpoints>
</Resources>

```

통신 수신기는 `ServiceInitializationParameters`의 `CodePackageActivationContext`에서 할당된 끝점 리소스에 액세스할 수 있습니다. 그런 다음 수신기가 열릴 때 요청을 수신하기 시작할 수 있습니다.

```csharp

var codePackageActivationContext = this.serviceInitializationParameters.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE]끝점 리소스는 전체 서비스 패키지에 공통되며 서비스 패키지가 활성화될 때 서비스 패브릭에서 할당합니다. 동일한 ServiceHost에서 호스팅되는 모든 복제본은 동일한 포트를 공유합니다. 이는 통신 수신기가 포트 공유를 지원해야 한다는 의미입니다. 이에 대한 권장 방법은 통신 수신기가 수신 주소를 생성할 때 파티션 ID 및 복제본/인스턴스 ID를 사용하도록 하는 것입니다.

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

`ICommunicationListener`을 작성하는 방법에 대한 전체 연습은 [OWIN 자체 호스팅을 사용한 서비스 패브릭 Web API 서비스](service-fabric-reliable-services-communication-webapi.md)를 참조하세요.

### 클라이언트-서비스 통신
Reliable Services API는 서비스와 통신하기 위한 클라이언트를 쉽게 작성하도록 지원하는 다음과 같은 추상화를 제공합니다.

#### ServicePartitionResolver
이 유틸리티 클래스를 통해 클라이언트가 런타임에 서비스 패브릭 서비스의 끝점을 확인할 수 있습니다. 서비스 패브릭 용어로 서비스의 끝점을 결정하는 프로세스를 서비스 끝점 확인이라고 합니다.

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
> [AZURE.NOTE]FabricClient는 서비스 패브릭 클러스터의 다양한 관리 작업에서 클러스터와 통신하는 데 사용되는 개체입니다.

일반적으로 클라이언트 코드는 `ServicePartitionResolver`와 직접 연동할 필요가 없습니다. 이 코드가 생성되면 Reliable Services API의 다른 도우미 클래스에 전달됩니다. 이러한 클래스는 내부적으로 확인자를 사용하여 클라이언트가 서비스와 통신하도록 도와줍니다.

#### CommunicationClientFactory
`ICommunicationClientFactory`는 서비스 패브릭 서비스와 통신할 수 있는 클라이언트를 생성하는 통신 클라이언트 팩터리에서 구현되는 기본 인터페이스를 정의합니다. CommunicationClientFactory의 구현은 클라이언트가 통신하려고 하는 서비스 패브릭 서비스에서 사용하는 통신 스택에 따라 달라집니다. Reliable Services API는 `CommunicationClientFactoryBase<TCommunicationClient>`를 제공합니다. 이는 `ICommunicationClientFactory` 인터페이스의 기본 구현을 제공하고 모든 통신 스택에 공통된 작업을 수행합니다. (이러한 작업은 `ServicePartitionResolver`를 사용하여 서비스 끝점을 확인하는 것을 포함합니다.) 클라이언트는 일반적으로 추상 CommunicationClientFactoryBase 클래스를 구현하여 통신 스택에 특정된 논리를 처리합니다.

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
    protected override void AbortClient(MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
        throw new NotImplementedException();
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
        throw new NotImplementedException();
    }
}

```

#### ServicePartitionClient
`ServicePartitionClient`은 CommunicationClientFactory(및 내부적으로 ServicePartitionResolver)를 사용합니다. 또한 일반적인 통신 및 서비스 패브릭의 일시적 예외에 대한 재시도를 처리하여 서비스와의 통신에 도움이 됩니다.

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
* [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)

* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)

* [Reliable Services를 사용한 WCF 통신](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0121_2016-->