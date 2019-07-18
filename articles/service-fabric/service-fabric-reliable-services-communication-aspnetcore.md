---
title: ASP.NET Core와 서비스 통신 | Microsoft Docs
description: 상태 비저장 및 상태 저장 Reliable Services에서 ASP.NET Core를 사용하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 638c06e1854504dcb7ff34b1d9df56694556c421
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939790"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric Reliable Services의 ASP.NET Core

ASP.NET Core는 오픈 소스 이며 플랫폼 간 프레임 워크입니다. 이 프레임 워크는 웹 앱과 IoT 앱 같은 클라우드 기반, 인터넷에 연결 된 응용 프로그램을 빌드하기 위한 및 모바일 백 엔드입니다.

이 문서는 사용 하 여 Service Fabric Reliable Services의 ASP.NET Core 서비스 호스팅에 대 한 심층 가이드를을 **Microsoft.ServiceFabric.AspNetCore.** NuGet 패키지의 집합입니다.

Service Fabric에서 ASP.NET Core의 소개 자습서 및 개발 환경 설정 가져오기에 대 한 지침을 참조 하세요. [자습서: ASP.NET Core Web API 프런트 엔드 서비스 및 상태 저장 백 엔드 서비스 응용 프로그램을 만들어](service-fabric-tutorial-create-dotnet-app.md)합니다.

이 문서의 나머지 부분에서는 ASP.NET Core를 사용 하 여 이미 친숙 하는 것으로 가정 합니다. 그렇지 않은 경우까지 읽어보세요 합니다 [ASP.NET Core 기본 사항](https://docs.microsoft.com/aspnet/core/fundamentals/index)합니다.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 환경에서 ASP.NET Core

Service Fabric 및 ASP.NET Core 앱은.NET Core 또는 전체.NET Framework에서 실행할 수 있습니다. 두 가지 방법으로 Service Fabric에서 ASP.NET Core를 사용할 수 있습니다.
 - **게스트 실행 파일로 호스팅됨** - 이러한 방식으로 코드 변경 없이 Service Fabric에서 기존 ASP.NET Core 응용 프로그램을 실행 하려면 주로 사용 됩니다.
 - **Reliable service 내에서 실행**합니다. 이러한 방식으로 Service Fabric 런타임 사용 하 여 더 나은 통합을 상태 저장 ASP.NET Core 서비스 수 있도록 합니다.

이 문서의 나머지 부분에서는 Service Fabric SDK와 함께 제공 되는 ASP.NET Core 통합 구성 요소를 통해 reliable service 내에서 ASP.NET Core를 사용 하는 방법에 설명 합니다.

## <a name="service-fabric-service-hosting"></a>Service Fabric 서비스 호스팅

Service fabric에서 하나 이상의 인스턴스 및/또는 서비스의 복제본에서 실행 된 *서비스 호스트 프로세스*: 서비스 코드를 실행 하는 실행 파일입니다. 서비스 작성자는 자신이 서비스 호스트 프로세스 및 서비스 패브릭 활성화를 모니터링 합니다.

기존 ASP.NET(MVC 5 이하)은 System.Web.dll을 통해 IIS에 긴밀하게 결합됩니다. ASP.NET Core는 웹 서버와 웹 애플리케이션 간의 분리를 제공합니다. 웹 응용 프로그램을을 다른 웹 서버 간에 이동할 수 있습니다. 웹 서버의 수 있습니다 *자체 호스팅*합니다. 즉, IIS와 같은 전용된 웹 서버 소프트웨어에서 소유한 프로세스가 아니라 자신의 프로세스에서 웹 서버를 시작할 수 있습니다.

Service Fabric 서비스 및 게스트 실행 파일로 또는 reliable service에서 ASP.NET과 결합 하 여 서비스 호스트 프로세스 내에서 ASP.NET을 시작할 수 없게 해야 합니다. ASP.NET Core 자체 호스팅을 사용하면 이 작업을 수행할 수 있습니다.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Reliable service에서 ASP.NET Core 호스팅
일반적으로 자체 호스팅되는 ASP.NET Core 애플리케이션은 `Program.cs`의 `static void Main()` 메서드와 같이 애플리케이션의 진입점에 WebHost를 만듭니다. 이 경우 WebHost의 수명 주기 프로세스의 수명 주기와 바인딩됩니다.

![프로세스에서 ASP.NET Core 호스팅][0]

하지만 응용 프로그램 진입점에 적절 한 위치는 reliable service에서 WebHost를 만들 수 없습니다. 응용 프로그램 진입점만 되는 서비스 패브릭 런타임에서 서비스 유형 등록 해당 서비스 유형의 인스턴스를 만들 수 있도록 때문입니다. WebHost는 reliable service 자체에서 만들어야 합니다. 서비스 호스트 프로세스 내에서 여러 수명 주기를 통해 서비스 인스턴스 및/또는 복제본 이동 수 있습니다. 

Reliable Service 인스턴스는 `StatelessService` 또는 `StatefulService`에서 파생되는 서비스 클래스로 표현됩니다. 서비스를 위한 통신 스택은 서비스 클래스의 `ICommunicationListener` 구현에 포함되어 있습니다. 합니다 `Microsoft.ServiceFabric.AspNetCore.*` NuGet 패키지에는 구현이 포함 되어 `ICommunicationListener` 시작 하 고는 reliable service의 Kestrel 또는 HTTP.sys에 대 한 ASP.NET Core WebHost를 관리 합니다.

![Reliable service에서 ASP.NET Core 호스팅에 대 한 다이어그램][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
합니다 `ICommunicationListener` Kestrel 및 HTTP.sys에 대 한 구현을 `Microsoft.ServiceFabric.AspNetCore.*` NuGet 패키지만 비슷한 사용 패턴입니다. 하지만 각 웹 서버에 따라 약간 다른 작업을 수행 합니다. 

두 통신 수신기는 다음 인수를 사용하는 생성자를 제공합니다.
 - **`ServiceContext serviceContext`** : 이 `ServiceContext` 실행 중인 서비스에 대 한 정보를 포함 하는 개체입니다.
 - **`string endpointName`** : 이름임을 `Endpoint` ServiceManifest.xml에서 구성 합니다. 두 통신 수신기는 다를 주로 됩니다. HTTP.sys *필요* 는 `Endpoint` Kestrel 하지 하는 동안 구성 합니다.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : 이 만들고 반환에 구현 하는 람다는 `IWebHost`합니다. 구성할 수 있습니다 `IWebHost` ASP.NET Core 응용 프로그램에서 일반적으로 하는 방법은 합니다. 생성 되는 URL을 제공 하는 람다를 사용 하 여 Service Fabric 통합 옵션에 따라 및 `Endpoint` 제공한 구성 합니다. 수정 하거나 해당 URL을 사용 하 여 웹 서버를 시작할 수 있습니다.

## <a name="service-fabric-integration-middleware"></a>Service Fabric 통합 미들웨어
합니다 `Microsoft.ServiceFabric.AspNetCore` NuGet 패키지에 포함 된 `UseServiceFabricIntegration` 확장 메서드를 `IWebHostBuilder` Service Fabric 인식 미들웨어를 추가 하는 합니다. 이 미들웨어는 Kestrel 또는 HTTP.sys 구성 `ICommunicationListener` Service Fabric 명명 서비스를 사용 하 여 고유한 서비스 URL을 등록 합니다. 그런 다음 클라이언트는 적절 한 서비스에 연결 되도록 클라이언트 요청을 확인 합니다. 

이 단계는 클라이언트가 실수로 잘못 된 서비스에 연결 하는 것을 방지 해야 합니다. Service Fabric 같은 공유 호스트 환경에서 여러 웹 응용 프로그램이 동일한 물리적 컴퓨터 또는 가상 컴퓨터에서 실행할 수 있지만 고유한 호스트 이름을 사용 하지 않는 때문입니다. 이 시나리오에 대해서는 다음 섹션에서 자세히 설명합니다.

### <a name="a-case-of-mistaken-identity"></a>잘못된 ID의 경우
프로토콜에 관계없이 서비스 복제본은 고유한 IP:포트 조합에서 수신 대기합니다. Ip: port 끝점에서 수신 대기 서비스 복제본이 시작 되 면 Service Fabric 명명 서비스에 끝점 주소를 보고 합니다. 여기에서 다른 서비스나 클라이언트가 검색할 수 있습니다. 서비스에서 동적으로 할당 된 응용 프로그램 포트를 사용 하는 경우 서비스 복제본 다른 동일한 물리적에서 이전에 서비스 또는 가상 컴퓨터의 동일한 ip: port 끝점 우연히 사용할 수 있습니다. 이로 인해 클라이언트에서 실수로 잘못된 서비스에 연결할 수 있습니다. 이 시나리오는 다음과 같은 순서의 이벤트가 발생 하는 경우에 발생할 수 있습니다.

 1. 서비스 A가 HTTP를 통해 10.0.0.1:30000에서 수신 대기합니다. 
 2. 클라이언트가는 서비스 A를 확인 하 고 10.0.0.1: 30000 주소를 가져옵니다.
 3. 서비스 A가 다른 노드로 이동합니다.
 4. 서비스 B가 10.0.0.1에 위치하고 우연히 동일한 30000 포트를 사용합니다.
 5. 클라이언트가 캐시된 10.0.0.1:30000 주소로 서비스 A에 연결하려고 시도합니다.
 6. 클라이언트가 서비스 B에 성공적으로 연결 되어 이제, 잘못 된 서비스에 연결 된 인식 하지 못하는 합니다.

이로 인해 진단할 수 없는 버그가 임의의 시간에 발생할 수 있습니다.

### <a name="using-unique-service-urls"></a>고유한 서비스 URL 사용
이러한 버그를 방지 하려면 서비스는 고유 식별자를 사용 하 여 명명 서비스에 끝점을 게시 하 고 클라이언트 요청 하는 동안 해당 고유 식별자를 유효성을 검사할 수 있습니다. 이 작업은 적대적이지 않은 테넌트가 신뢰할 수 있는 환경에서 서비스 간의 공동 작업이며, 적대적인 테 넌 트 환경에서는 보안 서비스 인증을 제공 하지 않습니다.

신뢰할 수 있는 환경에서 추가 되는 미들웨어에서는 `UseServiceFabricIntegration` 메서드 명명 서비스에 게시 하는 주소에 고유 식별자를 자동으로 추가 합니다. 각 요청에서 해당 식별자 유효성을 검사 합니다. 식별자 일치 하지 않는 경우 미들웨어는 즉시 HTTP 410 응답을 반환 합니다.

동적으로 할당된 포트를 사용하는 서비스는 이 미들웨어를 사용해야 합니다.

고유한 고정된 포트를 사용 하는 서비스 공동 작업 환경에서이 문제가 발생 하지 않습니다. 고유한 고정 포트는 일반적으로 클라이언트 애플리케이션이 연결될 잘 알려진 포트가 필요한 외부 연결 서비스에 사용됩니다. 예를 들어, 대부분의 인터넷 연결 웹 응용 프로그램 포트 80 또는 443을 사용 하 여 웹 브라우저 연결에 대 한 됩니다. 이 경우 고유 식별자를 활성화 해서는 안 됩니다.

다음 다이어그램에서는 미들웨어를 사용하도록 설정된 요청 흐름을 보여 줍니다.

![Service Fabric ASP.NET Core 통합][2]

Kestrel 및 HTTP.sys `ICommunicationListener` 구현을 동일한 방식으로이 메커니즘을 사용 합니다. HTTP.sys 기본을 사용 하 여 고유한 URL 경로에 따라 요청을 내부적으로 구분할 수 있지만 **HTTP.sys** 포트 공유 기능으로,이 기능은 *하지* HTTP.sys를사용한`ICommunicationListener`구현입니다. 앞에서 설명한 시나리오에서 HTTP 503 및 HTTP 404 오류 상태 코드가 발생 때문입니다. 다시 어렵습니다 오류를 확인 하는 클라이언트에 대 한 HTTP 503 및 HTTP 404는 일반적으로 사용 되 고 다른 오류를 나타냅니다. 

따라서 Kestrel 및 HTTP.sys `ICommunicationListener` 구현을 제공 되는 미들웨어에서 표준화 된 `UseServiceFabricIntegration` 확장 메서드. 따라서 클라이언트는만 HTTP 410 응답에서 서비스 끝점 재확인 작업을 수행 해야 합니다.

## <a name="httpsys-in-reliable-services"></a>Reliable Services의 HTTP.sys
가져와서 Reliable Services에서 HTTP.sys를 사용할 수 있습니다 합니다 **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet 패키지. 이 패키지 포함 `HttpSysCommunicationListener`, 구현의 `ICommunicationListener`합니다. `HttpSysCommunicationListener` 웹 서버 HTTP.sys를 사용 하 여 reliable service에서 ASP.NET Core WebHost를 만들 수 있습니다.

HTTP.sys에서 빌드되는 [Windows HTTP 서버 API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)합니다. 이 API에서 사용 하 여 **HTTP.sys** HTTP 요청을 처리 하 여 웹 응용 프로그램을 실행 하는 프로세스에 라우팅하는 커널 드라이버입니다. 이렇게 하면 여러 프로세스가 동일한 물리적 컴퓨터 또는 가상 컴퓨터 호스트 웹 응용 프로그램에 동일한 포트를 두는 고유한 URL 경로 또는 호스트 이름으로 명확 하 게 합니다. 이러한 기능은 동일한 클러스터에서 여러 웹 사이트를 호스팅하는 Service Fabric에서 유용합니다.

>[!NOTE]
>HTTP.sys 구현 Windows 플랫폼 에서만 작동합니다.

다음 다이어그램은 HTTP.sys를 사용 하는 방법을 보여 줍니다.는 **HTTP.sys** 포트 공유를 위해 Windows에서 커널 드라이버:

![HTTP.sys 다이어그램][3]

### <a name="httpsys-in-a-stateless-service"></a>상태 비저장 서비스에서는 HTTP.sys
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

### <a name="httpsys-in-a-stateful-service"></a>상태 저장 서비스에서는 HTTP.sys

`HttpSysCommunicationListener` 현재 기본 복잡성으로 인해 상태 저장 서비스의 사용에 맞게 디자인 되지 **HTTP.sys** 포트 공유 기능입니다. 자세한 내용은 HTTP.sys 사용 하 여 동적 포트 할당에 다음 섹션을 참조 하세요. 상태 저장 서비스의 경우 Kestrel은 제안 된 웹 서버를 사용 합니다.

### <a name="endpoint-configuration"></a>엔드포인트 구성

`Endpoint` 구성은 HTTP.sys를 포함 하 여 Windows HTTP 서버 API를 사용 하는 웹 서버에 필요 합니다. Windows HTTP Server API를 사용 하는 웹 서버 HTTP.sys 사용 하 여 해당 URL을 먼저 예약 해야 합니다 (사용 하 여 일반적으로 이렇게 합니다 [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 도구). 

이 작업에는 기본적으로 서비스에 없는 상승 된 권한이 필요 합니다. 에 대 한 "http" 또는 "https" 옵션을 합니다 `Protocol` 의 속성을 `Endpoint` ServiceManifest.xml에서 구성 하는 데 특히 귀하를 대신해 HTTP.sys를 사용 하 여 URL을 등록 하도록 Service Fabric 런타임에 지시 합니다. 이 사용 하 여 수행 합니다 [ *강력한 와일드 카드* ](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL 접두사입니다.

예를 들어 예약할 `http://+:80` 서비스의 ServiceManifest.xml에서 다음 구성을 사용 합니다.

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

#### <a name="use-httpsys-with-a-static-port"></a>정적 포트를 사용 하 여 HTTP.sys를 사용 합니다.
HTTP.sys를 사용 하 여 정적 포트를 사용 하려면 포트 번호를 제공 합니다 `Endpoint` 구성:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>동적 포트를 사용 하 여 HTTP.sys를 사용 합니다.
HTTP.sys를 사용 하 여 동적으로 할당 된 포트를 사용 하려면 생략 합니다 `Port` 속성에는 `Endpoint` 구성:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

동적 포트 할당을 `Endpoint` 구성은 하나의 포트만 제공 *호스트 프로세스당*합니다. 현재 Service Fabric 호스팅 모델에는 여러 서비스 인스턴스 및/또는 복제본을 동일한 프로세스에서 호스팅할 수 있습니다. 즉, 각각을 통해 할당 된 경우 동일한 포트 공유를 `Endpoint` 구성 합니다. 여러 **HTTP.sys** 인스턴스 내부를 사용 하 여 포트를 공유할 수 있습니다 **HTTP.sys** 포트 공유 기능입니다. 지원 되지 않습니다 하지만 `HttpSysCommunicationListener` 클라이언트 요청에 대 한 복잡성으로 인해 합니다. 동적 포트를 사용 Kestrel이 권장된 웹 서버입니다.

## <a name="kestrel-in-reliable-services"></a>Reliable Services의 Kestrel
가져와서 Reliable Services의 Kestrel을 사용 합니다 **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 패키지. 이 패키지 포함 `KestrelCommunicationListener`, 구현의 `ICommunicationListener`합니다. `KestrelCommunicationListener` 웹 서버로 Kestrel을 사용 하 여 reliable service에서 ASP.NET Core WebHost를 만들 수 있습니다.

Kestrel은 libuv(플랫폼 간 비동기 I/O 라이브러리)를 기반으로 하는 ASP.NET Core용 플랫폼 간 웹 서버입니다. HTTP.sys와 달리 Kestrel에는 중앙 집중식된 끝점 관리자를 사용 하지 않습니다. 또한 Kestrel는 HTTP.sys와 달리 여러 프로세스 간의 포트 공유를 지원 하지 않습니다. Kestrel의 각 인스턴스는 고유한 포트를 사용해야 합니다.

![Kestrel 다이어그램][4]

### <a name="kestrel-in-a-stateless-service"></a>상태 비저장 서비스의 Kestrel
상태 비저장 서비스에서 `Kestrel`을 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

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

이 예제에서는 WebHost 종속성 삽입 컨테이너에 `IReliableStateManager`의 singleton 인스턴스를 제공합니다. 이것이 반드시 필요 하지만 사용할 수 있습니다 `IReliableStateManager` 및 MVC 컨트롤러 작업 메서드에서 신뢰할 수 있는 컬렉션입니다.

상태 저장 서비스에서는 `Endpoint` 구성 이름이 `KestrelCommunicationListener`에 제공되지 *않습니다*. 자세한 내용은 다음 섹션에서 설명합니다.

### <a name="configure-kestrel-to-use-https"></a>HTTPS를 사용하도록 Kestrel 구성
서비스의 Kestrel 사용 하 여 HTTPS를 사용 하는 경우에 여러 수신 대기 옵션을 설정 하는 것이 해야 합니다. 업데이트를 `ServiceInstanceListener` 사용 하는 *EndpointHttps* 끝점과 (예: 포트 443)가 특정 포트에서 수신 대기 합니다. Kestrel 웹 서버를 사용 하도록 웹 호스트를 구성할 때 모든 네트워크 인터페이스에서 IPv6 주소에 대 한 수신 대기 하도록 Kestrel을 구성 해야 합니다. 

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

자습서의 전체 예제를 보려면 [HTTPS를 사용 하도록 Kestrel 구성](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)합니다.


### <a name="endpoint-configuration"></a>엔드포인트 구성
`Endpoint` 구성 Kestrel을 사용 하는 데 필요한 되지 않습니다. 

Kestrel은 간단한 독립 실행형 웹 서버입니다. HTTP.sys (또는 HttpListener)와 달리 필요 하지 않습니다는 `Endpoint` ServiceManifest.xml에서 구성을 시작 하기 전에 URL을 등록할 필요가 없기 때문에 있습니다. 

#### <a name="use-kestrel-with-a-static-port"></a>정적 포트로 Kestrel 사용
정적 포트를 구성할 수는 `Endpoint` Kestrel 사용 하려면 ServiceManifest.xml의 구성 합니다. 이것이 반드시 필요 하지만 잠재적인 두 가지 이점을 제공 합니다.
 - 포트는 응용 프로그램 포트 범위에도 속하지 않습니다, 경우 열릴 OS 방화벽을 통해 Service Fabric에서.
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

ServiceManifest.xml 사용 하지 않는 경우는 `Endpoint` 구성에서 이름을 생략 합니다 `KestrelCommunicationListener` 생성자입니다. 이 경우에 동적 포트가 사용 됩니다. 이 대 한 자세한 내용은 다음 섹션을 참조 하세요.

#### <a name="use-kestrel-with-a-dynamic-port"></a>동적 포트로 Kestrel 사용
Kestrel에서 자동 포트 할당을 사용할 수 없습니다는 `Endpoint` ServiceManifest.xml에서 구성 합니다. 있기 때문입니다 자동 할당 포트를 `Endpoint` 당 고유한 포트를 할당 *호스트 프로세스*, 단일 호스트 프로세스에 여러 Kestrel 인스턴스가 포함 될 수 있습니다. 포트 공유를 지원 하지 않으므로 Kestrel에서는 작동 하지 않습니다. 따라서 각 Kestrel 인스턴스는 고유한 포트에서 열어야 합니다.

Kestrel을 사용 하 여 동적 포트 할당을 사용 하려면 생략를 `Endpoint` ServiceManifest.xml에서 구성을 완전히에 끝점 이름을 전달 하지는 `KestrelCommunicationListener` 같이 생성자:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

이 구성에서 `KestrelCommunicationListener`는 애플리케이션 포트 범위에서 사용되지 않는 포트를 자동으로 선택합니다.

## <a name="service-fabric-configuration-provider"></a>Service Fabric 구성 공급자
ASP.NET Core에서 앱 구성 설정의 구성 공급자에서 키-값 쌍을 기반으로 합니다. 읽기 [ASP.NET Core의 구성](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/) 자세한 온 일반적인 ASP.NET Core 구성 지원 알아야 합니다.

이 섹션에서는 Service Fabric 구성 공급자를 ASP.NET Core 구성으로 가져와서 통합 하는 하는 방법을 설명 합니다 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 패키지.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 시작 확장
가져온 후는 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 패키지를 ASP.NET Core 구성 API 사용 하 여 Service Fabric 구성 소스를 등록 해야 합니다. 확인 하 여이 작업을 수행 **AddServiceFabricConfiguration** 확장을 `Microsoft.ServiceFabric.AspNetCore.Configuration` 네임 스페이스에 대해 `IConfigurationBuilder`합니다.

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

이제 ASP.NET Core 서비스에는 Service Fabric 구성 설정을 다른 응용 프로그램 설정과 마찬가지로 액세스할 수 있습니다. 예를 들어, 강력한 형식의 개체로 설정 로드 옵션 패턴을 사용할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>기본 키 매핑
기본적으로 Service Fabric 구성 공급자를 패키지 이름, 섹션 이름 및 속성 이름을 포함합니다. 이러한 지역은 함께 ASP.NET Core 구성 키를 다음과 같이 합니다.
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

예를 들어 명명 된 구성 패키지가 `MyConfigPackage` 는 다음과 같은 내용으로 다음 구성 값에서 사용할 수 ASP.NET Core `IConfiguration` 를 통해 *MyConfigPackage:MyConfigSection:MyParameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 구성 옵션
Service Fabric 구성 공급자도 지원 합니다. `ServiceFabricConfigurationOptions` 키 매핑의 기본 동작을 변경 합니다.

#### <a name="encrypted-settings"></a>암호화 설정
Service Fabric Service Fabric 구성 공급자는 암호화 된 설정을 지원 합니다. ASP.NET Core로 암호화 된 설정 암호 해독 되지 않습니다 `IConfiguration` 기본적으로 합니다. 암호화 된 값 대신 저장 됩니다. ASP.NET Core IConfiguration에 저장할 값의 암호를 해독 하려는 경우 설정할 수 있습니다 하지만 합니다 *DecryptValue* 플래그를 false는 `AddServiceFabricConfiguration` 다음과 같이 확장:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>여러 구성 패키지
Service Fabric은 여러 구성 패키지를 지원 합니다. 기본적으로 패키지 이름은 구성 키에 포함 됩니다. 설정할 수 있습니다는 `IncludePackageName` 다음과 같은 플래그를 false로 합니다.
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
Service Fabric 구성 공급자를 사용 하 여 키 매핑 사용자 지정 하려면 고급 시나리오도 지원 `ExtractKeyFunc` 사용자 지정 값을 추출 하 고 `ExtractValueFunc`입니다. 사용 하 여 ASP.NET Core 구성에 Service Fabric 구성에서 데이터를 채우는 전체 프로세스를 변경할 수도 있습니다 `ConfigAction`합니다.

다음 예제에서는 사용 하는 방법을 보여 줍니다 `ConfigAction` 데이터 채우기를 사용자 지정할 수 있습니다.
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
또한 Service Fabric 구성 공급자 구성 업데이트를 지원합니다. ASP.NET Core를 사용할 수 있습니다 `IOptionsMonitor` 변경 알림 받기 및 사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드 합니다. 자세한 내용은 [ASP.NET Core 옵션](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)합니다.

이러한 옵션은 기본적으로 지원 됩니다. 더 이상 코딩 구성 업데이트를 사용 하도록 설정 하려면 필요 합니다.

## <a name="scenarios-and-configurations"></a>시나리오 및 구성
이 섹션에서는 조합 웹 서버, 포트 구성, Service Fabric 통합 옵션 및 기타 설정을 다음 시나리오를 해결 하는 것이 좋습니다.
 - 외부에 노출된 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 저장 서비스

**외부 서비스에 노출 된** 일반적으로 부하 분산 장치를 통해 클러스터 외부에서 호출 되는 끝점을 노출 하는 것입니다.

**내부 전용** 서비스는 끝점이 클러스터 내에서 호출만 합니다.

> [!NOTE]
> 상태 저장 서비스 끝점 일반적으로 인터넷에 노출 해서는 안 됩니다. Azure Load Balancer와 같은 Service Fabric 서비스 확인, 인식 되지 않는 부하 분산 장치 뒤 클러스터 상태 저장 서비스를 공개할 수 없습니다. 이것은 부하 분산 장치를 찾아 적절 한 상태 저장 서비스 복제본에 트래픽을 라우팅할 수 없습니다. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>외부에 노출된 ASP.NET Core 상태 비저장 서비스
Kestrel은 외부 인터넷 연결 HTTP 끝점을 노출 하는 프런트 엔드 서비스에 대 한 제안 된 웹 서버입니다. Windows에서 HTTP.sys 같은 동일한 포트를 사용 하 여 노드 집합에서 여러 웹 서비스를 호스팅할 수 있는 포트 공유 기능을 제공할 수 있습니다. 이 시나리오에서는 웹 서비스 프런트 엔드 프록시 또는 게이트웨이가 HTTP 라우팅을 제공 하기에 의존 하지 않고 호스트 이름 또는 경로에서 구분 됩니다.
 
인터넷에 노출 하는 경우 잘 알려진 안정적인 끝점을 부하 분산 장치를 통해 연결할 수 있는 상태 비저장 서비스에 사용 해야 합니다. 응용 프로그램의 사용자에 게이 URL을 제공할 수 있습니다. 다음 구성이 권장됩니다.

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | Kestrel은 Windows 및 Linux에서 지원 되는 기본 웹 서버를 합니다. |
| 포트 구성 | 정적 | 잘 알려진 정적 포트는 ServiceManifest.xml의 `Endpoints` 구성에 구성해야 합니다(예: HTTP의 경우 80, HTTPS의 경우 443). |
| ServiceFabricIntegrationOptions | 없음 | 사용 된 `ServiceFabricIntegrationOptions.None` Service Fabric 통합 미들웨어를 구성 하는 경우 옵션을 서비스에 대 한 고유 식별자를 들어오는 요청의 유효성을 검사 하려고 시도 하지 않습니다. 응용 프로그램의 외부 사용자는 미들웨어를 사용 하는 고유한 식별 정보를 알 수 없습니다. |
| 인스턴스 수 | -1 | 일반적인 사용 사례에서 설정 하는 인스턴스 수로 설정 해야 *-1*합니다. 이 인스턴스를 부하 분산 장치에서 트래픽을 수신 하는 모든 노드에서 사용할 수 있도록 수행 됩니다. |

여러 외부적으로 노출 된 서비스는 동일한 노드 집합을 공유 하는 경우에 고유 하지만 안정 된 URL 경로 사용 하 여 HTTP.sys를 사용할 수 있습니다. 이 IWebHost를 구성할 때 제공 된 URL을 수정 하 여 수행할 수 있습니다. 이에 적용 되도록 HTTP.sys만 note 합니다.

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

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | 내부 상태 비저장 서비스에 대 한 HTTP.sys를 사용 하 여 있지만 Kestrel은 여러 서비스 인스턴스에서 호스트를 공유할 수 있도록 가장 적합 한 서버입니다.  |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있습니다 및 있으므로 고유한 포트가 필요 합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |
| InstanceCount | 모든 | 인스턴스 수 설정은 서비스를 작동하는 데 필요한 모든 값으로 설정할 수 있습니다. |

### <a name="internal-only-stateful-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 저장 서비스
클러스터 내에서만 호출되는 상태 저장 서비스는 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 다음 구성이 권장됩니다.

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | `HttpSysCommunicationListener` 복제본 호스트 프로세스를 공유할 수 있는 상태 저장 서비스에서 사용 하도록 설계 되지 않습니다. |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있습니다 및 있으므로 고유한 포트가 필요 합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 Service Fabric 애플리케이션 디버그](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
