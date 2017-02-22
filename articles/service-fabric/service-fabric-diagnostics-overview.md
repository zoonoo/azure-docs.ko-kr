---
title: "Azure Service Fabric 모니터링 및 진단 개요 | Microsoft Docs"
description: "Azure, 개발 또는 온-프레미스에서 호스팅되는 Microsoft Azure Service Fabric 응용 프로그램을 모니터링하고 진단하는 방법을 알아봅니다."
services: service-fabric
documentationcenter: .net
author: ms-toddabel
manager: mfussell
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/9/2017
ms.author: toddabel
translationtype: Human Translation
ms.sourcegitcommit: 1683b52d4e5e6370a737038128f48b8ea8263038
ms.openlocfilehash: 27df2afd97107dd13a7aa2dd79343ec9425c0aed


---
# <a name="monitor-and-diagnose-azure-service-fabric-applications"></a>Azure Service Fabric 응용 프로그램 모니터링 및 진단

모니터링, 감지, 진단 및 문제 해결은 사용자 환경에 미치는 영향을 최소화하고, 비즈니스 통찰력을 제공하며, 리소스 사용을 모니터링하고, 하드웨어/소프트웨어 오류 또는 성능 문제를 감지하고 잠재적인 서비스 동작 문제를 진단하여 서비스를 계속할 수 있도록 합니다. 모니터링과 진단은 실제로 배포된 프로덕션 환경에서 매우 중요하지만, 효율성은 서비스 개발 과정에서 유사한 모델을 채택하여 실제 설치로 이전했을 때 작동하는지에 따라 결정됩니다. 서비스 패브릭을 사용하면 서비스 개발자가 단일 컴퓨터 로컬 개발 설정과 실제 사용 프로덕션 클러스터 설정 양쪽에서 매끄럽게 작동하는 진단 기능을 간편하게 구현할 수 있습니다. 

모니터링은 코드 계측, 계측 로그 수집, 로그 분석, 로그 데이터 기반 통찰력 시각화, 로그 값/통찰력 기반 경고, 인프라 모니터링 및 엔지니어에 의한 고객에게 영향을 미치는 문제 감지 및 진단을 포함하는 광범위한 용어입니다. 이 문서는 .NET을 사용하여 Windows 또는 Linux에 배포된 Azure 또는 온-프레미스에서 호스팅되는 Service Fabric 클러스터 모니터링에 대한 개요를 간략히 설명하기 위한 것입니다. 이제 문제를 세 부분으로 나누어 살펴보겠습니다.
- 계측 코드 또는 인프라
- 생성된 이벤트 수집
- 저장소, 집계, 시각화 및 분석

세 가지 측면 모두를 다루는 일부 제품이 있지만 많은 고객이 각 영역마다 다양한 기술을 선택하고 있습니다. 이에 따라 각각의 기술을 서로 연결하여 응용 프로그램에 종단 간 모니터링 솔루션을 제공하는 것이 중요합니다. 

## <a name="monitoring-infrastructure"></a>인프라 모니터링

인프라 오류가 발생하는 동안에도 응용 프로그램이 Service Fabric을 통해 계속 실행될 수 있지만 응용 프로그램 운영자는 응용 프로그램 또는 기본 인프라 내에서 오류가 발생하는지를 파악해야 합니다. 인프라 모니터링은 인프라를 추가 또는 제거할 시기를 파악하기 위한 용량 계획에도 필요합니다. Service Fabric 배포를 구성하는 인프라와 응용 프로그램은 모두 모니터링과 문제 해결에 중요합니다. 고객이 응용 프로그램을 사용할 수 있는 한 일부 인프라가 문제가 될 수 있습니다.

### <a name="azure-monitoring"></a>Azure 모니터링

Azure가 배포된 클러스터의 경우 [Azure 모니터링](../monitoring-and-diagnostics/monitoring-overview.md)을 사용하면 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다. 일단의 메트릭이 자동으로 수집되어 [VMSS(Virtual Machine Scale Set)](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) 및 개별 [VM](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines)의 Azure Portal에 표시됩니다. Azure Portal에서 Service Fabric 클러스터가 포함된 리소스 그룹을 선택하고 볼 수 있는 VMSS를 선택하여 이 정보를 볼 수 있습니다. 그런 다음 모니터링 탐색 섹션에서 메트릭을 선택하여 값의 그래프를 봅니다.

![수집된 메트릭 정보의 Azure Portal 보기](./media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

차트는 [Microsoft Azure의 메트릭 개요](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) 문서의 지침에 따라 사용자 지정할 수 있습니다. 또한 [Azure Portal을 사용하여 Azure 서비스에 대한 경고 만들기](../monitoring-and-diagnostics/insights-alerts-portal.md) 문서에서 설명한 대로 이러한 메트릭에 기반하여 경고를 작성할 수도 있습니다. 경고는 [Azure 메트릭 경고에 웹후크 구성(../ monitoring-and-diagnostics/insights-webhooks-alerts.md) 문서에서 설명한 대로 웹후크를 사용하여 알림 서비스에 보낼 수 있습니다. Azure 모니터링은 단일 구독을 지원합니다. 여러 구독에 대한 지원이 필요하거나 추가 기능이 필요한 경우 Operations Management Suite의 [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) 구성 요소에서 온-프레미스/클라우드 기반 인프라에 포괄적인 IT 관리 솔루션을 제공합니다. Azure Monitor의 데이터를 Log Analytics로 직접 라우팅할 수 있으므로 전체 환경에 대한 메트릭과 로그를 한 곳에서 볼 수 있습니다.

Operations Management Suite는 온-프레미스 인프라를 모니터링하는 데 권장되는 방법이지만 회사에서 인프라 모니터링에 사용하는 기존 솔루션도 사용할 수 있습니다.

### <a name="service-fabric-support-logs"></a>Service Fabric 지원 로그

Azure Service Fabric 클러스터에 대한 도움을 받기 위해 Microsoft 지원에 문의해야 하는 경우 지원 로그가 거의 항상 필요합니다. 클러스터가 Azure 내에서 호스팅되는 경우 클러스터를 만드는 과정의 일부로 이러한 로그를 자동으로 구성하여 수집합니다. 로그는 클러스터의 리소스 그룹에서 볼 수 있는 전용 저장소 계정에 저장됩니다. 저장소 계정에 고정된 이름은 없지만 계정에는 'fabric'으로 시작하는 Blob 컨테이너와 테이블이 있습니다. 클러스터가 독립형 클러스터인 경우 [독립 실행형 Azure Service Fabric 클러스터 만들기 및 관리](service-fabric-cluster-creation-for-windows-server.md) 및 [독립 실행형 Windows 클러스터에 대한 구성 설정](service-fabric-cluster-manifest.md)의 지침에 따라 이러한 로그 수집을 구성해야 합니다. 독립 실행형 Service Fabric의 경우 로그는 로컬 파일 공유로 보내야 합니다. 지원을 받으려면 이러한 로그가 **필요하지만** Microsoft 고객 지원 팀 외부의 사람이 사용할 수 있는 것은 아닙니다. Obi Wan Kenobi(신뢰할 수 있는 후원자)에 의한 영향력 있는 최고의 결과물을 사용하면 관심 있는 로그가 아니겠지만...

## <a name="instrument-your-code"></a>코드 계측

코드를 계측하는 것은 다른 측면 대부분의 서비스 모니터링에 대한 기반이 됩니다. 얼마나 많은 계측이 필요한지는 종종 놀라운 일이지만, 이 계측이 잘못된 것을 파악하는 유일한 방법이며 수정해야 할 것을 진단하는 유일한 방법이라고 생각하면 안 됩니다. 기술적으로는 가능하지만 디버거를 프로덕션 서비스에 연결하는 것은 드문 경우이므로 자세한 계측 데이터를 확보하는 것이 중요합니다. 이러한 대량의 정보를 생성할 때 로컬 노드에서 모든 이벤트를 전송하는 것은 많은 비용이 듭니다. 많은 서비스에서 다음 두 가지 전략을 사용하여 계측 데이터 양을 처리합니다.
* 모든 이벤트가 짧은 간격(일)으로 로컬 롤링 로그 파일에 저장되며 디버깅에 필요할 때만 수집됩니다. 일반적으로 자세한 진단을 위해 필요한 이벤트는 비용과 리소스 사용량을 줄이기 위해 노드에 남아 있습니다.
* 오류 이벤트, 하트비트 이벤트 또는 성능 이벤트와 같이 서비스 상태를 나타내는 이벤트는 모두 중앙의 리포지토리로 보내지며, 이 이벤트는 비정상 서비스에 대한 경고를 생성하는 데 사용될 수 있습니다.

코드를 자동으로 계측하는 솔루션이 있습니다. 이 제품들이 정상적으로 작동하는 동안 수동 계측이 거의 항상 필요합니다. 결국 응용 프로그램을 과학 수사 방식으로 디버그하는 데 필요한 정보가 충분히 있어야 합니다. 다음 섹션에서는 코드를 계측하는 여러 가지 방법과 다른 방법을 선택해야 하는 경우에 대해 설명합니다.

### <a name="eventsource"></a>EventSource

Visual Studio의 템플릿에서 Azure Service Fabric 솔루션을 만들면 EventSource 파생 클래스(*ServiceEventSource* 또는 *ActorEventSource*)가 생성됩니다. 이 클래스는 응용 프로그램이나 서비스에 적절한 추가 이벤트를 추가할 수 있는 템플릿을 제공합니다. EventSource 이름은 **고유해야** 하며 'MyCompany-&lt;solution&gt;-&lt;project&gt;'의 시작 문자열에서 이름을 변경해야 합니다. 동일한 이름을 사용하는 EventSource 정의가 여러 개 있으면 런타임 시 문제가 발생합니다. 정의된 각 이벤트에는 고유 식별자가 있어야 합니다. 식별자가 고유하지 않으면 런타임 오류가 발생합니다. 별도의 개발 팀 간에 충돌을 피하기 위해 종종 값의 범위가 식별자에 미리 할당됩니다. EventSource에 대한 자세한 내용은 [Vance의 블로그](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) 또는 [MSDN 설명서](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx)를 참조하세요.

#### <a name="using-structured-eventsource-events"></a>구조적 EventSource 이벤트 사용

다음 이벤트 각각은 특정 사용 사례(예: 서비스 유형이 등록되었음)에 대해 정의됩니다. 이러한 방식으로 메시지를 정의하면 데이터를 오류 텍스트와 함께 패키지로 포함할 수 있습니다. 이렇게 하면 지정된 속성의 이름이나 값을 기준으로 더 효율적으로 검색하고 필터링할 수 있습니다. 계측 출력을 구조화하면 더 쉽게 사용할 수 있지만 각 사용 사례마다 새 이벤트를 정의하는 데 더 많은 생각과 시간이 필요합니다. 일부 이벤트 정의는 전체 응용 프로그램에서 공유할 수 있습니다. 예를 들어 메서드 시작 또는 중지 이벤트는 응용 프로그램 내의 많은 서비스에서 재사용됩니다. 주문 시스템과 같은 도메인 관련 서비스에는 자체의 고유한 이벤트를 포함하는 CreateOrder 이벤트가 있을 수 있습니다. 종종 이 방법은 많은 이벤트를 생성하고 잠재적으로 프로젝트 팀 전체에서 식별자를 조정할 필요가 있습니다. Service Fabric의 EventSources 구조에 대한 전체 예제는 Party Cluster 샘플 내의 [PartyCluster.ApplicationDeployService.ServiceEventSource](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster/blob/master/src/PartyCluster.ApplicationDeployService/ServiceEventSource.cs)를 참조하세요.

```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
        private ServiceEventSource() : base() { }

        ...

        // ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceTypeRegisteredEventId = 3;
        [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
        public void ServiceTypeRegistered(int hostProcessId, string serviceType)
        {
            WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
        }

        // ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event and the code implementation of the event.
        private const int ServiceHostInitializationFailedEventId = 4;
        [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
        public void ServiceHostInitializationFailed(string exception)
        {
            WriteEvent(ServiceHostInitializationFailedEventId, exception);
        }
```
#### <a name="using-eventsource-generically"></a>일반적 EventSource 사용

특정 이벤트를 정의하는 것이 어려울 수 있기 때문에 많은 사람들은 일반적으로 정보를 문자열로 출력하는 공통 매개 변수 집합을 사용하는 이벤트를 거의 정의하지 않습니다. 구조적 측면 대부분이 손실되어 결과를 검색하고 필터링하기가 더 어려워집니다. 이 방법을 사용하면 일반적으로 로깅 수준에 해당하는 몇 가지 이벤트가 정의됩니다. 다음 코드 조각에서는 디버그 및 오류 메시지를 정의합니다.
```csharp
    [EventSource(Name = "MyCompany-VotingState-VotingStateService")]
    internal sealed class ServiceEventSource : EventSource
    {
        public static readonly ServiceEventSource Current = new ServiceEventSource();

        // Instance constructor is private to enforce singleton semantics
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
또한 구조적 계측과 제네릭 계측을 혼합하는 방법을 사용하면 효과적일 수 있습니다. 이 경우 구조적 계측은 오류 보고 및 메트릭 보고에 사용되지만, 제네릭 이벤트는 문제를 해결할 때 엔지니어가 사용하는 자세한 로깅에 사용할 수 있습니다.

### <a name="aspnet-core-logging"></a>ASP.NET Core 로깅

코드 계측 방법을 선택하는 것이 어려울 수 있습니다. 잘못 선택하여 다시 계측해야 하는 경우 코드베이스를 다시 방문하여 잠재적으로 불안정하게 만듭니다. 위험을 줄이기 위해 개발자는 ASP.NET Core에서 제공하는 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/)과 같은 계측 라이브러리를 선택할 수 있습니다. 이렇게 하면 [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) 인터페이스를 통해 원하는 공급자를 사용하면서 기존 코드에 미치는 영향을 최소화할 수 있습니다. 또 다른 좋은 점은 Windows 및 Linux의 .NET Core뿐만 아니라 전체 .NET 프레임워크에서도 코드를 사용할 수 있으므로 .NET 및 .NET Core에서 계측 코드를 표준화하는 기능을 제공한다는 것입니다.

#### <a name="how-to-use-microsoftextensionslogging-within-service-fabric"></a>Service Fabric 내에서 Microsoft.Extensions.Logging을 사용하는 방법

1. **Microsoft.Extensions.Logging** NuGet 패키지를 계측 중인 프로젝트에 추가합니다. 또한 모든 공급자 패키지를 추가하려고 합니다. 아래에서 타사 패키지에 대한 추가 작업을 수행하겠습니다. 자세한 내용은 [ASP.NET Core 로그인](https://docs.microsoft.com/aspnet/core/fundamentals/logging)을 참조하세요.

2. 'Microsoft.Extensions.Logging'에 대한 **using** 지시문을 서비스 파일에 추가합니다.

3. 서비스 클래스 내 private 변수 정의

    ```csharp
        private ILogger _logger = null;
    ```

4. 서비스 클래스의 생성자에서 다음을 추가합니다.
    
    ```csharp
        _logger = new LoggerFactory().CreateLogger<Stateless>();
    ```

5. 메서드에서 코드 계측을 시작합니다. 다음은 몇 가지 샘플입니다.
    
    ```csharp

        _logger.LogDebug("Debug level event from Microsoft.Logging");
        _logger.LogInformation("Informational level event from Microsoft.Logging");

        // In this variant, we're adding structured properties RequestName and Duration that has values MyRequest and the duration of the request.
        // More on why you'll want to do this later.
        _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);

    ```

#### <a name="using-other-logging-providers"></a>다른 로깅 공급자 사용

이 방법을 사용하는 타사 공급자가 있습니다. 세 가지 예제는 [SeriLog](https://serilog.net/), [NLog](http://nlog-project.org/) 및 [loggr](https://github.com/imobile3/Loggr.Extensions.Logging)입니다. 이들 각각은 ASP.Net Core 로깅에 플러그인할 수 있으며 별도로 사용할 수도 있습니다. SeriLog에는 로거에서 보낸 모든 메시지를 보강하여 서비스 이름, 유형 및 파티션 정보를 출력하는 데 유용할 수 있는 기능이 있습니다. ASP.NET Core 인프라에서 이 기능을 사용하려면 다음을 수행합니다.

1. **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Observable** NuGet 패키지를 프로젝트에 추가합니다. 또한 이 예제에서는 이 문서의 뒷부분에서 더 나은 방법을 보여 주는 **SeriLog.Sinks.Literate**도 추가합니다.
2. SeriLog에 LoggerConfiguration 및 Logger 인스턴스를 만듭니다.

    ```csharp

        Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();

    ```

3. SeriLog.ILogger 인수를 서비스 생성자에 추가하고 새로 만들어진 로거를 전달합니다.
    
    ```csharp

        ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();

    ```

4. 서비스 생성자에서 다음을 추가합니다. 이 예제에서는 서비스의 ServiceTypeName, ServiceName, PartitionId 및 InstanceId 속성에 대한 속성 보강자(property enricher)를 만듭니다. 또한 ASP.NET Core 로깅 팩터리에 코드를 추가하여 Microsoft.Extensions.Logging.ILogger를 코드에 사용할 수도 있습니다.
    
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

5. SeriLog 없이 ASP.NET Core를 사용할 때와 동일한 코드를 계측합니다.

> [!NOTE] 
> Service Fabric은 단일 프로세스 내에서 동일한 서비스 유형의 여러 인스턴스를 호스팅할 수 있으므로 정적 Log.Logger를 이 방법과 함께 사용하는 것은 바람직하지 않습니다. 이는 실행 중인 모든 인스턴스에 대해 표시된 값이 속성 보강자의 마지막 작성자에 있다는 것을 의미하며, _logger 변수가 서비스 클래스의 private 멤버 변수가 되는 한 가지 이유입니다. 또한 _logger는 서비스 전체에서 사용할 수 있는 공통 코드에서 사용할 수 있어야 합니다. 

### <a name="which-one-should-i-use"></a>어느 것을 사용해야 합니까?

응용 프로그램이 성능에 깊이 관여하는 경우 **일반적으로** ASP.NET Core 로깅이나 타사 솔루션을 사용하는 것보다 EventSource에서 더 적은 리소스를 사용하고 더 효율적으로 수행할 수 있기 때문에 EventSource가 가장 좋은 방법입니다.  대부분의 서비스에서는 문제가 되지 않지만 성능 지향적인 서비스인 경우 EventSource를 사용하는 것이 더 나은 선택일 수 있습니다. 구조적 로깅과 동일한 이점을 얻으려면 엔지니어링 팀의 많은 투자가 EventSource에 필요합니다. 프로젝트를 위해 선택할 대상을 결정하는 가장 좋은 방법은 각각의 방법에서 수행할 작업에 대한 빠른 프로토타입 작업을 수행한 다음 사용자의 요구에 가장 적합한 방법을 선택하는 것입니다.

## <a name="event-and-log-collection"></a>이벤트 및 로그 수집

### <a name="azure-diagnostics"></a>Azure 진단

Azure에서는 Azure 모니터링이 제공하는 기능 외에도 각 서비스의 이벤트를 한 곳으로 수집하는 수단을 제공합니다. [Windows](service-fabric-diagnostics-how-to-setup-wad.md) 및 [Linux](service-fabric-diagnostics-how-to-setup-lad.md)의 이벤트 수집을 구성하는 방법을 보여주는 두 가지 문서가 있습니다. 이러한 문서에서는 이벤트 데이터를 수집하여 Azure 저장소로 보냅니다. 진단을 사용하도록 설정하면 포털 또는 Resource Manager 템플릿에서 이 작업을 쉽게 수행할 수 있습니다. 이 기능을 작동하면 Service Fabric에서 자동으로 생성하는 다음과 같은 몇 가지 이벤트 원본을 수집합니다.

- Reliable Actor 프로그래밍 모델을 사용하는 경우 EventSource 이벤트 및 성능 카운터를 수집합니다. 관련 이벤트는 [Reliable Actors에 대한 진단 및 성능 모니터링](service-fabric-reliable-actors-diagnostics.md) 문서에서 열거하고 있습니다.
- Reliable Services 프로그래밍 모델을 사용하는 경우 EventSource 이벤트를 수집합니다. 관련 이벤트는 [상태 저장 Reliable Services의 진단 기능](service-fabric-reliable-services-diagnostics.md) 문서에서 열거하고 있습니다.
- 시스템 이벤트를 ETW 이벤트로 내보냅니다. 이 범주의 일환으로 Service Fabric에서 내보내는 많은 이벤트가 있으며, 여기에는 서비스 배치, 시작/중지 이벤트가 포함됩니다. 내보낸 이벤트를 확인하는 가장 좋은 방법은 로컬 컴퓨터에서 실행할 때 [Visual Studio의 진단 뷰어](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)를 사용하는 것입니다. 이러한 이벤트는 네이티브 ETW 이벤트이므로 수집할 수 있는 방법에 몇 가지 제한이 있습니다
- Service Fabric 5.4 릴리스부터 상태 및 로드 메트릭 이벤트가 공개되고 있습니다. 이를 통해 기록 보고 및 경고에 이러한 이벤트 수집을 사용할 수 있습니다. 이러한 이벤트도 네이티브 ETW 이벤트이며 수집할 수 있는 방법에 몇 가지 제한이 있습니다.

구성되면 이러한 이벤트는 클러스터를 만들 때 만들어진 Azure Storage 계정 중 하나에 나타나며, 진단을 사용하도록 설정했다고 가정합니다. 테이블의 이름은 *WADServiceFabricReliableActorEventTable*, *WADServiceFabricReliableServiceEventTable* 및 *WADServiceFabricSystemEventTable*입니다. 기본적으로 상태 이벤트는 추가되지 않으며 Resource Manager 템플릿을 수정해야 합니다. 자세한 내용은 [Azure 진단을 사용하여 로그 수집](service-fabric-diagnostics-how-to-setup-wad.md)을 참조하세요.

이 문서에서는 Azure Storage에 사용자 지정 이벤트를 가져오는 방법도 설명합니다. VM의 성능 카운터 또는 기타 모니터링 정보를 Azure 진단에 구성하는 방법에 대한 기존의 Azure 진단 관련 문서도 Service Fabric 클러스터에 적용됩니다. 예를 들어 Azure 테이블 저장소를 대상으로 원하지 않는 경우 [Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure 진단 데이터 스트리밍](../event-hubs/event-hubs-streaming-azure-diags-data.md) 문서가 있습니다. 이벤트가 Event Hub에 있으면 사용자가 읽고 선택한 위치로 보낼 수 있습니다. 통합과 관련하여 [Application Insights를 통한 Azure 진단 정보](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/)(영문) 문서도 있습니다.

Azure 진단을 사용하는 단점 중 하나는 Resource Manager 템플릿을 사용하여 구성되기 때문에 VMSS 수준에서만 발생한다는 것입니다. VMSS는 Service Fabric의 노드 형식에 해당합니다. 즉 해당 형식의 노드에서 실행될 수 있는 모든 응용 프로그램과 서비스에 대해 각각의 노드 형식을 구성해야 합니다. 그러면 구성된 응용 프로그램과 서비스의 수에 따라 많은 수의 EventSources가 있을 수 있습니다. 또한 응용 프로그램의 구성이 변경될 때마다 Resource Manager 배포를 수행해야 합니다. 모니터링 구성은 서비스 구성과 함께 전달되는 것이 가장 이상적입니다.

Azure 진단은 Azure에 배포된 Service Fabric 클러스터에서만 작동하지만 Windows 및 Linux 클러스터 모두에서 작동합니다.

### <a name="eventflow"></a>EventFlow

[EventFlow는 Visual Studio 팀에서 릴리스](https://github.com/Azure/diagnostics-eventflows)되었으며 이벤트를 노드에서 하나 이상의 모니터링 대상으로 라우팅하는 메커니즘을 제공합니다. 서비스 프로젝트에 NuGet 패키지로 포함되어 있기 때문에 EventFlow의 코드와 구성이 서비스와 함께 이동하므로 Azure 진단에 대해 언급된 노드별 구성 문제가 제거됩니다. EventFlow는 서비스 프로세스 내에서 실행되며 구성된 출력에 직접 연결됩니다. 이러한 직접 연결로 인해 EventFlow는 서비스의 Azure, 컨테이너 또는 온-프레미스 배포에서 작동합니다. 각 EventFlow 파이프라인은 외부로 연결하기 때문에 동일한 노드에서 많은 복제본을 실행하는 경우 주의해야 합니다. 많은 프로세스를 호스팅하는 경우 아웃바운드 연결이 많이 생깁니다! 이 경우 ServiceType의 모든 복제본이 동일한 프로세스 내에서 실행되어 아웃바운드 연결 수를 제한하므로 Service Fabric 응용 프로그램에 별로 문제가 되지 않습니다. EventFlow는 이벤트 필터링도 제공하므로 지정된 필터와 일치하는 이벤트만 보냅니다. Service Fabric과 함께 EventFlow를 사용하는 방법에 대한 자세한 내용은 [Azure Service Fabric 서비스 프로세스에서 직접 로그 수집](service-fabric-diagnostic-collect-logs-without-an-agent.md)을 참조하세요.

> [!NOTE]
> Service Fabric의 향후 릴리스에서는 EventSource 호스트 응용 프로그램을 사용하여 ETW 기반 입력, 노드 수준 메트릭 수집 및 롤링 로그 파일 지원을 수신 대기할 수 있습니다.

EventFlow는 다음과 같이 매우 쉽게 사용할 수 있습니다.
1. NuGet 패키지를 서비스 프로젝트에 추가합니다.
2. 서비스의 **Main** 함수 내에 EventFlow 파이프라인을 만들고 출력을 구성합니다. 여기서는 SeriLog를 출력으로 사용하고 있습니다.
    ```csharp

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

                    // The ServiceManifest.XML file defines one or more service type names.
                    // Registering a service maps a service type name to a .NET type.
                    // When Service Fabric creates an instance of this service type,
                    // an instance of the class is created in this host process.

                    ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless).Name);
                    
                    // Prevents this host process from terminating so services keep running.
                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }

    ```
3. 서비스의 PackageRoot(*eventFlowConfig.json*라는 구성 폴더)에 파일을 만듭니다. 파일 내 구성은 다음과 같습니다.
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
구성에는 Service Fabric에서 만든 두 개의 EventSource 기반 원본 및 서비스에 대한 EventSource라는 두 개의 입력이 정의되어 있습니다. ETW를 사용하는 시스템 수준 및 상태 이벤트는 EventFlow에서 사용할 수 없습니다. 이는 ETW 원본을 수신 대기하는 데에는 높은 수준의 권한이 필요하지만 서비스는 높은 권한으로 실행하면 안되기 때문입니다. 다른 입력은 SeriLog이며 **Main** 메서드에서 발생하는 구성입니다.  또한 일부 필터가 적용되며, 첫 번째 필터는 이벤트 수준의 자세한 정보인 모든 이벤트를 삭제하도록 EventFlow에 지시합니다. 잠시 후에 다른 필터 정의를 살펴보겠습니다. Visual Studio 내에서 출력 창에 작성하는 두 개의 출력도 표준 출력으로 구성되어 있습니다. 다른 출력은 ApplicationInsights이며, 반드시 계측 키를 추가해야 합니다.

4. 마지막 단계는 코드를 계측하는 것입니다. 이 예제에서는 몇 가지 다른 방법으로 RunAsync를 예로 들어 계측하겠습니다. 아래 코드에서는 SeriLog를 계속 사용하고 있으며 사용된 구문 중 일부는 SeriLog에만 해당됩니다. 선택한 로깅 솔루션의 특정 기능을 알고 있어야 합니다. 세 가지 이벤트, 즉 디버그 수준 이벤트와 두 개의 정보 이벤트가 생성됩니다. 두 번째 이벤트는 요청 기간을 추적합니다. 위의 EventFlow 구성을 사용하면 디버그 수준 이벤트가 출력으로 전달되지 않아야 합니다.

    ```csharp
        Stopwatch sw = Stopwatch.StartNew();

        while (true)
        {
            cancellationToken.ThrowIfCancellationRequested();

            sw.Restart();

            // Delay a random interval to provide a more interesting request duration.
            await Task.Delay(TimeSpan.FromMilliseconds(DateTime.Now.Millisecond), cancellationToken);

            ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);
            _logger.LogDebug("Debug level event from Microsoft.Logging");
            _logger.LogInformation("Informational level event from Microsoft.Logging");
            _logger.LogInformation("{RequestName} {Duration}", "MyRequest", sw.ElapsedMilliseconds);
        }
    ```

Application Insights에서 이벤트를 보려면 Azure Portal을 열고 ApplicationInsights 리소스로 이동합니다. 그런 다음 왼쪽 위에서 "검색"을 클릭하면 이벤트가 표시됩니다.

![Application Insights 검색 보기의 이벤트](./media/service-fabric-diagnostics-overview/ai-search-events.png)

그림의 아래쪽에 추적이 있습니다. 두 이벤트만 볼 수 있으며, 디버그 수준 이벤트는 EventFlow에서 삭제했습니다. 따라서 추적 위의 요청 항목은 무엇입니까? 세 번째 '_logger' 계측 줄이며, Application Insights 내에서 이벤트를 요청 메트릭으로 변환했음을 나타냅니다. 이제 '메타데이터' 형식의 필터 정의로 돌아가 보겠습니다. 여기서는 'MyRequest' 값을 포함한 'RequestName' 속성과 요청의 기간(밀리 초)을 포함한 'Duration' 속성을 사용하는 이벤트를 선언하고 있습니다. Application Insights의 요청 이벤트에서 이 이벤트를 볼 수 있습니다. 동일한 방법이 EventSource를 포함하여 지원되는 모든 EventFlow 입력에서 작동합니다.

클러스터가 정책 상의 이유로 클라우드 기반 솔루션에 연결할 수 없는 독립 실행형 클러스터인 경우 EventFlow는 탄력적 검색을 출력으로 지원하지만 다른 출력을 작성할 수 있으며 끌어오기 요청을 사용하는 것이 좋습니다. ASP.NET Core 로깅을 위한 타사 공급자 중 일부는 온-프레미스 설치를 지원하는 솔루션도 제공하고 있습니다.

## <a name="azure-service-fabric-health-and-load-reporting"></a>Azure Service Fabric 상태 및 로드 보고

Service Fabric에는 다음 몇 가지 문서에서 자세히 설명하는 자체 상태 모델이 있습니다.
- [Service Fabric 상태 모니터링 소개](service-fabric-health-introduction.md)
- [서비스 상태 보고 및 확인](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [사용자 지정 서비스 패브릭 상태 보고서 추가](service-fabric-report-health.md)
- [서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

상태 모니터링은 서비스 운영의 다양한 측면에서 매우 중요합니다. 가장 중요한 것은 Service Fabric에서 명명된 응용 프로그램 업그레이드를 수행할 때 서비스의 업그레이드 도메인 각각을 업그레이드하고 고객이 사용할 수 있게 되었으면 다음 업그레이드 도메인으로 이동하기 전에 상태 검사를 통과해야 한다는 것입니다. 양호한 상태를 달성할 수 없으면 배포를 롤백하여 응용 프로그램을 알려진 양호한 상태로 유지합니다. 일부 고객은 서비스를 롤백할 수 있기 전에 영향을 받았지만 대부분의 고객에게는 문제가 발생하지 않았습니다. 또한 비교적 빨리 해결되었으며 운영자가 조치를 취할 때까지 기다릴 필요가 없었습니다. 코드에 통합된 상태 검사가 많을수록 배포 문제에 대한 서비스 복원력이 더욱 높아집니다.

서비스 상태의 또 다른 측면은 서비스의 메트릭 보고입니다. 메트릭은 리소스 사용량을 조정하는 데 사용되고 시스템 상태 표시기로 사용할 수 있기 때문에 Service Fabric에서 중요합니다. 응용 프로그램에 많은 서비스가 있고 각 인스턴스에서 RPS(초당 요청 수) 메트릭을 보고한다고 가정해 보겠습니다. 서비스 중 하나가 다른 서비스보다 많은 리소스를 사용하는 경우 Service Fabric에서 클러스터 주변의 서비스 인스턴스를 이동하고 심지어는 리소스 사용률을 유지하려고도 합니다. 이러한 작동 방식에 대한 자세한 설명은 [메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리](service-fabric-cluster-resource-manager-metrics.md) 문서에 있습니다.

또한 메트릭을 사용하면 서비스를 수행하는 방법을 파악할 수 있으며 시간이 경과함에 따라 서비스가 예상된 매개 변수 내에서 작동하는지 확인할 수 있습니다. 예를 들어 추세에 기반하여 월요일 오전 9시의 평균 RPS가 1000인 경우 RPS가 500 미만이거나 1500 이상일 때 경고하는 상태 보고서를 설정할 수 있습니다. 모든 것이 완벽하게 좋을 수 있지만 고객이 매우 만족스럽게 경험하고 있는지도 확인할 만한 가치가 있습니다. 서비스에서 상태에 대해 보고할 수 있는 메트릭 집합을 정의할 수 있지만 클러스터의 리소스 분산에 영향을 주지 않도록 메트릭 가중치를&0;으로 설정합니다. 가중치가&0;인 모든 메트릭을 시작하고 이로 인해 클러스터의 리소스 분산에 미치는 영향을 확실히 이해할 때까지 가중치를 높이지 않는 것이 좋습니다.

> [!TIP]
> 서비스 인스턴스가 이동되는 이유를 이해하기 어려울 수 있으므로 가중치가 높은 메트릭을 너무 많이 사용하지 않도록 주의하세요. 그렇지 않을 경우 몇 가지 메트릭은 먼 길을 갈 수 있습니다!

메트릭 및 상태 보고서의 후보는 응용 프로그램의 상태 및 성능을 나타낼 수 있는 항목입니다. CPU 성능 카운터는 노드를 활용하는 수준을 알려주지만 단일 노드에서 여러 서비스가 실행될 수 있기 때문에 특정 서비스가 정상적으로 작동하는지 여부는 실제로 알려주지 않습니다. 반면 RPS, 처리된 항목 또는 요청 대기 시간과 같은 메트릭은 모두 특정 서비스의 상태를 나타낼 수 있습니다.

상태를 보고하려면 다음과 같은 코드를 추가합니다.
```csharp
        if (!result.HasValue)
        {
            HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
            this.Partition.ReportInstanceHealth(healthInformation);
        }
```

메트릭을 보고하려면 서비스에 다음과 같은 코드를 추가합니다.
```csharp
        this.ServicePartition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
```

## <a name="watchdogs"></a>Watchdog

Watchdog은 서비스 전체에서 상태와 부하를 감시하고 상태 모델 계층 구조 상의 모든 항목에 대한 상태를 보고할 수 있는 별도의 서비스입니다. 이렇게 하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. Watchdog은 사용자가 개입하지 않아도 알려진 조건에 대한 수정 작업을 수행할 수 있는 코드를 호스팅하기에 좋은 출발점이기도 합니다.

## <a name="visualization-analysis-and-alerting"></a>시각화, 분석 및 경고

모니터링의 마지막 부분은 이벤트 스트림을 시각화하고, 서비스 성능을 보고하고, 문제가 감지되면 경고하는 것입니다. 이러한 측면의 모니터링에 사용되는 다양한 솔루션이 있으며 Application Insights 및 OMS를 사용하여 이벤트 스트림을 기반으로 하여 경고할 수 있습니다. PowerBI 또는 타사 솔루션(예: [Kibana](https://www.elastic.co/products/kibana) 또는 [Splunk](https://www.splunk.com/))을 사용하여 데이터를 시각화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 진단을 사용하여 로그를 수집하는 방법](service-fabric-diagnostics-how-to-setup-wad.md)
* [Azure Service Fabric 서비스 프로세스에서 직접 로그 수집](service-fabric-diagnostic-collect-logs-without-an-agent.md)
*  [메트릭을 사용하여 Service Fabric에서 리소스 부하 및 소비 관리](service-fabric-cluster-resource-manager-metrics.md)




<!--HONumber=Feb17_HO2-->


