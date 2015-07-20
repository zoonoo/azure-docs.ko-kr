<properties
   pageTitle="서비스 패브릭이 제공하는 기본 통신 스택"
   description="이 문서에서는 신뢰할 수 있는 서비스의 프레임워크에서 통신할 서비스와 클라이언트에 대해 제공하는 기본 통신 스택을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="BharatNarasimman"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="04/13/2015"
   ms.author="bharatn@microsoft.com"/>

# 신뢰할 수 있는 서비스 프레임워크가 제공하는 기본 통신 스택
특정 통신 스택 구현(WebAPI, WCF 등)에 얽매이지 않는 서비스 작성자를 위해 이 프레임워크는 서비스와 클라이언트 간의 통신을 설정하는 데 사용할 수 있는 클라이언트 및 서비스 측 통신 부분을 제공합니다.

> [AZURE.NOTE]아래에 언급된 기능을 얻으려면 최신 NuGet 패키지로 업데이트하세요.

## 서비스 통신 수신기
서비스의 기본 통신 수신기는 `ServiceCommunicationListener` 클래스에 구현됩니다.

```csharp

public class ServiceCommunicationListener<TServiceImplementation> : ICommunicationListener where TServiceImplementation : class
{
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType);
    public ServiceCommunicationListener(TServiceImplementation serviceImplementationType, string endpointResourceName);

    public void Abort();
    public Task CloseAsync(CancellationToken cancellationToken);
    public void Initialize(ServiceInitializationParameters serviceInitializationParameters);
    public Task<string> OpenAsync(CancellationToken cancellationToken);
}

```
서비스가 구현하고 클라이언트에 노출하려고 하는 메서드는 `IService` 인터페이스에서 상속되는 인터페이스의 비동기 메서드로 정의됩니다. 그러면 서비스가 `ServiceCommunicationListener` 개체를 인스턴스화하고 [`CreateCommunicationListener` 메서드](service-fabric-reliable-services-communication.md)에 반환할 수 있습니다. 예를 들어 이 통신 스택을 설정하는 HelloWorld 서비스 코드는 다음과 같이 정의할 수 있습니다.

```csharp

[DataContract]
public class Message
{
    [DataMember]
    public string Content;
}

public interface IHelloWorld : IService
{
    Task<Message> GetGreeting();
}

public class HelloWorldService : StatelessService, IHelloWorld
{
    public const string ServiceTypeName = "HelloWorldUsingDefaultCommunicationType";

    private Message greeting = new Message() { Content = "Default greeting" };

    protected override ICommunicationListener CreateCommunicationListener()
    {
        return new ServiceCommunicationListener<HelloWorldService>(this);
    }

    public Task<Message> GetGreeting()
    {
        return Task.FromResult(this.greeting);
    }
}

```
> [AZURE.NOTE]서비스 인터페이스(예를 들어 위의 Message 클래스)의 인수 및 반환 형식은 .net [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)로 직렬화할 수 있어야 합니다.


## ServiceCommunicationListener와 통신하는 클라이언트 작성
이 프레임워크는 `ServiceCommunicationListener`를 사용하여 서비스와 통신하는 클라이언트를 위해 `ServiceProxy` 클래스를 제공합니다.

```csharp

public abstract class ServiceProxy : IServiceProxy
{
...

    public static TServiceInterface Create<TServiceInterface>(Uri serviceName);

...
}

```

클라이언트는 해당 서비스 인터페이스를 구현하고 프록시 개체에서 메서드를 호출하는 서비스 프록시 개체를 인스턴스화할 수 있습니다.

```csharp

var helloWorldClient = ServiceProxy.Create<IHelloWorld>(helloWorldServiceName);

var message = await helloWorldClient.GetGreeting();

Console.WriteLine("Greeting is {0}", message.Content);


```

>[AZURE.NOTE]통신 프레임워크는 서비스에서 클라이언트에 throw된 예외 전파를 처리합니다. 따라서 ServiceProxy를 사용하는 클라이언트에서 예외 처리 논리는 서비스가 잠재적으로 throw할 수 있는 예외를 직접 처리할 수 있습니다.
 

<!---HONumber=July15_HO2-->