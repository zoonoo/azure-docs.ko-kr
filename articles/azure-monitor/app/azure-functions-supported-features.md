---
title: Azure 애플리케이션 Insights-지원 되는 Azure Functions 기능
description: Azure Functions에 대한 Application Insights 지원 기능
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: reference
author: TimothyMothra
ms.author: tilee
ms.date: 4/23/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 5688bd92e63016e195726488bd9ff9ed039c5795
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928412"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Azure Functions에 대한 Application Insights 지원 기능

Azure Functions는 ILogger 인터페이스를 통해 사용할 수 있는 Application Insights와의 [기본 제공 통합](../../azure-functions/functions-monitoring.md)을 제공합니다. 다음은 현재 지원되는 기능 목록입니다. Azure Functions 가이드에서 [시작](../../azure-functions/functions-monitoring.md#enable-application-insights-integration) 방법을 검토하세요.

## <a name="supported-features"></a>지원되는 기능

| Azure Function                       | V1                | V2(Ignite 2018)  | 
|-----------------------------------    |---------------    |------------------ |
| **Application Insights .NET SDK**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **자동 컬렉션**        |                 |                   |               
| &bull; 요청                     | yes             | yes               | 
| &bull; 예외                   | yes             | yes               | 
| &bull; 성능 카운터         | yes             | yes               |
| &bull; 종속성                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; ServiceBus|                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; EventHub  |                 | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | yes               | 
| | | | 
| **지원되는 기능**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | yes             | yes               | 
| &nbsp;&nbsp;&nbsp;&mdash; 컨트롤 채널 보호|                 | yes               | 
| &bull; 샘플링                     | yes             | yes               | 
| &bull; 하트비트                   |                 | yes               | 
| | | | 
| **상관 관계**                       |                   |                   |               
| &bull; ServiceBus                     |                   | yes               | 
| &bull; EventHub                       |                   | yes               | 
| | | | 
| **구성 가능 여부**                      |                   |                   |           
| &bull;완전히 구성 가능합니다.<br/>지침을 보려면 [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852)를 참조하세요.<br/>모든 옵션에 대해서는 [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)를 참조하세요.               |                   | yes                   | 


## <a name="performance-counters"></a>성능 카운터

성능 카운터의 자동 수집은 Windows 머신에서만 작동합니다.


## <a name="live-metrics--secure-control-channel"></a>라이브 메트릭 및 컨트롤 채널 보호

지정한 사용자 지정 필터 조건이 Application Insights SDK의 라이브 메트릭 구성 요소에 다시 전송됩니다. 필터는 customerid와 같은 잠재적으로 중요한 정보를 포함할 수 있습니다. 비밀 API 키를 사용해 채널 보안을 유지할 수 있습니다. 해당 지침은 [컨트롤 채널 보호](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel)를 참조하세요.

## <a name="sampling"></a>샘플링

Azure Functions를 사용하면 기본적으로 구성에서 샘플링을 사용하도록 설정할 수 있습니다. 자세한 내용은 [샘플링 구성](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling)을 참조하세요.

프로젝트에서 Application Insights SDK에 대 한 종속성을 사용 하 여 수동 원격 분석 추적을 수행 하는 경우 샘플링 구성이 함수의 샘플링 구성과 다를 경우 이상한 동작이 발생할 수 있습니다. 

함수와 동일한 구성을 사용 하는 것이 좋습니다. **V2 함수**를 사용 하면 생성자에서 종속성 주입을 사용 하 여 동일한 구성을 가져올 수 있습니다.

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
