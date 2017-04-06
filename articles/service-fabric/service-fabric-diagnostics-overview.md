---
title: "Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs"
description: "Azure, 개발 또는 온-프레미스에서 호스팅되는 Microsoft Azure Service Fabric 응용 프로그램을 모니터링하고 진단하는 방법에 대해 알아봅니다."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: ebbb29ee031fb477ce284f7a0d27c1522317f4f0
ms.openlocfilehash: 46a35fa4ec341561ab234f7ec19fb20658fcb2c4
ms.lasthandoff: 02/27/2017


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Azure Service Fabric 응용 프로그램 모니터링 및 진단

모니터링 및 진단은 실제 배포된 프로덕션 환경에서 중요합니다. Azure Service Fabric을 사용하면 서비스를 개발할 때 모니터링 및 진단을 구현하여 단일 컴퓨터, 로컬 개발 환경 및 실제 프로덕션 클러스터 설치에서 서비스가 원활하게 작동할 수 있도록 합니다.

모니터링 및 진단은 다음과 같은 목적으로 서비스를 개발할 때 도움이 될 수 있습니다.
* 고객의 혼란 최소화
* 비즈니스 통찰력 제공
* 리소스 사용 모니터링
* 하드웨어 및 소프트웨어 오류 또는 성능 문제 감지
* 잠재적 서비스 문제 진단

모니터링은 다음과 같은 작업을 포함하는 광범위한 용어입니다.
* 코드 계측
* 계측 로그 수집
* 로그 분석
* 로그 데이터에 기반한 통찰력 시각화
* 로그 값 및 통찰력에 기반한 경고 설정
* 인프라 모니터링
* 고객에게 영향을 주는 문제 감지 및 진단

이 문서에서는 Windows 또는 Linux에 배포된 Azure 또는 온-프레미스에서 호스팅되거나 Microsoft .NET Framework를 사용하여 호스팅되는 Service Fabric 클러스터 모니터링에 대한 개요를 간략히 설명합니다. 모니터링 및 진단의 세 가지 중요한 측면을 살펴봅니다.
- 코드 또는 인프라 계측
- 생성된 이벤트 수집
- 저장소, 집계, 시각화 및 분석

이 세 가지 측면 모두를 다루는 여러 제품을 사용할 수 있지만, 많은 고객이 모니터링의 각 측면에 대해 서로 다른 기술을 선택합니다. 응용 프로그램에 종단 간 모니터링 솔루션을 제공하도록 각각의 기술이 함께 작동하는 것이 중요합니다.

## <a name="monitoring-infrastructure"></a>인프라 모니터링

Service Fabric을 사용하면 인프라 오류 시에도 응용 프로그램을 계속 실행할 수 있지만, 응용 프로그램이나 기본 인프라에서 오류가 발생하는지를 파악해야 합니다. 용량 계획을 위해 인프라를 모니터링해야 하므로 인프라를 추가하거나 제거할 시기를 파악할 수 있습니다. Service Fabric 배포를 구성하는 인프라와 응용 프로그램을 모두 모니터링하고 문제를 해결하는 것이 중요합니다. 고객이 응용 프로그램을 사용할 수 있는 경우에도 인프라에 여전히 문제가 있을 수 있습니다.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md)를 사용하여 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다. [가상 컴퓨터 크기 집합](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) 및 개별 [가상 컴퓨터](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)에 대한 일단의 메트릭이 자동으로 수집되어 Azure Portal에 표시됩니다. 수집된 정보를 보려면 Azure Portal에서 Service Fabric 클러스터가 포함된 리소스 그룹을 선택합니다. 그런 다음 보려는 가상 컴퓨터 크기 집합을 선택합니다. **모니터링** 섹션에서 **메트릭**을 선택하여 값의 그래프를 봅니다.

![수집된 메트릭 정보의 Azure Portal 보기](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.PNG)

차트를 사용자 지정하려면 [Microsoft Azure의 메트릭](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)의 지침을 따릅니다. 또한 [Azure Monitor에서 Azure 서비스에 대한 알림 만들기](../monitoring-and-diagnostics/insights-alerts-portal.md)에서 설명한 대로 이러한 메트릭을 기반으로 하여 알림을 만들 수 있습니다. [Azure 메트릭 경고에 대한 웹후크 구성](../monitoring-and-diagnostics/insights-webhooks-alerts.md)에서 설명한 대로 웹후크를 사용하여 알림 서비스에 알림을 보낼 수 있습니다. Azure Monitor는 하나의 구독만 지원합니다. 여러 구독을 모니터링해야 하거나 추가 기능이 필요한 경우 Microsoft Operations Management Suite의 일부인 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/)는 온-프레미스 및 클라우드 기반 인프라에 포괄적인 IT 관리 솔루션을 제공합니다. Azure Monitor의 데이터를 Log Analytics로 직접 라우팅할 수 있으므로 전체 환경에 대한 메트릭과 로그를 한 곳에서 볼 수 있습니다.

Operations Management Suite를 사용하여 온-프레미스 인프라를 모니터링하는 것이 좋지만, 조직에서 인프라 모니터링에 사용하는 기존 솔루션도 사용할 수 있습니다.

### <a name="service-fabric-support-logs"></a>Service Fabric 지원 로그

Azure Service Fabric 클러스터에 대한 도움을 받기 위해 Microsoft 지원에 문의해야 하는 경우 지원 로그가 거의 항상 필요합니다. 클러스터가 Azure에서 호스팅되는 경우 클러스터를 만드는 과정의 일부로 이러한 로그를 자동으로 구성하여 수집합니다. 로그는 클러스터의 리소스 그룹에 있는 전용 저장소 계정에 저장됩니다. 저장소 계정에 고정된 이름은 없지만, 계정에는 *fabric*으로 시작하는 이름의 Blob 컨테이너와 테이블이 표시됩니다. 독립 실행형 클러스터의 로그 수집 설정에 대한 자세한 내용은 [독립 실행형 Azure Service Fabric 클러스터 만들기 및 관리](service-fabric-cluster-creation-for-windows-server.md) 및 [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)을 참조하세요. 독립 실행형 Service Fabric 인스턴스의 경우 로그는 로컬 파일 공유로 보내야 합니다. 지원을 받으려면 이러한 로그가 **필요하지만** Microsoft 고객 지원 팀 외부의 사람이 사용할 수 있는 것은 아닙니다.

## <a name="instrument-your-code"></a>코드 계측

코드를 계측하는 것은 다른 측면 대부분의 서비스 모니터링에 대한 기반이 됩니다. 계측은 무언가가 잘못되었다는 것을 알 수 있는 유일한 방법이며, 수정해야 할 것을 진단합니다. 기술적으로 디버거를 프로덕션 서비스에 연결할 수도 있지만 일반적인 방법은 아닙니다. 따라서 자세한 계측 데이터를 갖는 것이 중요합니다. 이러한 대량의 정보를 생성할 때 로컬 노드에서 모든 이벤트를 전송하는 것은 많은 비용이 듭니다. 많은 서비스에서 다음 두 가지 전략을 사용하여 계측 데이터 양을 처리합니다.
1.  모든 이벤트는 짧은 간격으로 로컬 롤링 로그 파일에 저장되며 디버깅에 필요할 때만 수집됩니다. 일반적으로 자세한 진단을 위해 필요한 이벤트는 비용과 리소스 사용을 줄이기 위해 노드에 남아 있습니다.
2.  서비스 상태를 나타내는 모든 이벤트는 중앙 리포지토리로 보내지며, 이러한 이벤트를 통해 비정상 서비스에 대한 경고를 알릴 수 있습니다. 서비스 상태 이벤트에는 오류 이벤트, 하트비트 이벤트 및 성능 이벤트가 포함됩니다.

일부 제품은 자동으로 코드를 계측합니다. 이러한 솔루션이 잘 작동할 수 있지만 수동 계측이 거의 항상 필요합니다. 결국 응용 프로그램을 과학 수사 방식으로 디버그하는 데 필요한 정보가 충분히 있어야 합니다. 다음 섹션에서는 코드를 계측하는 여러 가지 방법과 다른 방법을 선택하는 경우에 대해 설명합니다.

### <a name="eventsource"></a>EventSource

Visual Studio의 템플릿에서 Service Fabric 솔루션을 만들면 **EventSource** 파생 클래스(**ServiceEventSource** 또는 **ActorEventSource**)가 생성됩니다. 응용 프로그램 또는 서비스에 대한 이벤트를 추가할 수 있는 템플릿을 만듭니다. **EventSource** 이름은 **고유해야** 하며 MyCompany-&lt;solution&gt;-&lt;project&gt; 기본 템플릿 문자열에서 이름을 변경해야 합니다. 동일한 이름을 사용하는 **EventSource** 정의가 여러 개 있으면 런타임에서 문제가 발생합니다. 정의된 각 이벤트에는 고유 식별자가 있어야 합니다. 식별자가 고유하지 않으면 런타임 오류가 발생합니다. 일부 조직에서는 별도의 개발 팀 간에 충돌을 피하기 위해 식별자에 대한 값의 범위가 미리 할당됩니다. 자세한 내용은 [Vance의 블로그](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) 또는 [MSDN 설명서](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)를 참조하세요.

#### <a name="using-structured-eventsource-events"></a>구조적 EventSource 이벤트 사용

이 섹션의 코드 예제에 있는 이벤트 각각은 특정 사례(예: 서비스 유형이 등록된 경우)에 대해 정의됩니다. 사용 사례별로 메시지를 정의할 때 오류 텍스트와 함께 데이터를 패키지할 수 있으며, 지정된 속성의 이름이나 값을 기반으로 하여 보다 쉽게 검색하고 필터링할 수 있습니다. 계측 출력을 구조화하면 더 쉽게 사용할 수 있지만 각 사용 사례마다 새 이벤트를 정의하는 데 더 많은 생각과 시간이 필요합니다. 일부 이벤트 정의는 전체 응용 프로그램에서 공유할 수 있습니다. 예를 들어 메서드 시작 또는 중지 이벤트는 응용 프로그램 내의 많은 서비스에서 다시 사용됩니다. 주문 시스템과 같은 도메인 관련 서비스에는 자체의 고유 이벤트를 포함하는 **CreateOrder** 이벤트가 있을 수 있습니다. 이 방법은 많은 이벤트를 생성하고 잠재적으로 프로젝트 팀 전체에서 식별자를 조정할 필요가 있습니다. Service Fabric의 구조적 **EventSource** 이벤트에 대한 전체 예제는 Party Cluster 샘플의 **PartyCluster.ApplicationDeployService** 이벤트를 참조하세요.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The instance constructor is private to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```

#### <a name="using-eventsource-generically"></a>일반적 EventSource 사용

특정 이벤트를 정의하는 것이 어려울 수 있기 때문에 많은 사람들이 일반적으로 정보를 문자열로 출력하는 공통 매개 변수 집합을 포함하는 몇 가지 이벤트를 정의합니다. 구조적 측면 대부분이 손실되어 결과를 검색하고 필터링하는 것이 더욱 어렵습니다. 이 방법에서는 일반적으로 로깅 수준에 해당하는 몇 가지 이벤트가 정의됩니다. 다음 코드 조각에서는 디버그 및 오류 메시지를 정의합니다.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // The Instance constructor is private, to enforce singleton semantics.
        private ServiceEventSource() : base() { }

        ...

        private const int DebugEventId = 10;
        [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
        public void Debug(string msg)
        {
            WriteEvent(DebugEventId, msg);
        }

        private const int ErrorEventId = 11;
        [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
        public void Error(string error, string msg)
        {
            WriteEvent(ErrorEventId, error, msg);
        }
```

또한 구조적 계측과 일반 계측의 하이브리드를 사용하면 효과적일 수 있습니다. 구조적 계측은 오류 및 메트릭 보고에 사용됩니다. 일반 이벤트는 문제 해결을 위해 엔지니어가 사용하는 자세한 로깅에 사용할 수 있습니다.

### <a name="aspnet-core-logging"></a>ASP.NET Core 로깅

코드를 계측하는 방법을 신중하게 계획해야 합니다. 올바른 계측 계획을 사용하면 잠재적인 코드 기반의 불안정을 피한 다음 코드를 다시 계측할 수 있습니다. 위험을 줄이기 위해 Microsoft ASP.NET Core의 일부인 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)과 같은 계측 라이브러리를 선택할 수 있습니다. ASP.NET Core에는 기존 코드에 미치는 영향을 최소화하면서 선택한 공급자와 함께 사용할 수 있는 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 인터페이스가 있습니다. Windows 및 Linux의 ASP.NET Core 및 전체 .NET Framework의 코드를 사용하여 계측 코드를 표준화할 수 있습니다.

#### <a name="using-microsoftextensionslogging-in-service-fabric"></a>Service Fabric에서 Microsoft.Extensions.Logging 사용

1. Microsoft.Extensions.Logging NuGet 패키지를 계측하려는 프로젝트에 추가합니다. 또한 모든 공급자 패키지도 추가합니다(타사 패키지의 경우 다음 예제 참조). 자세한 내용은 [ASP.NET Core 로그인](https://docs.microsoft.com/aspnet/core/fundamentals/logging)(영문)을 참조하세요.
2. 'Microsoft.Extensions.Logging'에 대한 **using** 지시문을 서비스 파일에 추가합니다.
3. 서비스 클래스 내에 private 변수를 정의합니다.

  ```csharp
  private ILogger _logger = null;

  ```
4. 서비스 클래스의 생성자에서 다음 코드를 추가합니다.

  ```csharp
  _logger = new LoggerFactory().CreateLogger<Stateless>();

  ```
5. 메서드에서 코드 계측을 시작합니다. 다음은 몇 가지 샘플입니다.

  ```csharp
  _logger.LogDebug("Debug-level event from Microsoft.Logging");
  _logger.LogInformation("Informational-level event from Microsoft.Logging");

  // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
  // Later in the article, we discuss why this step is useful.
  _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

  ```

#### <a name="using-other-logging-providers"></a>다른 로깅 공급자 사용

타사 공급자 중 일부는 [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/) 및 [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging)를 포함하여 이전 섹션에서 설명한 방법을 사용합니다. 이들 각각을 ASP.NET Core 로깅에 연결하거나 개별적으로 사용할 수 있습니다. Serilog에는 로거에서 보낸 모든 메시지를 보강하는 기능이 있습니다. 이 기능은 서비스 이름, 유형 및 파티션 정보를 출력하는 데 유용할 수 있습니다. ASP.NET Core 인프라에서 이 기능을 사용하려면 다음 단계를 수행합니다.

1. Serilog, Serilog.Extensions.Logging 및 Serilog.Sinks.Observable NuGet 패키지를 프로젝트에 추가합니다. 다음 예제에서는 Serilog.Sinks.Literate도 추가합니다. 더 나은 방법은 이 문서의 뒷부분에 나와 있습니다.
2. Serilog에서 LoggerConfiguration 및 Logger 인스턴스를 만듭니다.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Serilog.ILogger 인수를 서비스 생성자에 추가하고 새로 만들어진 로거를 전달합니다.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. 서비스 생성자에서 다음 코드를 추가합니다. 이 코드는 서비스의 **ServiceTypeName**, **ServiceName**, **PartitionId** 및 **InstanceId** 속성에 대한 속성 보강자를 만듭니다. 또한 ASP.NET Core 로깅 팩터리에 속성 보강자를 추가하여 코드에 Microsoft.Extensions.Logging.ILogger를 사용할 수도 있습니다.

  ```csharp
  public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
      : base(context)
  {
      PropertyEnricher[] properties = new PropertyEnricher[]
      {
          new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
          new PropertyEnricher("ServiceName", context.ServiceName),
          new PropertyEnricher("PartitionId", context.PartitionId),
          new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
      };

      serilog.ForContext(properties);

      _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
  }
  ```

5. Serilog 없이 ASP.NET Core를 사용하는 경우와 동일한 코드를 계측합니다.

  > [!NOTE]
  > 앞의 예제와 함께 정적 Log.Logger를 사용하지 않는 것이 좋습니다. Service Fabric은 단일 프로세스 내에서 동일한 서비스 유형의 여러 인스턴스를 호스팅할 수 있습니다. 정적 Log.Logger를 사용하는 경우 속성 보강자의 마지막 기록기는 실행 중인 모든 인스턴스의 값을 표시합니다. _logger 변수가 서비스 클래스의 private 멤버 변수가 되는 한 가지 이유입니다. 또한 서비스 전체에서 사용할 수 있는 일반 코드에서 _logger를 사용할 수 있도록 합니다.

### <a name="choosing-a-logging-provider"></a>로깅 공급자 선택

응용 프로그램이 높은 성능에 의존하는 경우 **EventSource**를 사용하는 것이 가장 좋습니다. **EventSource**는 *일반적으로* ASP.NET Core 로깅이나 사용 가능한 타사 솔루션보다 더 적은 리소스를 사용하는 한편 더 효율적으로 수행합니다.  이는 대부분의 서비스에서 문제가 되지 않지만, 성능 지향적인 서비스인 경우 **EventSource**를 사용하는 것이 더 나은 선택일 수 있습니다. 구조적 로깅과 동일한 이점을 얻으려면 엔지니어링 팀에서 **EventSource**에 많이 투자해야 합니다. 프로젝트에 사용할 방법을 결정하려면 각 옵션에서 수반하는 빠른 프로토타입을 수행한 다음 요구 사항에 가장 부합하는 것을 선택합니다.

## <a name="event-and-log-collection"></a>이벤트 및 로그 수집

### <a name="azure-diagnostics"></a>Azure 진단

Azure Monitor에서 제공하는 정보 외에도 Azure는 중앙 위치에서 각 서비스의 이벤트를 수집합니다. 자세한 내용은 [Windows](service-fabric-diagnostics-how-to-setup-wad.md) 및 [Linux](service-fabric-diagnostics-how-to-setup-lad.md)에 대한 이벤트 수집을 구성하는 방법을 알아보세요. 이러한 문서에서는 이벤트 데이터를 수집하여 Azure 저장소로 보내는 방법을 보여 줍니다. 진단을 사용하도록 설정하여 Azure Portal 또는 Azure Resource Manager 템플릿에서 이 작업을 수행할 수 있습니다. Azure 진단은 Service Fabric에서 자동으로 생성하는 다음과 같은 몇 가지 이벤트 원본을 수집합니다.

- Reliable Actor 프로그래밍 모델을 사용하는 경우 **EventSource** 이벤트 및 성능 카운터를 수집합니다. 관련 이벤트는 [Reliable Actors에 대한 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md)에서 열거하고 있습니다.
- Reliable Services 프로그래밍 모델을 사용하는 경우 **EventSource** 이벤트를 수집합니다. 관련 이벤트는 [상태 저장 Reliable Services의 진단 기능](service-fabric-reliable-services-diagnostics.md)에서 열거하고 있습니다.
- 시스템 이벤트를 ETW(Windows용 이벤트 추적) 이벤트로 내보냅니다. 서비스 배치 및 시작/중지 이벤트를 포함하여 이 범주의 일부로 Service Fabric에서 많은 이벤트를 내보냅니다. 내보낸 이벤트를 확인하는 가장 좋은 방법은 로컬 컴퓨터에서 [Visual Studio 진단 이벤트 뷰어](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 사용하는 것입니다. 이러한 이벤트는 네이티브 ETW 이벤트이므로 수집할 수 있는 방법에 몇 가지 제한이 있습니다.
- Service Fabric 5.4 릴리스부터 상태 및 로드 메트릭 이벤트가 공개되고 있습니다. 이러한 방식으로 기록 보고 및 경고를 위해 이벤트 수집을 사용할 수 있습니다. 이러한 이벤트도 네이티브 ETW 이벤트이며 수집할 수 있는 방법에 몇 가지 제한이 있습니다.

구성하는 경우 진단을 사용하도록 설정했다고 가정하고 클러스터를 만들 때 만들어진 Azure 저장소 계정에 이벤트가 나타납니다. 테이블의 이름은 WADServiceFabricReliableActorEventTable, WADServiceFabricReliableServiceEventTable 및 WADServiceFabricSystemEventTable입니다. 상태 이벤트는 기본적으로 추가되지 않으며, Resource Manager 템플릿을 수정하여 추가해야 합니다. 자세한 내용은 [Azure 진단을 사용하여 로그 수집](service-fabric-diagnostics-how-to-setup-wad.md)을 참조하세요.

이 섹션에서 나열하는 이러한 문서도 사용자 지정 이벤트를 Azure 저장소로 가져오는 방법을 보여 줍니다. 성능 카운터 구성에 대한 다른 Azure 진단 문서 또는 가상 컴퓨터에서 Azure 진단으로의 다른 모니터링 정보가 있는 문서는 Service Fabric 클러스터에도 적용됩니다. 예를 들어 Azure Table Storage를 대상으로 사용하지 않으려면 [Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍](../event-hubs/event-hubs-streaming-azure-diags-data.md)을 참조하세요. 이벤트가 Azure Event Hubs에 있는 경우 해당 이벤트를 읽고 선택한 위치로 보낼 수 있습니다. [Application Insights와 Azure 진단 정보를 통합](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)(영문)하는 방법에 대한 자세한 정보를 얻을 수 있습니다.

Azure 진단을 사용하는 단점은 Resource Manager 템플릿을 사용하여 설정한다는 것입니다. 그런 다음 가상 컴퓨터 크기 집합 수준에서만 진단이 발생합니다. 가상 컴퓨터 크기 집합은 Service Fabric의 노드 유형에 해당합니다. 해당 유형의 노드에서 실행될 수 있는 모든 응용 프로그램과 서비스에 대해 각각의 노드 유형을 각각 구성합니다. 이렇게 하면 구성한 응용 프로그램과 서비스의 수에 따라 많은 **EventSource** 이벤트가 있을 수 있습니다. 또한 응용 프로그램 구성이 변경될 때마다 Resource Manager를 배포해야 합니다. 모니터링 구성은 서비스 구성과 함께 전달되는 것이 가장 이상적입니다.

Azure 진단은 Azure에 배포된 Service Fabric 클러스터에서만 작동합니다. Windows 및 Linux 클러스터 모두에서도 작동합니다.

### <a name="eventflow"></a>EventFlow

[Microsoft 진단 EventFlow](https://github.com/Azure/diagnostics-eventflow)(영문)는 노드의 이벤트를 하나 이상의 모니터링 대상으로 라우팅할 수 있습니다. EventFlow가 서비스 프로젝트에 NuGet 패키지로 포함되어 있어 EventFlow 코드와 구성이 서비스와 함께 이동하므로 Azure 진단에 대해 앞에서 언급한 노드별 구성 문제가 제거됩니다. EventFlow는 서비스 프로세스 내에서 실행되며 구성된 출력에 직접 연결됩니다. 이러한 직접 연결로 인해 EventFlow는 Azure, 컨테이너 및 온-프레미스 서비스 배포에서 작동합니다. 각 EventFlow 파이프라인이 외부 연결을 만들기 때문에 컨테이너와 같이 고밀도 시나리오에서 EventFlow를 실행하는 경우 신중해야 합니다. 많은 프로세스를 호스팅하는 경우 아웃바운드 연결이 많이 있게 됩니다! 이 경우`ServiceType`의 모든 복제본이 동일한 프로세스에서 실행되어 아웃바운드 연결 수를 제한하므로 Service Fabric 응용 프로그램에 별로 문제가 되지 않습니다. EventFlow는 이벤트 필터링도 제공하므로 지정된 필터와 일치하는 이벤트만 보냅니다. Service Fabric에서 EventFlow를 사용하는 방법에 대한 자세한 내용은 [Azure Service Fabric 서비스 프로세스에서 직접 로그 수집](service-fabric-diagnostic-collect-logs-without-an-agent.md)을 참조하세요.

EventFlow를 사용하려면 다음을 수행합니다.

1. 서비스 프로젝트에 NuGet 패키지를 추가합니다.
2. 서비스의 **Main** 함수에서 EventFlow 파이프라인을 만든 다음 출력을 구성합니다. 다음 예제에서는 Serilog를 출력으로 사용합니다.

  ```csharp
  internal static class Program
  {
      /// <summary>
      /// This is the entry point of the service host process.
      /// </summary>
      private static void Main()
      {
          try
          {
              using (var pipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MonitoringE2E-Stateless-Pipeline"))
              {
                  Log.Logger = new LoggerConfiguration().WriteTo.EventFlow(pipeline).CreateLogger();

                  // The ServiceManifest.xml file defines one or more service type names.
                  // Registering a service maps a service type name to a .NET type.
                  // When Service Fabric creates an instance of this service type,
                  // an instance of the class is created in this host process.

                  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                  ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);

                  // Prevents this host process from terminating, so services keep running.
                  Thread.Sleep(Timeout.Infinite);
              }
          }
          catch (Exception e)
          {
              ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
              throw;
          }
      }
  }
  ```

3. 서비스의 \\PackageRoot\\ 구성 폴더에 eventFlowConfig.json이라는 파일을 만듭니다. 파일 내 구성은 다음과 같습니다.

  ```json
      {
      "inputs": [
          {
          "type": "EventSource",
          "sources": [
              { "providerName": "Microsoft-ServiceFabric-Services" },
              { "providerName": "Microsoft-ServiceFabric-Actors" },
              { "providerName": "MyCompany-MonitoringE2E-Stateless" }
          ]
          },
          {
          "type": "Serilog"
          }
      ],
      "filters": [
          {
          "type": "drop",
          "include": "Level == Verbose"
          },
          {
          "type": "metadata",
          "metadata": "request",
          "requestNameProperty": "RequestName",
          "include":  "RequestName==MyRequest",
          "durationProperty": "Duration",
          "durationUnit": "milliseconds"
          }
      ],
      "outputs": [
          {
          "type": "StdOutput"
          },
          {
          "type": "ApplicationInsights",
          "instrumentationKey": "== instrumentation key here =="
          }
      ],
      "schemaVersion": "2016-08-11",
      "extensions": []
      }
  ```
    구성에는 Service Fabric에서 만든 두 개의 **EventSource** 기반 원본 및 서비스에 대한 **EventSource**의 두 가지 입력이 정의되어 있습니다. ETW를 사용하는 시스템 수준 및 상태 이벤트는 EventFlow에서 사용할 수 없습니다. 이는 ETW 원본을 수신 대기하는 데에는 높은 수준의 권한이 필요하지만 서비스는 높은 권한으로 실행하면 안되기 때문입니다. 다른 입력은 Serilog입니다. Serilog 구성은 **Main** 메서드에서 발생하는 구성입니다.  일부 필터가 적용됩니다. 첫 번째 필터는 이벤트 세부 정보 표시 수준의 모든 이벤트를 삭제하도록 EventFlow에 지시합니다. Visual Studio의 출력 창에 쓰는 표준 출력과 ApplicationInsights의 두 가지 출력이 구성되어 있습니다. 계측 키를 추가해야 합니다.

4. 코드를 계측합니다. 다음 예제에서는 예제를 보여 주기 위해 여러 가지 방법으로 `RunAsync`를 계측합니다. 다음 코드에서는 Serilog를 여전히 사용하고 있습니다. 사용하는 구문 중 일부는 Serilog에만 관련이 있습니다. 선택한 로깅 솔루션의 특정 기능에 주의합니다. 디버그 수준 이벤트 및 두 개의 정보 이벤트의 세 가지 이벤트가 생성됩니다. 두 번째 정보 이벤트는 요청 기간을 추적합니다. 앞에서 설명한 EventFlow의 구성에서 디버그 수준 이벤트는 출력으로 전달되지 않아야 합니다.

  ```csharp
      Stopwatch sw = Stopwatch.StartNew();

      while (true)
      {
          cancellationToken.ThrowIfCancellationRequested();

          sw.Restart();

          // Delay a random interval, to provide a more interesting request duration.
          await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

          ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
          _logger.LogDebug("Debug level event from Microsoft.Logging");
          _logger.LogInformation("Informational level event from Microsoft.Logging");
          _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
      }
  ```

Azure Application Insights에서 이벤트를 보려면 Azure Portal에서 Application Insights 리소스로 이동합니다. 이벤트를 보려면 **검색** 상자를 선택합니다.

![Application Insights 검색 보기의 이벤트](./media/service-fabric-diagnostics-overview/ai-search-events.PNG)

이전 스크린샷의 아래쪽에서 추적을 볼 수 있습니다. 두 이벤트만 표시하고, 디버그 수준 이벤트는 EventFlow에서 삭제했습니다. 추적 앞에 있는 요청 항목은 세 번째 `_logger` 계측 선입니다. 이 선은 이벤트를 Application Insights의 요청 메트릭으로 변환했음을 보여 줍니다.

필터 정의에서 형식은 **메타데이터**입니다. 이는 `MyRequest` 값을 가진 `RequestName` 속성과 요청 시간(밀리초)을 포함한 `Duration` 속성을 가지고 있는 이벤트를 선언하고 있습니다. Application Insights의 요청 이벤트에서 이 이벤트를 볼 수 있습니다. 동일한 방법이 **EventSource**를 포함하여 지원되는 EventFlow 입력 중 하나에서 작동합니다.

정책 상의 이유로 클라우드 기반 솔루션에 연결할 수 없는 독립 실행형 클러스터가 있는 경우 Elasticsearch를 출력으로 사용할 수 있습니다. 그러나 다른 출력을 쓸 수 있으며 끌어 오기 요청을 사용하는 것이 좋습니다. ASP.NET Core 로깅을 위한 타사 공급자 중 일부에는 온-프레미스 설치를 지원하는 솔루션도 있습니다.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 상태 및 로드 보고

Service Fabric에는 다음과 같은 문서에서 자세히 설명하는 자체의 상태 모델이 있습니다.
- [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)
- [서비스 상태 보고 및 확인](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [사용자 지정 서비스 패브릭 상태 보고서 추가](service-fabric-report-health.md)
- [서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

상태 모니터링은 서비스 운영의 여러 측면에서 매우 중요합니다. 상태 모니터링은 Service Fabric에서 명명된 응용 프로그램 업그레이드를 수행할 때 특히 중요합니다. 서비스의 각 업그레이드 도메인을 업그레이드하고 고객이 사용할 수 있게 되면, 배포가 다음 업그레이드 도메인으로 이동하기 전에 업그레이드 도메인이 상태 검사를 통과해야 합니다. 상태가 양호하지 않으면 배포를 롤백하여 응용 프로그램을 알려진 양호한 상태로 유지합니다. 서비스를 롤백하기 전에 일부 고객이 영향을 받을 수 있지만, 대부분의 고객에게는 문제가 발생하지 않습니다. 또한 비교적 빨리 해결되며 운영자가 조치를 취할 때까지 기다릴 필요가 없습니다. 코드에 통합된 상태 검사가 많을수록 배포 문제에 대한 서비스 복원력이 더욱 높아집니다.

서비스 상태의 또 다른 측면은 서비스의 메트릭 보고입니다. 메트릭은 리소스 사용량을 조정하는 데 사용되므로 Service Fabric에서 중요합니다. 또한 메트릭은 시스템 상태 표시기가 될 수 있습니다. 예를 들어 많은 서비스가 있는 응용 프로그램을 가질 수 있으며, 각 인스턴스에서 RPS(초당 요청 수)를 보고합니다. 한 서비스에서 다른 서비스보다 많은 리소스를 사용하는 경우 Service Fabric에서 클러스터 주변의 서비스 인스턴스를 이동하여 리소스 사용률을 유지하려고 합니다. 리소스 사용률이 작동하는 방식에 대한 자세한 설명은 [메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

또한 메트릭을 통해 서비스 수행 방식에 대한 통찰력을 얻을 수도 있습니다. 시간이 지남에 따라 메트릭을 사용하여 서비스가 예상된 매개 변수 내에서 작동하는지 확인할 수 있습니다. 예를 들어 추세에서 월요일 오전 9시의 평균 RPS가 1,000인 경우 RPS가 500 미만이거나 1,500 이상일 때 경고하는 상태 보고서를 설정할 수 있습니다. 모든 것이 완벽할 수도 있지만 고객이 매우 만족스럽게 경험하고 있는지도 확인할 만한 가치가 있습니다. 서비스에서 상태 검사를 위해 보고할 수 있지만 클러스터의 리소스 분산에는 영향을 미치지 않는 메트릭 집합을 정의할 수 있습니다. 이렇게 하려면 메트릭 가중치를&0;으로 설정합니다. 가중치가&0;인 모든 메트릭을 시작하고 메트릭의 가중치가 클러스터의 리소스 분산에 미치는 영향을 확실히 이해할 때까지 가중치를 높이지 않는 것이 좋습니다.

> [!TIP]
> 너무 높은 가중치가 적용된 메트릭은 사용하지 마세요. 분산을 조정하기 위해 서비스 인스턴스를 이동하는 이유를 이해하기 어려울 수 있습니다. 몇 가지 메트릭에는 많은 시간이 걸릴 수 있습니다.

응용 프로그램의 상태 및 성능을 나타낼 수 있는 정보는 메트릭 및 상태 보고서의 후보가 됩니다. CPU 성능 카운터는 노드가 활용되는 상태를 알려주지만 단일 노드에서 여러 서비스가 실행될 수 있기 때문에 특정 서비스의 상태가 정상인지 여부는 알려주지 않습니다. 그러나 RPS, 처리된 항목 및 요청 대기 시간과 같은 메트릭은 모두 특정 서비스의 상태를 나타낼 수 있습니다.

상태를 보고하려면 다음과 유사한 코드를 사용합니다.

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

메트릭을 보고하려면 다음과 유사한 코드를 사용합니다.

  ```csharp
    this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

## <a name="watchdogs"></a>Watchdog

Watchdog은 서비스 전체에서 상태와 부하를 감시하고 상태 모델 계층 구조 상의 모든 항목에 대한 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. 또한 Watchdog은 사용자가 개입하지 않아도 알려진 조건에 대한 수정 작업을 수행할 수 있는 코드를 호스팅하기에 좋은 출발점이기도 합니다.

## <a name="visualization-analysis-and-alerts"></a>시각화, 분석 및 경고

모니터링의 마지막 부분은 이벤트 스트림을 시각화하고, 서비스 성능을 보고하고, 문제가 감지되면 경고하는 것입니다. 이러한 모니터링 측면에 대해 서로 다른 솔루션을 사용할 수 있습니다. Azure Application Insights 및 Operations Management Suite를 사용하여 이벤트 스트림을 기반으로 하여 경고할 수 있습니다. Microsoft Power BI 또는 [Kibana](https://www.elastic.co/products/kibana) 또는 [Splunk](https://www.splunk.com/)와 같은 타사 솔루션을 사용하여 데이터를 시각화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 진단을 사용하여 로그 수집](service-fabric-diagnostics-how-to-setup-wad.md)
* [Azure Service Fabric 서비스 프로세스에서 직접 로그 수집](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리](service-fabric-cluster-resource-manager-metrics.md)

