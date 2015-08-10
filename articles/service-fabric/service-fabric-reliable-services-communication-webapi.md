<properties
   pageTitle="OWIN 자체 호스트를 사용하는 서비스 패브릭 Web API 서비스 | Microsoft Azure"
   description="이 서비스 패브릭 문서에서는 신뢰할 수 있는 서비스에서 OWIN이 자체 호스팅되는 ASP.NET Web API를 사용하여 서비스 통신을 구현하는 방법을 설명합니다."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/23/2015"
   ms.author="vturecek"/>

# OWIN 자체 호스팅을 사용하여 Microsoft Azure 서비스 패브릭 웹 API 서비스 시작

서비스 패브릭은 서비스가 사용자, 그리고 다른 서비스와 통신하는 방법을 결정할 때 도움이 됩니다. 이 자습서에서는 서비스 패브릭의 *신뢰할 수 있는 서비스* API에서 OWIN이 자체 호스팅되는 ASP.NET Web API를 사용하여 서비스 통신을 구현하는 방법을 중점적으로 살펴봅니다. *신뢰할 수 있는 서비스* 플러그형 통신 API에 대해 심층적으로 살펴보고, 예제로 사용된 웹 API를 통해 서비스의 사용자 지정 통신 수신기를 설정하는 방법을 단계별로 보여줍니다. 웹 API 통신 수신기의 완전한 예제를 보려면 [GitHub에서 서비스 패브릭 웹 응용 프로그램 샘플](https://github.com/Azure/servicefabric-samples/tree/master/samples/Services/VS2015/WebApplication)을 확인하십시오.


## 서비스 패브릭의 Web API 소개

ASP.NET Web API는 .NET Framework 위에 HTTP API를 구축할 때 널리 사용되는, 강력한 프레임워크입니다. Web API에 아직 친숙하지 않은 경우 이에 대한 자세한 내용을 보려면 [www.asp.net/webapi](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)를 방문하십시오.

서비스 패브릭의 Web API는 여러분이 알고 있고 즐겨 사용하는 것과 동일한 ASP.NET Web API입니다. Web API 응용 프로그램을 *호스트*하는 방법에 차이가 있습니다(힌트: IIS를 사용하지 않음). 차이를 좀 더 잘 이해할 수 있도록 두 부분으로 나누어 보겠습니다.

 1. Web API 응용 프로그램(컨트롤러, 모델 등)
 2. 호스트(웹 서버, 일반적으로 IIS)

여기에서 Web API 응용 프로그램 자체는 변경되지 않습니다. 이전에 작성한 Web API 응용 프로그램과 다르지 않으며 대부분의 응용 프로그램 코드를 바로 이동할 수 있습니다. IIS에서 응용 프로그램을 호스팅하는 기능에 익숙한 경우 응용 프로그램 호스팅 방식이 기존 방식과 약간 다르게 보일 수 있습니다. 호스팅 부분으로 들어가기 전에 더 친숙한 부분인 Web API 응용 프로그램부터 살펴보겠습니다.


## Web API 응용 프로그램 설정

Visual Studio 2015에서 단일 상태 비저장 서비스를 사용해서 새 응용 프로그램을 만들어 시작합니다.

![새 서비스 패브릭 응용 프로그램 만들기](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

![단일 상태 비저장 서비스 만들기](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

이는 Web API 응용 프로그램을 호스팅하게 될 빈 상태 비저장 서비스를 제공합니다. 전체적으로 어떻게 만들어지는지 알아보기 위해 응용 프로그램을 처음부터 설정하겠습니다.

첫 번째 단계는 Web API에 대한 일부 NuGet 패키지를 가져오는 것입니다. 우리가 사용할 패키지는 **Microsoft.AspNet.WebApi.OwinSelfHost**입니다. 이 패키지에는 필요한 모든 Web API 패키지 및 *호스트* 패키지가 포함되어 있습니다. 호스트 패키지는 나중에 중요하게 사용됩니다.

![](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

패키지가 설치되면 기본 Web API 프로젝트 구조 구축을 시작할 수 있습니다. Web API를 사용한 적이 있다면 프로젝트 구조가 매우 익숙할 것입니다. 기본 Web API 디렉터리를 만들어 시작합니다.

 + App\_Start
 + Controllers
 + Models

App\_Start 디렉터리에 기본 웹 API 구성 클래스를 추가합니다.

 + FormatterConfig.cs

```csharp

namespace WebApi
{
    using System.Net.Http.Formatting;

    public static class FormatterConfig
    {
        public static void ConfigureFormatters(MediaTypeFormatterCollection formatters)
        {
        }
    }
}

```

 + RouteConfig.cs

```csharp

namespace WebApi
{
    using System.Web.Http;

    public static class RouteConfig
    {
        public static void RegisterRoutes(HttpRouteCollection routes)
        {
            routes.MapHttpRoute(
                    name: "DefaultApi",
                    routeTemplate: "api/{controller}/{id}",
                    defaults: new { controller = "Default", id = RouteParameter.Optional }
                );
        }
    }
}

```

Controllers 디렉터리에 기본 컨트롤러를 추가합니다.

 + DefaultController.cs

```csharp

namespace WebApi.Controllers
{
    using System.Collections.Generic;
    using System.Web.Http;

    public class DefaultController : ApiController
    {
        // GET api/values
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5
        public void Delete(int id)
        {
        }
    }
}

```

마지막으로, 프로젝트 루트에 Startup 클래스를 추가하여 라우팅, 포맷터 및 기타 구성 설치 프로그램을 등록합니다. 이것은 Web API가 *host*에 플러그인하는 위치이기도 합니다. 이 부분은 나중에 다시 살펴보겠습니다. Startup 클래스를 설정할 때는 Startup 클래스에 Configuration 메서드를 정의하는 *IOwinAppBuilder*라는 인터페이스를 만듭니다. 엄밀히 말해 Web API가 작동하는 데 필요한 것은 아니지만 나중에 Startup 클래스를 보다 유연 하게 사용할 수 있게 해줍니다.

 + Startup.cs

```csharp

namespace WebApi
{
    using Owin;
    using System.Web.Http;

    public class Startup : IOwinAppBuilder
    {
        public void Configuration(IAppBuilder appBuilder)
        {
            HttpConfiguration config = new HttpConfiguration();

            FormatterConfig.ConfigureFormatters(config.Formatters);
            RouteConfig.RegisterRoutes(config.Routes);

            appBuilder.UseWebApi(config);
        }
    }
}

```

 + IOwinAppBuilder.cs

```csharp

namespace WebApi
{
    using Owin;

    public interface IOwinAppBuilder
    {
        void Configuration(IAppBuilder appBuilder);
    }
}

```

응용 프로그램 부분은 이제 끝났습니다. 이로써 기본 Web API 프로젝트 레이아웃 설정이 끝났습니다. 이전에 작성한 Web API 프로젝트나 기본 웹 API 템플릿과 비교하면 그렇게 많이 다르지 않을 것입니다. 예전과 마찬가지로 컨트롤러와 모델에는 비즈니스 논리가 들어갑니다.

이제 호스팅을 실제로 실행하려면 어떤 작업을 해야 할까요?


## 서비스 호스팅

서비스 패브릭에서 서비스는 서비스 코드를 실행하는 실행 파일인 *서비스 호스트 프로세스*로 실행됩니다. .NET에서 서비스 패브릭에 서비스를 작성할 때는 대부분의 경우 신뢰할 수 있는 서비스 API를 사용하여 서비스를 작성하는데, 이 경우 서비스 유형을 등록하고 코드를 실행하는 .EXE로 서비스 프로젝트가 컴파일됩니다. 상태 비저장 서비스 프로젝트에서 **Program.cs**를 열면 다음이 표시되어야 합니다.

```csharp

public class Program
{
    public static void Main(string[] args)
    {
        try
        {
            using (FabricRuntime fabricRuntime = FabricRuntime.Create())
            {
                fabricRuntime.RegisterServiceType(Service.ServiceTypeName, typeof(Service));

                Thread.Sleep(Timeout.Infinite);
            }
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e);
            throw;
        }
    }
}

```

콘솔 응용 프로그램에 대한 진입점과 아주 비슷하게 보이는 경우 다음과 같은 이유 때문입니다.

![](media/service-fabric-reliable-services-communication-webapi/webapi-projectproperties.png)

서비스 호스트 프로세스와 서비스 등록에 대한 세부 정보는 이 문서의 범위를 벗어나는 내용이지만 이제 **서비스 코드가 자체 프로세스를 실행 중**이므로 알아두어야 합니다.

## OWIN 호스트로 자체 호스팅되는 웹 API

Web API 응용 프로그램 코드가 자체 프로세스에서 호스팅되는 경우 웹 서버에 연결하는 방법은 무엇일까요? [OWIN](http://owin.org/)을 입력합니다. OWIN은 간단히 말해 .NET 웹 응용 프로그램 및 웹 서버 간의 계약입니다. 기존에 ASP.NET(MVC 5까지)은 웹 응용 프로그램이 System.Web을 통해 IIS에 긴밀하게 결합되었습니다. 그러나 Web API는 호스팅하는 웹 서버에서 분리된 웹 응용 프로그램을 작성할 수 있는 OWIN을 구현합니다. 따라서 사용자 고유의 프로세스에서 위에서 설명한 서비스 패브릭 호스팅 모델에 완벽하게 들어맞는 *self-host* OWIN 웹 서버를 시작하고 사용할 수 있습니다.

이 문서에서는 Katana를 Web API 응용 프로그램의 OWIN 호스트로 사용합니다. Katana는 오픈 소스 OWIN 호스트 구현입니다.

> [AZURE.NOTE]Katana에 대한 자세한 내용은 [Katana 사이트](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana)에서 확인할 수 있으며, Katana를 사용하여 웹 API를 자체 호스팅하는 방법에 대한 간략한 문서는 [OWIN을 사용하여 ASP.NET Web API 2를 자체 호스팅](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api)하는 방법에 대한 이 문서를 확인하세요.


## 웹 서버 설정

신뢰할 수 있는 서비스 API는 비즈니스 논리에 대 한 다음과 같은 두 진입점을 제공합니다.

 + 주로 장기 실행 계산 워크로드용으로 고안된 것으로, 원하는 모든 워크로드의 실행을 시작할 수 있는 개방형 진입점 메서드:

```csharp

protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}

```

 + 원하는 통신 스택을 연결할 수 있는 통신 진입점:

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
    ...
}

```

웹 서버 통신 스택 및 Websocket과 같이 나중에 사용할 수 있는 기타 통신 스택은 ICommunicationListener 인터페이스를 사용하여 올바르게 시스템과 통합해야 합니다. 그 이유는 다음 단계에서 더욱 명확해집니다.

먼저 ICommunicationListener를 구현하는 OwinCommunicationListener라는 클래스를 만듭니다.

 + OwinCommunicationListener.cs:

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}

```

ICommunicationListener 인터페이스에서는 서비스에 대한 통신 수신기를 관리하는 4개의 메서드를 제공합니다.

 + **Initialize**: 일반적으로 서비스에서 수신 대기할 주소를 설정합니다. 웹 서버의 경우 URL을 설정하는 데 사용됩니다.
 + **OpenAsync**: 요청에 대한 수신 대기를 시작합니다.
 + **CloseAsync**: 요청에 대한 수신 대기를 중지하고, 진행 중인 모든 요청을 완료하고, 정상적으로 종료합니다.
 + **Abort**: 모든 것을 취소하고 즉시 중지합니다.

시작하려면 URL 경로 접두사에 대한 전용 클래스 멤버 및 이전에 작성된 **Startup** 클래스를 추가합니다. 이는 생성자를 통해 초기화되며 나중에 수신 대기 URL을 설정할 때 사용됩니다. 또한 초기화하는 동안, 그리고 나중에 서버가 시작될 때 생성되는 수신 대기 주소 및 서버 핸들을 저장하려면 전용 클래스 멤버를 추가합니다.

```csharp

public class OwinCommunicationListener : ICommunicationListener
{
    private readonly IOwinAppBuilder startup;
    private readonly string appRoot;
    private IDisposable serverHandle;
    private string listeningAddress;

    public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
    {
        this.startup = startup;
        this.appRoot = appRoot;
    }

    ...

```

### Initialize

여기에는 웹 서버 URL을 설정합니다. 이 작업을 수행하려면 몇 가지 정보가 필요합니다.

 + **URL 경로 접두사**. 선택 사항이지만 응용 프로그램의 여러 웹 서비스를 안전하게 호스팅할 수 있도록 이를 설정하는 것이 좋습니다.
 + **포트**.

웹 서버에 대한 포트를 잡기 전에 서비스 패브릭에서 응용 프로그램과 응용 프로그램이 실행되는 기본 운영 체제 사이에서 버퍼 역할을 하는 응용 프로그램 계층을 제공한다는 사실을 이해하는 것이 중요합니다. 이와 같이 서비스 패브릭은 서비스의 *끝점*을 구성하는 방법을 제공합니다. 서비스가 끝점을 사용할 수 있도록 서비스 패브릭이 관리하므로 사용자가 기본 OS 환경에 직접 구성할 필요가 없습니다. 따라서 응용 프로그램을 변경하지 않고도 다양한 환경에서 손쉽게 서비스 패브릭을 호스팅할 수 있습니다(예를 들어 Azure 또는 자체 데이터 센터에서 동일한 응용 프로그램을 호스팅할 수 있음).

PackageRoot\\ServiceManifest.xml에 HTTP 끝점을 구성합니다.

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="80" />
    </Endpoints>
</Resources>

```

서비스 호스트 프로세스가 제한된 자격 증명(Windows의 네트워크 서비스)에서 실행되므로 서비스에 자체적으로 HTTP 끝점을 설정할 수 있는 액세스 권한이 없기 때문에 이 단계가 중요합니다. 끝점 구성을 사용하면 서비스 패브릭에서 서비스가 수신 대기할 URL의 적절한 ACL을 설정하는 방법을 알고, 끝점을 구성할 표준 위치를 제공합니다.

다시 OwinCommunicationListener.cs에서 Initialize 메서드의 끝점 정보를 가져와 포트를 가져옵니다. 서비스가 수신 대기할 URL을 만들고 앞에서 만든 클래스 멤버 변수에 저장합니다. 이는 OpenAsync에서 웹 서버를 시작하는 데 사용됩니다.

```csharp

public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
{
    EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = String.Format(
        CultureInfo.InvariantCulture,
        "http://+:{0}/{1}",
        port,
        String.IsNullOrWhiteSpace(this.appRoot)
            ? String.Empty
            : this.appRoot.TrimEnd('/') + '/');
}

```

여기에 "http://+"가 사용된 점에 주목하시기 바랍니다. 이는 웹 서버가 localhost, FQDN 및 컴퓨터 IP를 비롯한 사용 가능한 모든 주소에서 수신 대기하도록 합니다.

### OpenAsync

OpenAsync는 초기화 후 플랫폼에 의해 호출됩니다. 여기에서는 Initialize에서 웹 서버를 열기 위해 만든 주소를 사용합니다.

OpenAsync를 구현하는 것은 웹 서버(또는 모든 통신 스택)를 서비스의 RunAsync()에서 바로 여는 대신 ICommunicationListener로 구현하는 가장 중요한 이유 중 하나입니다. OpenAsync의 반환 값은 웹 서버에서 수신 대기 중인 주소입니다. 이 주소가 시스템에 반환되면 서비스에 주소가 등록됩니다. 서비스 패브릭은 클라이언트 또는 다른 서비스가 서비스 이름별로 이 주소를 요청할 수 있게 해주는 API를 제공합니다. 서비스가 리소스 균형 조정 및 가용성 목적을 위해 클러스터 전체를 이동하므로 서비스 주소는 정적 주소가 아니기 때문에 이는 중요합니다. 이는 클라이언트가 서비스의 수신 대기 주소를 확인할 수 있도록 하는 메커니즘입니다.

OpenAsync는 이를 염두에 두고 웹 서버를 시작하고 수신 대기 중인 주소를 반환합니다. "http://+"에서 수신 대기하고 있지만 주소를 반환하기 전에 "+"가 현재 있는 노드의 IP 또는 FQDN으로 바뀐다는 사실에 주목해야 합니다. 그 이유는 메서드에 의해 반환되는 이 주소가 시스템에 등록된 주소이고, 클라이언트 및 다른 서비스가 서비스의 주소를 요청할 때 보게 될 주소이기 때문입니다. 클라이언트가 이 주소에 올바르게 연결하려면 주소에 실제 IP 또는 FQDN이 필요합니다.

```csharp

public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

    string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

    return Task.FromResult(resultAddress);
}

```

이는 생성자의 OwinCommunicationListener에 전달된 **Startup** 클래스를 참조합니다. 이 Startup 인스턴스는 웹 서버에서 Web API 응용 프로그램을 부트스트랩하는 데 사용됩니다.

나중에 응용 프로그램을 실행하면 진단 이벤트 창에 웹 서버가 성공적으로 시작되었음을 알려주는 ServiceEventSource.Current.Message() 줄이 표시됩니다.

### CloseAsync 및 Abort

마지막으로, 웹 서버를 중지하는 CloseAsync 및 Abort를 구현합니다. OpenAsync 중에 만들어진 서버 핸들을 삭제하여 웹 서버를 중지할 수 있습니다.

```csharp

public Task CloseAsync(CancellationToken cancellationToken)
{
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.serverHandle != null)
    {
        try
        {
            this.serverHandle.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}

```

이 구현 예에서는 CloseAsync와 Abort가 웹 서버를 간단히 중지합니다. CloseAsync에서 더욱 원활하게 조정된 웹 서버 종료를 수행하도록 선택할 수 있습니다(예를 반환하기 전에 진행 중인 요청이 완료될 때까지 대기).

## 웹 서버 시작

이제 만들고 웹 서버를 시작하는 OwinCommunicationListener의 인스턴스를 만들고 반환할 준비가 되었습니다. 다시 Service 클래스(Service.cs)에서 **CreateCommunicationListener()** 메서드를 재정의합니다.

```csharp

protected override ICommunicationListener CreateCommunicationListener()
{
	return new OwinCommunicationListener("api", new Startup());
}

```

이는 Web API *응용 프로그램* 및 OWIN *호스트*가 마침내 만나는 곳입니다. *호스트*(\*\*OwinCommunicationListener\*\*)에는 (**Startup**을 통해 Web API) *응용 프로그램* 인스턴스가 지정되고, 서비스 패브릭이 수명 주기를 관리합니다. 일반적으로 모든 통신 스택 뒤에 이와 동일한 패턴이 올 수 있습니다.

## 모든 항목 요약

이 예제에서는 재정의를 간단히 제거할 수 있도록 RunAsync() 메서드에서 어떤 작업도 수행할 필요가 없습니다.

통신 수신기만 만들면 되므로 최종 Service 구현이 매우 간단합니다.

```csharp

namespace WebApi
{
    using Microsoft.ServiceFabric.Services;

    public class Service : StatelessService
    {
        public const string ServiceTypeName = "WebApiType";

        protected override ICommunicationListener CreateCommunicationListener()
        {
            return new OwinCommunicationListener("api", new Startup());
        }
    }
}

```

그리고 완전한 OwinCommunicationListener 클래스는 다음과 같습니다.

```csharp

namespace WebApi
{
    using System;
    using System.Fabric;
    using System.Fabric.Description;
    using System.Globalization;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Owin.Hosting;
    using Microsoft.ServiceFabric.Services;

    public class OwinCommunicationListener : ICommunicationListener
    {
        private readonly IOwinAppBuilder startup;
        private readonly string appRoot;
        private IDisposable serverHandle;
        private string listeningAddress;

        public OwinCommunicationListener(string appRoot, IOwinAppBuilder startup)
        {
            this.startup = startup;
            this.appRoot = appRoot;
        }

        public void Initialize(ServiceInitializationParameters serviceInitializationParameters)
        {
            EndpointResourceDescription serviceEndpoint = serviceInitializationParameters.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
            int port = serviceEndpoint.Port;

            this.listeningAddress = String.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/{1}",
                port,
                String.IsNullOrWhiteSpace(this.appRoot)
                    ? String.Empty
                    : this.appRoot.TrimEnd('/') + '/');
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            this.serverHandle = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Configuration(appBuilder));

            string resultAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            ServiceEventSource.Current.Message("Listening on {0}", resultAddress);

            return Task.FromResult(resultAddress);
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.serverHandle != null)
            {
                try
                {
                    this.serverHandle.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}

```

모든 항목이 결합되면 이제 프로젝트가 신뢰할 수 있는 서비스 API 진입점과 OWIN 호스트가 있는 일반적인 Web API 응용 프로그램과 유사할 것입니다.


![](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## 웹 브라우저를 통한 실행 및 연결

아직 하지 않았다면 [개발 환경을 설정](service-fabric-get-started.md)합니다.


이제 서비스를 빌드하고 배포할 수 있습니다. Visual Studio에서 **F5** 키를 눌러 응용 프로그램을 빌드하고 배포합니다. 진단 이벤트 창에 웹 서버가 **http://localhost:80/api**에 열렸음을 나타내는 메시지가 표시됩니다.


![](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE]컴퓨터의 다른 프로세스에서 포트를 이미 연 경우 여기에 수신기를 열지 못했음을 나타내는 오류가 표시될 수 있습니다. 이러한 경우 ServiceManifest.xml에서 끝점 구성에 다른 포트를 사용해 보세요.


서비스가 실행되면 브라우저를 열고 [http://localhost/api](http://localhost/api)로 이동하여 테스트합니다.

## 확장

상태 비저장 웹앱을 확장하려면 일반적으로 더 많은 컴퓨터를 추가하고 해당 컴퓨터에서 웹앱을 구동합니다. 서비스 패브릭의 오케스트레이션 엔진은 클러스터에 새 노드가 추가될 때마다 이 작업을 자동으로 수행합니다. 상태 비저장 서비스의 인스턴스를 만들 때 만들려는 인스턴스 수를 지정할 수 있습니다. 서비스 패브릭은 클러스터의 노드에 해당 개수의 인스턴스를 적절히 배치하여 한 노드에 둘 이상의 인스턴스가 생성되지 않도록 합니다. 또한 인스턴스 수에 "-1"을 지정하여 서비스 패브릭이 모든 노드에서 항상 하나의 인스턴스만 만들도록 지시할 수 있습니다. 이렇게 하면 클러스터를 확장하기 위해 노드를 추가할 때마다 새 노드에 상태 비저장 서비스의 인스턴스가 생성됩니다. 이 값은 PowerShell을 통해 서비스 인스턴스를 만들 때 설정되므로 서비스 인스턴스의 속성입니다.

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

또는 Visual Studio 상태 비저장 서비스 프로젝트의 기본 서비스를 정의할 때 설정되는 경우는 다음과 같습니다.

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

응용 프로그램 및 서비스 인스턴스를 만드는 방법에 대한 자세한 내용은 [응용 프로그램을 배포 및 제거하는 방법](service-fabric-deploy-remove-applications.md)을 참조하세요.

## ASP.NET 5

ASP.NET 5에서 웹 응용 프로그램의 *호스트*와 *응용 프로그램*을 분리하는 개념 및 프로그래밍 모델은 동일합니다. 이는 다른 형태의 통신에도 적용할 수 있습니다. 또한 ASP.NET 5의 *호스트*는 다를 수 있지만 대량의 응용 프로그램 논리가 실제로 존재하는 Web API *응용 프로그램* 계층은 동일하게 유지됩니다.

## 다음 단계

[Visual Studio에서 서비스 패브릭 응용 프로그램 디버깅](service-fabric-debugging-your-application.md)

<!---HONumber=July15_HO5-->