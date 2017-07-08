---
title: "Azure Service Fabric 서비스 프로세스에서 직접 로그 수집 | Microsoft Azure"
description: "Service Fabric 응용 프로그램이 Azure Diagnostics 에이전트 없이도 Azure Application Insights 또는 Elasticsearch 등의 중앙 위치에 직접 로그를 보낼 수 있음을 설명합니다."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz
redirect_url: /azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 08f7b57985382f2abbb90ba1e13a30f40b38917f
ms.contentlocale: ko-kr
ms.lasthandoff: 06/15/2017


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Azure Service Fabric 서비스 프로세스에서 직접 로그 수집 
## <a name="in-process-log-collection"></a>프로세스에서 로그 수집
로그 원본과 대상의 집합이 작고, 자주 변경되지 않으며, 원본과 대상 간의 매핑이 단순하다면 [Azure Diagnostics 확장](service-fabric-diagnostics-how-to-setup-wad.md)을 사용한 응용 프로그램 로그 수집이 **Azure Service Fabric** 서비스에 좋은 옵션입니다.  그렇지 않은 경우 서비스가 중앙 위치에 직접 로그를 보내도록 할 수도 있습니다. 이 프로세스를 **프로세스에서 로그 수집**이라고 하며 다음과 같은 몇 가지 장점이 있습니다.

* *쉬운 구성 및 배포*

    * 진단 데이터 수집 구성이 서비스 구성의 일부입니다. 응용 프로그램 나머지와 항상 "동기화" 상태로 쉽게 유지할 수 있습니다.
    * 응용 프로그램당 또는 서비스당 구성은 쉽게 달성할 수 있습니다.
        * 에이전트 기반 로그 수집이 일반적으로 추가 관리 작업 및 잠재적인 오류 소스인 진단 에이전트의 별도 배포 및 구성이 필요합니다. 종종 가상 컴퓨터(노드)당 하나의 에이전트 인스턴스만 허용되며, 해당 노드에서 실행 중인 모든 응용 프로그램과 서비스 간에 에이전트 구성을 공유합니다. 

* *유연성*
   
    * 응용 프로그램은 대상 데이터 저장소 시스템을 지원하는 클라이언트 라이브러리가 있는 한 데이터를 필요한 어느 곳이든 보낼 수 있습니다. 새로운 대상을 원하는 대로 추가할 수 있습니다.
    * 복잡한 캡처, 필터링 및 데이터 집계 규칙을 구현할 수 있습니다.
    * 에이전트 기반 로그 수집은 종종 에이전트가 지원하는 데이터 싱크에 의해 제한됩니다. 일부 에이전트는 확장할 수 있습니다.

* *내부 응용 프로그램 데이터 및 컨텍스트에 대한 액세스*
   
    * 응용 프로그램/서비스 프로세스 내에서 실행되는 진단 하위 시스템은 컨텍스트 정보와 함께 추적을 쉽게 보강할 수 있습니다.
    * 에이전트 기반 로그 수집에서 ETW(Windows용 이벤트 추적)와 같은 일부 프로세스간 통신 메커니즘을 통해 에이전트에 데이터를 보내야 합니다. 이 메커니즘에 따라 추가 제한 사항이 적용될 수 있습니다.

두 가지 수집 방법을 결합하여 양쪽의 이점을 누릴 수도 있습니다. 실제로 많은 응용 프로그램에는 이 방법이 최적의 솔루션일 수 있습니다. 에이전트 기반 수집은 전체 클러스터와 개별 클러스터 노드와 관련한 로그를 수집하기 위한 자연스러운 솔루션입니다. 프로세스에서 로그 수집보다 훨씬 안정적으로 서비스 시작 문제와 충돌을 진단할 수 있습니다. 또한 Service Fabric 클러스터 안에서 실행되는 많은 서비스에서는 자체적으로 프로세스에서 로그 수집을 수행하는 각 서비스로 인해 클러스터로부터 나가는 연결이 매우 많아집니다. 나가는 연결이 많으면 네트워크 하위 시스템과 로그 대상 모두에 부담이 초래됩니다. [**Azure Diagnostics**](../cloud-services/cloud-services-dotnet-diagnostics.md) 같은 에이전트는 여러 서비스로부터 데이터를 수집하고 몇 번의 연결을 통해 모든 데이터를 보내므로 처리량이 향상됩니다. 

이 문서에서는 [**EventFlow 오픈 소스 라이브러리**](https://github.com/Azure/diagnostics-eventflow)를 사용하여 프로세스에서 로그 수집을 설정하는 방법을 살펴봅니다. 같은 용도로 다른 라이브러리를 사용할 수도 있지만 EventFlow에는 Service Fabric 서비스를 지원하고 프로세스에서 로그 수집을 위해 특별히 설계된 장점이 있습니다. [**Azure Application Insights**](https://azure.microsoft.com/services/application-insights/)를 로그 대상으로 사용합니다. [**Event Hubs**](https://azure.microsoft.com/services/event-hubs/) 또는 [**Elasticsearch**](https://www.elastic.co/products/elasticsearch) 같은 다른 대상도 지원됩니다. EventFlow 구성 파일에서 적합한 NuGet 패키지를 설치하고 대상을 구성하기만 하면 됩니다. Application Insights 이외의 로그 대상에 대한 자세한 내용은 [EventFlow 설명서](https://github.com/Azure/diagnostics-eventflow)를 참조하세요.

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Service Fabric 서비스 프로젝트에 EventFlow 라이브러리 추가
EventFlow 이진을 NuGet 패키지 집합으로 사용할 수 있습니다. EventFlow를 Service Fabric 서비스 프로젝트에 추가하려면 Solution Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 “NuGet 패키지 관리”를 선택합니다. "찾아보기" 탭으로 전환하고 "`Diagnostics.EventFlow`"를 검색합니다.

![Visual Studio NuGet 패키지 관리자 UI에서 EventFlow NuGet 패키지][1]

EventFlow를 호스팅하는 서비스는 응용 프로그램 로그에 대해 원본과 대상에 따라 적합한 패키지를 포함해야 합니다. 다음 패키지를 추가합니다. 

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 
    * (서비스의 EventSource 클래스 및 표준 EventSources에서 데이터 캡처, 예: *Microsoft-ServiceFabric-Services* 및 *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` 
    * (여기서는 Azure Application Insights 리소스에 로그를 보낼 것임)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (Service Fabric 서비스 구성에서 EventFlow 파이프라인의 초기화를 활성화하고 진단 데이터를 Service Fabric 상태 보고서로 보내 모든 문제를 보고)

> [!NOTE]
> `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` 패키지에서는 서비스 프로젝트가 .NET Framework 4.6 이상을 대상으로 해야 합니다. 이 패키지를 설치하기 전에 프로젝트 속성에서 적합한 대상 프레임워크를 설정해야 합니다. 

모든 패키지를 설치한 다음에는 서비스에서 EventFlow를 구성하여 활성화합니다.

## <a name="configuring-and-enabling-log-collection"></a>로그 수집 구성 및 사용
로그 보내기를 담당하는 EventFlow 파이프라인은 구성 파일에 저장된 사양으로부터 만들어집니다. `Microsoft.Diagnostics.EventFlow.ServiceFabric`패키지는 시작 EventFlow 구성 파일을 `PackageRoot\Config` 솔루션 폴더에 설치합니다. 이 파일 이름은 `eventFlowConfig.json`입니다. 기본 서비스 `EventSource` 클래스에서 데이터를 수집하고 Application Insights 서비스로 데이터를 보내기 위해 이 구성 파일을 수정해야 합니다. 

> [!NOTE]
> 여기서는 사용자가 **Azure Application Insights** 서비스에 대해 잘 알고 있고 Service Fabric 서비스의 모니터링에 사용할 Application Insights 리소스가 있다고 가정합니다. 자세한 내용은 [Application Insights 리소스 만들기](../application-insights/app-insights-create-new-resource.md)를 참조하세요.

편집기에서 `eventFlowConfig.json` 파일을 열고 다음과 같이 콘텐츠를 변경합니다. 주석에 따라 ServiceEventSource 이름 및 Application Insights 계측 키를 변경합니다.  

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> 서비스의 ServiceEventSource 이름은ServiceEventSource 클래스에 적용된 `EventSourceAttribute`의 Name 속성 값입니다. 모두 `ServiceEventSource.cs` 파일에서 지정하며 서비스 코드의 일부입니다. 예를 들어 다음 코드 조각에서 ServiceEventSource의 이름은 *MyCompany-Application1-Stateless1*입니다.
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

`eventFlowConfig.json` 파일은 서비스 구성 패키지의 일부입니다. 이 파일에 대한 변경 사항은 서비스의 전체 또는 구성 전용 업그레이드에 포함될 수 있습니다. 업그레이드 실패 시 Service Fabric 업그레이드 상태 확인과 자동 롤백의 대상이 됩니다. 자세한 내용은 [Service Fabric 응용 프로그램 업그레이드](service-fabric-application-upgrade.md)를 참조하세요.

마지막 단계는 `Program.cs` 파일에 있는 서비스 시작 코드에서 EventFlow 파이프라인을 인스턴스화하는 것입니다. 다음 예제에서는 EventFlow 관련 추가 `****`로 시작하는 주석으로 표시됩니다.

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

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
}
```

`ServiceFabricDiagnosticsPipelineFactory`의 `CreatePipeline` 메서드 매개 변수 형태로 전달되는 이름은 EventFlow 로그 수집 파이프라인을 나타내는 *상태 개체*의 이름입니다. EventFlow에서 오류가 발생하고 Service Fabric 상태 하위 시스템을 통해 보고할 경우 이 이름을 사용합니다.

## <a name="verification"></a>확인
서비스를 시작하고 Visual Studio의 출력 창에서 디버그를 관찰합니다. 서비스를 시작한 후 서비스에서 "Application Insights 원격 분석" 레코드를 보내면 증거를 확인하기 시작합니다. 웹 브라우저를 열고 Application Insights 리소스로 이동합니다. "검색" 탭(기본 "개요" 블레이드의 맨 위)을 엽니다. 잠깐 지연된 후 Application Insights 포털에서 추적이 시작됩니다.

![Service Fabric 응용 프로그램 로그를 표시하는 Application Insights 포털 ][2]

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 서비스 진단 및 모니터링에 대한 자세한 정보](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [EventFlow 설명서](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png

