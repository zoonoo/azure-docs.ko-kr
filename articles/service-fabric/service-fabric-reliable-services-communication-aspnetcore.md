---
title: ASP.NET 코어와의 서비스 통신
description: 상태 비수기 및 상태 Azure 서비스 패브릭 신뢰할 수 있는 서비스 응용 프로그램에서 ASP.NET Core를 사용하는 방법을 알아봅니다.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 0d432bd19d0689ef508fca0bf24eed4406929f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639635"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure 서비스 패브릭 신뢰할 수 있는 서비스의 핵심에 ASP.NET

ASP.NET 코어는 오픈 소스 및 크로스 플랫폼 프레임 워크입니다. 이 프레임워크는 웹 앱, IoT 앱 및 모바일 백 엔드와 같은 클라우드 기반의 인터넷 연결 응용 프로그램을 빌드하기 위해 설계되었습니다.

이 문서는 **Microsoft.ServiceFabric.AspNetCore를** 사용하여 서비스 패브릭 신뢰할 수 있는 서비스에서 ASP.NET 핵심 서비스를 호스팅하는 데 대한 심층적인 가이드입니다. NuGet 패키지 세트입니다.

서비스 패브릭의 ASP.NET Core에 대한 소개 자습서 및 개발 환경 설정에 대한 지침은 [자습서: ASP.NET Core Web API 프런트 엔드 서비스 및 상태 저장 백 엔드 서비스를 사용하여 응용 프로그램을 만들고 배포합니다.](service-fabric-tutorial-create-dotnet-app.md)

이 문서의 나머지 부분에서는 이미 코어에 익숙한 ASP.NET 가정합니다. 그렇지 않은 경우 [ASP.NET 핵심 기본 사항을](https://docs.microsoft.com/aspnet/core/fundamentals/index)읽어보시기 바랍니다.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 환경에서 ASP.NET Core

ASP.NET 코어 및 서비스 패브릭 앱은 모두 .NET Core 또는 full .NET 프레임워크에서 실행할 수 있습니다. 서비스 패브릭에서 두 가지 방법으로 ASP.NET 코어를 사용할 수 있습니다.
 - **게스트 실행 파일로 호스팅됨** - 이 방법은 주로 코드 변경 없이 서비스 패브릭에서 기존 ASP.NET 핵심 응용 프로그램을 실행하는 데 사용됩니다.
 - **신뢰할 수 있는 서비스 내에서 실행합니다.** 이렇게 하면 서비스 패브릭 런타임과 더 잘 통합되고 상태 ASP.NET 핵심 서비스가 허용됩니다.

이 문서의 나머지 부분에서는 서비스 패브릭 SDK와 함께 제공되는 ASP.NET 핵심 통합 구성 요소를 통해 신뢰할 수 있는 서비스 내에서 ASP.NET Core를 사용하는 방법을 설명합니다.

## <a name="service-fabric-service-hosting"></a>Service Fabric 서비스 호스팅

Service Fabric에서 서비스 *호스트 프로세스에서*서비스 실행 의 하나 이상의 인스턴스 및/또는 복제본: 서비스 코드를 실행 하는 실행 파일입니다. 서비스 작성자로서 서비스 호스트 프로세스를 소유하고 서비스 패브릭을 활성화하고 모니터링합니다.

기존 ASP.NET(MVC 5 이하)은 System.Web.dll을 통해 IIS에 긴밀하게 결합됩니다. ASP.NET Core는 웹 서버와 웹 애플리케이션 간의 분리를 제공합니다. 이러한 분리를 통해 웹 응용 프로그램을 서로 다른 웹 서버 간에 이식할 수 있습니다. 또한 웹 서버를 *자체 호스팅할*수 있습니다. 즉, IIS와 같은 전용 웹 서버 소프트웨어가 소유한 프로세스와 달리 자체 프로세스에서 웹 서버를 시작할 수 있습니다.

서비스 패브릭 서비스와 ASP.NET 게스트 실행 파일을 사용하거나 신뢰할 수 있는 서비스를 결합하려면 서비스 호스트 프로세스 내에서 ASP.NET 시작할 수 있어야 합니다. ASP.NET Core 자체 호스팅을 사용하면 이 작업을 수행할 수 있습니다.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>신뢰할 수 있는 서비스로 ASP.NET 코어 호스팅
일반적으로 자체 호스팅되는 ASP.NET Core 애플리케이션은 `Program.cs`의 `static void Main()` 메서드와 같이 애플리케이션의 진입점에 WebHost를 만듭니다. 이 경우 WebHost의 수명 주기는 프로세스의 수명 주기에 바인딩됩니다.

![프로세스에서 ASP.NET Core 호스팅][0]

그러나 응용 프로그램 진입점은 신뢰할 수 있는 서비스에서 WebHost를 만들 수 있는 올바른 장소가 아닙니다. 이는 응용 프로그램 진입점이 서비스 패브릭 런타임에 서비스 형식을 등록하는 데만 사용되므로 해당 서비스 형식의 인스턴스를 만들 수 있기 때문입니다. WebHost는 신뢰할 수 있는 서비스 자체로 만들어야 합니다. 서비스 호스트 프로세스 내에서 서비스 인스턴스 및/또는 복제본은 여러 수명 주기를 거치게 됩니다. 

Reliable Service 인스턴스는 `StatelessService` 또는 `StatefulService`에서 파생되는 서비스 클래스로 표현됩니다. 서비스를 위한 통신 스택은 서비스 클래스의 `ICommunicationListener` 구현에 포함되어 있습니다. `Microsoft.ServiceFabric.AspNetCore.*` NuGet 패키지에는 신뢰할 수 `ICommunicationListener` 있는 서비스에서 Kestrel 또는 HTTP.sys에 대한 ASP.NET 코어 WebHost를 시작하고 관리하는 구현이 포함되어 있습니다.

![신뢰할 수 있는 서비스에서 ASP.NET 코어를 호스팅하기 위한 다이어그램][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener` NuGet 패키지의 Kestrel 및 HTTP.sys구현은 비슷한 사용 패턴을 갖습니다. `Microsoft.ServiceFabric.AspNetCore.*` 그러나 각 웹 서버마다 약간 다른 작업을 수행합니다. 

두 통신 수신기는 다음 인수를 사용하는 생성자를 제공합니다.
 - **`ServiceContext serviceContext`**: 실행 `ServiceContext` 중인 서비스에 대한 정보가 포함된 개체입니다.
 - **`string endpointName`**: ServiceManifest.xml의 `Endpoint` 구성 이름입니다. 주로 두 통신 수신기가 다른 곳입니다. HTTP.sys는 `Endpoint` 구성이 *필요하지만* 케스트렐은 구성하지 않습니다.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: `IWebHost`을 만들고 반환하는 람다입니다. ASP.NET Core 응용 `IWebHost` 프로그램에서 일반적으로 원하는 방식으로 구성할 수 있습니다. lambda는 사용하는 서비스 패브릭 통합 옵션및 제공하는 구성에 따라 생성되는 `Endpoint` URL을 제공합니다. 그런 다음 해당 URL을 수정하거나 사용하여 웹 서버를 시작할 수 있습니다.

## <a name="service-fabric-integration-middleware"></a>Service Fabric 통합 미들웨어
NuGet 패키지에는 `UseServiceFabricIntegration` 서비스 패브릭 `IWebHostBuilder` 인식 미들웨어를 추가하는 확장 메서드가 포함되어 있습니다. `Microsoft.ServiceFabric.AspNetCore` 이 미들웨어는 Kestrel 또는 HTTP.sys를 `ICommunicationListener` 구성하여 서비스 패브릭 이름 지정 서비스에 고유한 서비스 URL을 등록합니다. 그런 다음 클라이언트 요청의 유효성을 검사하여 클라이언트가 올바른 서비스에 연결되도록 합니다. 

이 단계는 클라이언트가 잘못된 서비스에 실수로 연결되지 않도록 하는 데 필요합니다. 서비스 패브릭과 같은 공유 호스트 환경에서는 여러 웹 응용 프로그램이 동일한 물리적 또는 가상 컴퓨터에서 실행될 수 있지만 고유한 호스트 이름을 사용하지 않기 때문입니다. 이 시나리오에 대해서는 다음 섹션에서 자세히 설명합니다.

### <a name="a-case-of-mistaken-identity"></a>잘못된 ID의 경우
프로토콜에 관계없이 서비스 복제본은 고유한 IP:포트 조합에서 수신 대기합니다. 서비스 복제본이 IP:port 끝점에서 수신 대기를 시작하면 해당 끝점 주소를 서비스 패브릭 명명 서비스에 보고합니다. 클라이언트 또는 기타 서비스가 이를 검색할 수 있습니다. 서비스가 동적으로 할당된 응용 프로그램 포트를 사용하는 경우 서비스 복제본은 이전에 동일한 물리적 또는 가상 컴퓨터에서 다른 서비스의 동일한 IP:port 끝점을 동시에 사용할 수 있습니다. 이로 인해 클라이언트에서 실수로 잘못된 서비스에 연결할 수 있습니다. 이 시나리오는 다음과 같은 이벤트 시퀀스가 발생하는 경우 발생할 수 있습니다.

 1. 서비스 A가 HTTP를 통해 10.0.0.1:30000에서 수신 대기합니다. 
 2. 클라이언트는 서비스 A를 해결하고 주소 10.0.0.1:30000을 가져옵니다.
 3. 서비스 A가 다른 노드로 이동합니다.
 4. 서비스 B가 10.0.0.1에 위치하고 우연히 동일한 30000 포트를 사용합니다.
 5. 클라이언트가 캐시된 10.0.0.1:30000 주소로 서비스 A에 연결하려고 시도합니다.
 6. 클라이언트가 이제 잘못된 서비스에 연결되어 있다는 것을 깨닫지 못하고 서비스 B에 성공적으로 연결되었습니다.

이로 인해 진단할 수 없는 버그가 임의의 시간에 발생할 수 있습니다.

### <a name="using-unique-service-urls"></a>고유한 서비스 URL 사용
이러한 버그를 방지하기 위해 서비스는 고유한 식별자를 사용하여 명명 서비스에 끝점을 게시한 다음 클라이언트 요청 중에 해당 고유 식별자의 유효성을 검사할 수 있습니다. 이 작업은 적대적이지 않은 테넌트가 신뢰할 수 있는 환경에서 서비스 간의 공동 작업이며, 적대적인 테넌트 환경에서는 보안 서비스 인증을 제공하지 않습니다.

신뢰할 수 있는 환경에서 `UseServiceFabricIntegration` 메서드에 의해 추가되는 미들웨어는 명명 서비스에 게시된 주소에 고유 식별자를 자동으로 추가합니다. 각 요청에 대해 해당 식별자를 확인합니다. 식별자가 일치하지 않으면 미들웨어는 즉시 HTTP 410 Gone 응답을 반환합니다.

동적으로 할당된 포트를 사용하는 서비스는 이 미들웨어를 사용해야 합니다.

고정된 고유 포트를 사용하는 서비스는 협력 환경에서 이 문제가 없습니다. 고유한 고정 포트는 일반적으로 클라이언트 애플리케이션이 연결될 잘 알려진 포트가 필요한 외부 연결 서비스에 사용됩니다. 예를 들어 대부분의 인터넷 연결 웹 응용 프로그램은 웹 브라우저 연결에 포트 80 또는 443을 사용합니다. 이 경우 고유 식별자를 사용하도록 설정하면 안 됩니다.

다음 다이어그램에서는 미들웨어를 사용하도록 설정된 요청 흐름을 보여 줍니다.

![Service Fabric ASP.NET Core 통합][2]

Kestrel 및 HTTP.sys `ICommunicationListener` 구현 모두 정확히 동일한 방식으로 이 메커니즘을 사용합니다. HTTP.sys는 기본 **HTTP.sys** 포트 공유 기능을 사용하여 고유한 URL 경로를 기반으로 요청을 내부적으로 구분할 수 있지만 `ICommunicationListener` HTTP.sys 구현에서는 해당 기능을 사용하지 *않습니다.* 앞에서 설명한 시나리오에서 HTTP 503 및 HTTP 404 오류 상태 코드가 생성되기 때문입니다. 따라서 HTTP 503 및 HTTP 404가 일반적으로 다른 오류를 나타내는 데 사용되기 때문에 클라이언트가 오류의 의도를 파악하기가 어렵습니다. 

따라서 Kestrel 및 HTTP.sys `ICommunicationListener` 구현 모두 `UseServiceFabricIntegration` 확장 메서드에서 제공하는 미들웨어에서 표준화됩니다. 따라서 클라이언트는 HTTP 410 응답에서 서비스 끝점 다시 확인 작업을 수행하기만 하면 됩니다.

## <a name="httpsys-in-reliable-services"></a>신뢰할 수 있는 서비스의 HTTP.sys
**Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet 패키지를 가져와 신뢰할 수 있는 서비스에서 HTTP.sys를 사용할 수 있습니다. 이 패키지에는 의 `HttpSysCommunicationListener` `ICommunicationListener`구현이 포함됩니다. `HttpSysCommunicationListener`HTTP.sys를 웹 서버로 사용하여 신뢰할 수 있는 서비스 내에서 ASP.NET Core WebHost를 만들 수 있습니다.

HTTP.sys는 Windows [HTTP 서버 API에](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)빌드됩니다. 이 API는 **HTTP.s커널** 드라이버를 사용하여 HTTP 요청을 처리하고 웹 응용 프로그램을 실행하는 프로세스로 라우팅합니다. 이렇게 하면 동일한 물리적 또는 가상 컴퓨터의 여러 프로세스가 고유한 URL 경로 또는 호스트 이름으로 모호하게 처리된 동일한 포트에서 웹 응용 프로그램을 호스트할 수 있습니다. 이러한 기능은 동일한 클러스터에서 여러 웹 사이트를 호스팅하는 Service Fabric에서 유용합니다.

>[!NOTE]
>HTTP.sys 구현은 Windows 플랫폼에서만 작동합니다.

다음 다이어그램에서는 HTTP.sys가 Windows에서 포트 공유를 위해 **HTTP.sys** 커널 드라이버를 사용하는 방법을 보여 줍니다.

![HTTP.sys 다이어그램][3]

### <a name="httpsys-in-a-stateless-service"></a>상태 비수기 서비스의 HTTP.sys
상태 비저장 서비스에서 `HttpSys`를 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `HttpSysCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>상태 충실 서비스의 HTTP.sys

`HttpSysCommunicationListener`기본 **HTTP.sys** 포트 공유 기능의 복잡성으로 인해 상태 정보 서비스에서 사용하도록 현재 설계되지 않았습니다. 자세한 내용은 HTTP.sys를 통한 동적 포트 할당에 대한 다음 섹션을 참조하십시오. 상태 정보 서비스의 경우 Kestrel은 제안된 웹 서버입니다.

### <a name="endpoint-configuration"></a>엔드포인트 구성

HTTP.sys를 `Endpoint` 포함하여 Windows HTTP 서버 API를 사용하는 웹 서버에 는 구성이 필요합니다. Windows HTTP Server API를 사용하는 웹 서버는 먼저 HTTP.sys를 사용하여 URL을 예약해야 합니다(일반적으로 [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 도구를 사용하여 수행됩니다). 

이 작업을 수행하려면 서비스에 기본적으로 없는 높은 권한이 필요합니다. ServiceManifest.xml의 `Protocol` `Endpoint` 구성 속성에 대한 "http" 또는 "https" 옵션은 서비스 패브릭 런타임에 사용자 대신 HTTP.sys에 URL을 등록하도록 지시하는 데 특별히 사용됩니다. [*강력한 와일드카드*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL 접두사를 사용하여 이 작업을 수행합니다.

예를 들어 서비스를 `http://+:80` 예약하려면 ServiceManifest.xml에서 다음 구성을 사용합니다.

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

그리고 엔드포인트 이름은 `HttpSysCommunicationListener` 생성자에 전달해야 합니다.

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>정적 포트가 있는 HTTP.sys 사용
HTTP.sys와 정적 포트를 사용하려면 `Endpoint` 구성에서 포트 번호를 제공합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>동적 포트에서 HTTP.sys 사용
HTTP.sys를 사용하여 동적으로 할당된 포트를 `Port` 사용하려면 `Endpoint` 구성에서 속성을 생략합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

구성에 의해 할당된 동적 포트는 *호스트 프로세스당*하나의 포트만 제공합니다. `Endpoint` 현재 Service Fabric 호스팅 모델을 사용하면 여러 서비스 인스턴스 및/또는 복제본을 동일한 프로세스에서 호스팅할 수 있습니다. 즉, 구성을 통해 할당될 때 각 `Endpoint` 포트가 동일한 포트를 공유합니다. 여러 **HTTP.sys** 인스턴스는 기본 **HTTP.sys** 포트 공유 기능을 사용하여 포트를 공유할 수 있습니다. 그러나 클라이언트 요청에 대해 `HttpSysCommunicationListener` 도입되는 복잡성으로 인해 지원되지 않습니다. 동적 포트 사용을 위해 Kestrel은 제안된 웹 서버입니다.

## <a name="kestrel-in-reliable-services"></a>Reliable Services의 Kestrel
**Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 패키지를 가져와 신뢰할 수 있는 서비스에서 케스트렐을 사용할 수 있습니다. 이 패키지에는 의 `KestrelCommunicationListener` `ICommunicationListener`구현이 포함됩니다. `KestrelCommunicationListener`Kestrel을 웹 서버로 사용하여 신뢰할 수 있는 서비스 내에서 ASP.NET Core WebHost를 만들 수 있습니다.

Kestrel은 ASP.NET Core의 플랫폼 간 웹 서버입니다. HTTP.sys와 달리 Kestrel은 중앙 집중식 엔드포인트 관리자를 사용하지 않습니다. 또한 HTTP.sys와 달리 Kestrel은 여러 프로세스 간에 포트 공유를 지원하지 않습니다. Kestrel의 각 인스턴스는 고유한 포트를 사용해야 합니다. Kestrel에 대한 자세한 내용은 [구현 세부 정보를](https://docs.microsoft.com/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)참조하십시오.

![케스트렐 다이어그램][4]

### <a name="kestrel-in-a-stateless-service"></a>상태 비저장 서비스의 Kestrel
상태 비저장 서비스에서 `Kestrel`를 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>상태 저장 서비스의 Kestrel
상태 저장 서비스에서 `Kestrel`을 사용하려면 `CreateServiceReplicaListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

이 예제에서는 WebHost 종속성 삽입 컨테이너에 `IReliableStateManager`의 singleton 인스턴스를 제공합니다. 반드시 필요한 것은 아니지만 MVC 컨트롤러 `IReliableStateManager` 작업 메서드에서 신뢰할 수 있는 컬렉션을 사용할 수 있습니다.

상태 저장 서비스에서는 `Endpoint` 구성 이름이 `KestrelCommunicationListener`에 제공되지 *않습니다*. 자세한 내용은 다음 섹션에서 설명합니다.

### <a name="configure-kestrel-to-use-https"></a>HTTPS를 사용하도록 Kestrel 구성
서비스에서 Kestrel을 사용하여 HTTPS를 사용하도록 설정하면 몇 가지 듣기 옵션을 설정해야 합니다. `ServiceInstanceListener` *EndpointHttps* 끝점을 사용하도록 업데이트하고 특정 포트(예: 포트 443)에서 수신을 청취합니다. Kestrel 웹 서버를 사용하도록 웹 호스트를 구성할 때 는 모든 네트워크 인터페이스에서 IPv6 주소를 수신하도록 Kestrel을 구성해야 합니다. 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

자습서의 전체 예제는 [HTTPS를 사용하도록 Kestrel 구성을](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)참조하십시오.


### <a name="endpoint-configuration"></a>엔드포인트 구성
케스트렐을 `Endpoint` 사용하기 위해 구성이 필요하지 않습니다. 

Kestrel은 간단한 독립 실행형 웹 서버입니다. HTTP.sys(또는 HttpListener)와 달리 시작하기 `Endpoint` 전에 URL 등록이 필요하지 않기 때문에 ServiceManifest.xml의 구성이 필요하지 않습니다. 

#### <a name="use-kestrel-with-a-static-port"></a>정적 포트로 Kestrel 사용
Kestrel과 함께 사용할 `Endpoint` ServiceManifest.xml 의 구성에서 정적 포트를 구성할 수 있습니다. 이것은 반드시 필요한 것은 아니지만 두 가지 잠재적인 이점을 제공합니다.
 - 포트가 응용 프로그램 포트 범위에 속하지 않으면 서비스 패브릭에 의해 OS 방화벽을 통해 열립니다.
 - `KestrelCommunicationListener`를 통해 제공된 URL은 이 포트를 사용합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

`Endpoint`가 구성되면 해당 이름을 `KestrelCommunicationListener` 생성자로 전달해야 합니다. 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

ServiceManifest.xml이 구성을 `Endpoint` 사용하지 않는 경우 `KestrelCommunicationListener` 생성자에서 이름을 생략합니다. 이 경우 동적 포트를 사용합니다. 이에 대한 자세한 내용은 다음 섹션을 참조하십시오.

#### <a name="use-kestrel-with-a-dynamic-port"></a>동적 포트로 Kestrel 사용
Kestrel은 ServiceManifest.xml의 구성에서 자동 포트 할당을 `Endpoint` 사용할 수 없습니다. `Endpoint` 구성에서 자동 포트 할당호스트 *프로세스당*고유한 포트를 할당하고 단일 호스트 프로세스에 여러 Kestrel 인스턴스가 포함될 수 있기 때문입니다. 포트 공유를 지원하지 않으므로 Kestrel에서 작동하지 않습니다. 따라서 각 Kestrel 인스턴스는 고유한 포트에서 열어야 합니다.

Kestrel에서 동적 포트 할당을 사용하려면 ServiceManifest.xml의 `Endpoint` 구성을 완전히 생략하고 다음과 `KestrelCommunicationListener` 같이 끝점 이름을 생성자에게 전달하지 않습니다.

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

이 구성에서 `KestrelCommunicationListener`는 애플리케이션 포트 범위에서 사용되지 않는 포트를 자동으로 선택합니다.

HTTPS의 경우 ServiceManifest.xml에 지정된 포트 없이 HTTPS 프로토콜로 구성된 끝점을 가지고 있어야 하며 끝점 이름을 KestrelCommunicationListener 생성자에게 전달해야 합니다.


## <a name="service-fabric-configuration-provider"></a>서비스 패브릭 구성 공급자
ASP.NET 코어의 앱 구성은 구성 공급자가 설정한 키-값 쌍을 기반으로 합니다. ASP.NET [코어의 구성을](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) 읽고 일반 ASP.NET 코어 구성 지원에 대해 자세히 알아보십시오.

이 섹션에서는 서비스 패브릭 구성 공급자가 NuGet 패키지를 가져와 `Microsoft.ServiceFabric.AspNetCore.Configuration` ASP.NET 핵심 구성과 통합하는 방법에 대해 설명합니다.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabric구성 시작 확장
`Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 패키지를 가져온 후에는 ASP.NET 코어 구성 API를 사용하여 서비스 패브릭 구성 소스를 등록해야 합니다. `Microsoft.ServiceFabric.AspNetCore.Configuration` 에 대해 `IConfigurationBuilder`네임스페이스에서 **AddServiceFabricConfiguration** 확장을 확인하여 이 작업을 수행합니다.

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

이제 ASP.NET Core 서비스는 다른 응용 프로그램 설정과 마찬가지로 서비스 패브릭 구성 설정에 액세스할 수 있습니다. 예를 들어 옵션 패턴을 사용하여 설정을 강력한 형식의 객체에 로드할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>기본 키 매핑
기본적으로 Service Fabric 구성 공급자에는 패키지 이름, 섹션 이름 및 속성 이름이 포함됩니다. 이들은 다음과 같이 ASP.NET 코어 구성 키를 형성합니다.
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

예를 들어 다음 콘텐츠로 명명된 `MyConfigPackage` 구성 패키지가 있는 경우 `IConfiguration` *MyConfigPackage:MyConfigSection:MyParameter*를 통해 ASP.NET 코어에서 구성 값을 사용할 수 있습니다.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>서비스 패브릭 구성 옵션
서비스 패브릭 구성 공급자는 키 매핑의 기본 동작을 변경할 수도 있습니다. `ServiceFabricConfigurationOptions`

#### <a name="encrypted-settings"></a>암호화된 설정
서비스 패브릭은 서비스 패브릭 구성 공급자와 마찬가지로 암호화된 설정을 지원합니다. 암호화된 설정은 기본적으로 ASP.NET 코어로 `IConfiguration` 해독되지 않습니다. 암호화된 값은 대신 저장됩니다. 그러나 ASP.NET 코어 IConfiguration에 저장할 값을 해독하려면 다음과 같이 확장에서 *DecryptValue* 플래그를 `AddServiceFabricConfiguration` false로 설정할 수 있습니다.

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>여러 구성 패키지
서비스 패브릭은 여러 구성 패키지를 지원합니다. 기본적으로 패키지 이름은 구성 키에 포함됩니다. 그러나 다음과 같이 `IncludePackageName` 플래그를 false로 설정할 수 있습니다.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>사용자 지정 키 매핑, 값 추출 및 데이터 채우기
또한 Service Fabric 구성 공급자는 `ExtractKeyFunc` `ExtractValueFunc`에서 값을 사용자 지정하고 값을 사용자 지정하는 고급 시나리오를 지원합니다. 을 사용하여 `ConfigAction`서비스 패브릭 구성에서 ASP.NET 코어 구성으로 데이터를 채우는 전체 프로세스를 변경할 수도 있습니다.

다음 예제에서는 데이터 채우기를 사용자 지정하는 데 사용하는 `ConfigAction` 방법을 보여 줍니다.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>구성 업데이트
서비스 패브릭 구성 공급자도 구성 업데이트를 지원합니다. ASP.NET Core를 `IOptionsMonitor` 사용하여 변경 알림을 받은 `IOptionsSnapshot` 다음 구성 데이터를 다시 로드하는 데 사용할 수 있습니다. 자세한 내용은 [ASP.NET 핵심 옵션을](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)참조하십시오.

이러한 옵션은 기본적으로 지원됩니다. 구성 업데이트를 활성화하기 위해 더 이상 코딩할 필요가 없습니다.

## <a name="scenarios-and-configurations"></a>시나리오 및 구성
이 섹션에서는 웹 서버, 포트 구성, 서비스 패브릭 통합 옵션 및 다음과 같은 시나리오를 해결하는 데 권장되는 기타 설정의 조합을 제공합니다.
 - 외부에 노출된 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET 핵심 상태 비수기 서비스
 - 내부 전용 ASP.NET 핵심 상태 관리 서비스

**외부에서 노출된 서비스는** 일반적으로 로드 밸런서를 통해 클러스터 외부에서 호출되는 끝점을 노출하는 서비스입니다.

**내부 전용** 서비스는 클러스터 내에서만 끝점이 호출되는 서비스입니다.

> [!NOTE]
> 상태 풀 서비스 끝점은 일반적으로 인터넷에 노출되어서는 안됩니다. Azure Load Balancer와 같은 서비스 패브릭 서비스 확인을 인식하지 못하는 로드 밸런서 뒤에 있는 클러스터는 상태 공개 서비스를 노출할 수 없습니다. 로드 밸로드커가 트래픽을 찾아 적절한 상태 풀 서비스 복제본으로 라우팅할 수 없기 때문입니다. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>외부에 노출된 ASP.NET Core 상태 비저장 서비스
Kestrel은 인터넷과 마주보고 있는 외부 HTTP 엔드포인트를 노출하는 프런트 엔드 서비스를 위한 권장 웹 서버입니다. Windows에서 HTTP.sys는 동일한 포트를 사용하여 동일한 노드 집합에서 여러 웹 서비스를 호스트할 수 있는 포트 공유 기능을 제공할 수 있습니다. 이 시나리오에서 웹 서비스는 HTTP 라우팅을 제공하기 위해 프런트 엔드 프록시 또는 게이트웨이에 의존하지 않고 호스트 이름 또는 경로로 구분됩니다.
 
인터넷에 노출되면 상태 비수기 서비스는 로드 밸러커를 통해 연결할 수 있는 잘 알려진 안정적인 끝점을 사용해야 합니다. 이 URL은 응용 프로그램의 사용자에게 제공합니다. 다음 구성이 권장됩니다.

|  |  | **참고** |
| --- | --- | --- |
| 웹 서버 | Kestrel | Kestrel은 Windows 및 Linux에서 지원되기 때문에 선호되는 웹 서버입니다. |
| 포트 구성 | static | 잘 알려진 정적 포트는 ServiceManifest.xml의 `Endpoints` 구성에 구성해야 합니다(예: HTTP의 경우 80, HTTPS의 경우 443). |
| ServiceFabricIntegrationOptions | None | 서비스가 `ServiceFabricIntegrationOptions.None` 고유 식별자에 대한 들어오는 요청의 유효성을 검사하지 않도록 서비스 패브릭 통합 미들웨어를 구성할 때 이 옵션을 사용합니다. 응용 프로그램의 외부 사용자는 미들웨어에서 사용하는 고유한 식별 정보를 알지 못합니다. |
| 인스턴스 수 | -1 | 일반적인 사용 사례에서 인스턴스 수 설정은 *-1로*설정해야 합니다. 이 작업은 로드 밸런서에서 트래픽을 받는 모든 노드에서 인스턴스를 사용할 수 있도록 수행됩니다. |

외부에 노출된 여러 서비스가 동일한 노드 집합을 공유하는 경우 고유하지만 안정적인 URL 경로로 HTTP.sys를 사용할 수 있습니다. IWebHost를 구성할 때 제공된 URL을 수정하여 이 작업을 수행할 수 있습니다. 이는 HTTP.sys에만 적용됩니다.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 비저장 서비스
클러스터 내에서만 호출되는 상태 비저장 서비스는 고유한 URL과 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 다음 구성이 권장됩니다.

|  |  | **참고** |
| --- | --- | --- |
| 웹 서버 | Kestrel | 내부 상태 비관리 서비스에 HTTP.sys를 사용할 수 있지만 Kestrel은 여러 서비스 인스턴스가 호스트를 공유할 수 있도록 하는 가장 좋은 서버입니다.  |
| 포트 구성 | 동적으로 할당 | 상태 관리 서비스의 여러 복제본은 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |
| InstanceCount | any | 인스턴스 수 설정은 서비스를 작동하는 데 필요한 모든 값으로 설정할 수 있습니다. |

### <a name="internal-only-stateful-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 저장 서비스
클러스터 내에서만 호출되는 상태 저장 서비스는 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 다음 구성이 권장됩니다.

|  |  | **참고** |
| --- | --- | --- |
| 웹 서버 | Kestrel | 복제본이 `HttpSysCommunicationListener` 호스트 프로세스를 공유하는 상태 풀 서비스에서 사용하도록 설계되지 않았습니다. |
| 포트 구성 | 동적으로 할당 | 상태 관리 서비스의 여러 복제본은 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 Service Fabric 애플리케이션 디버그](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
