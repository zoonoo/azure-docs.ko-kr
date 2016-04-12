<properties
   pageTitle="서비스 패브릭에서 서비스에 대한 통신 보호 | Microsoft Azure"
   description="서비스 패브릭 클러스터에서 실행되는 Reliable Services에 대한 통신을 보호하는 방법을 간략하게 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="punewa"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="03/22/2016"
   ms.author="punewa"/>

# 서비스 패브릭에서 서비스에 대한 통신 보호
통신의 가장 중요한 측면 중 하나는 보안입니다. Reliable Services 응용 프로그램 프레임워크는 사용 가능한 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 이 문서에서는 서비스 원격 및 WCF 통신 스택 사용 시 보안을 설정하는 방법에 대해 설명합니다.

## 서비스 원격 사용 시의 서비스 보호
서비스에 대해 보안 원격을 설정하는 과정에서는 다음 단계를 수행합니다.

1. Reliable Services 원격 설정 방법에 대해 설명했던 이전 [예제](service-fabric-reliable-services-communication-remoting.md)를 사용하겠습니다. 먼저 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의하는 `IHelloWorldStateful` 인터페이스부터 작성합니다. 또한 이 서비스는 `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 네임스페이스에 선언되는 `FabricTransportServiceRemotingListener`를 사용합니다. 이것은 원격 호출 기능을 제공하는 `ICommunicationListener` 구현입니다.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. 수신기 설정 및 보안 자격 증명을 추가합니다.

    서비스 통신을 보호하는 데 사용할 인증서는 클러스터의 모든 노드에 설치해야 합니다. 두 가지 방법으로 수신기 설정 및 보안 자격 증명을 제공할 수 있습니다.

    1. 서비스 코드에서 직접 제공

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listnerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listnerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. [구성 패키지](service-fabric-application-model.md) 사용

        settings.xml에 `TransportSettings` 섹션을 추가합니다.

        ```xml
        <!--Section name should always end with "TransportSettings"-->
        <!--Here we are using a prefix "HelloWorldStateful"-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        이 경우 `CreateServiceReplicaListeners` 메서드는 다음과 같습니다.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         settings.xml에 접두사 없이 `TransportSettings` 섹션을 추가하면 `FabricTransportListenerSettings`는 기본적으로 이 섹션의 모든 설정을 로드합니다.

         ```xml
         <!--"TransportSettings" section without any prefix-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         이 경우 `CreateServiceReplicaListeners` 메서드는 다음과 같습니다.

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. 여기서는 원격 스택을 사용하여 보안 서비스에 대해 메서드를 호출할 때 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 클래스를 사용해 서비스 프록시를 만드는 대신 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`를 사용하며, `SecurityCredentials`가 포함된 `FabricTransportSettings`를 전달합니다.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    클라이언트 코드를 서비스의 일부로 실행하는 경우 settings.xml에서 `FabricTransportSettings`를 로드할 수 있습니다. 이렇게 하려면 위에 나와 있는 서비스 코드와 비슷한 TransportSettings 섹션을 만들고 클라이언트 코드를 다음과 같이 변경합니다.

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    클라이언트를 서비스의 일부로 실행하지 않는 경우에는 client\_name.exe와 같은 위치에 client\_name.settings.xml 파일을 만들고 해당 파일에 TransportSettings 섹션을 만들 수 있습니다.

    서비스와 마찬가지로 클라이언트 settings.xml/client\_name.settings.xml의 경우에도 *접두사* 없이 `TransportSettings` 섹션을 추가하면 `FabricTransportSettings`는 기본적으로 이 섹션의 모든 설정을 로드합니다.

    그러면 위 코드를 더 간략하게 작성할 수 있습니다.

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## WCF 기반 통신 스택 사용 시 서비스 보호

여기서는 Reliable Services에 대한 WCF 기반 통신을 설정하는 방법을 설명했던 이전 [예제](service-fabric-reliable-services-communication-wcf.md)를 사용하되 보안을 유지하기 위해 이 예제를 확장합니다.

1. 서비스의 경우 안전한 `WcfCommunicationListener`를 만들어야 합니다. 이렇게 하려면 `CreateServiceReplicaListeners` 메서드를 수정해야 합니다.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListner = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example we will be using NetTcpBinding
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the servicehost credentials.
        wcfCommunicationListner.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListner;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```
    
2. 클라이언트에서는 이전 [예제](service-fabric-reliable-services-communication-wcf.md)에서 만든 `WcfCommunicationClient` 클래스가 변경되지 않고 그대로 유지됩니다. 그러나 `WcfCommunicationClientFactory`의 `CreateClientAsync` 메서드를 재정의해야 합니다.

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by the
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    `SecureWcfCommunicationClientFactory`를 사용하여 `WcfCommunicationClient`를 만든 다음 클라이언트를 사용하여 서비스 메서드를 호출합니다.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## 다음 단계

* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)

<!---HONumber=AcomDC_0330_2016-->