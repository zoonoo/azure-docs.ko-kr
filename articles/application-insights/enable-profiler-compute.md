---
title: "Azure Compute 응용 프로그램에 Application Insights Profiler를 사용하도록 설정 | Microsoft Docs"
description: "Azure Compute에서 실행 중인 응용 프로그램에서 Profiler를 설정하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Azure Virtual Machines, Service Fabric 및 Cloud Services에서 Application Insights Profiler를 사용하도록 설정

이 연습에는 Azure Compute 리소스에서 호스팅하는 ASP.NET 응용 프로그램에서 Azure Application Insights Profiler를 사용하도록 설정하는 방법을 보여 줍니다.  
예제에는 Azure Virtual Machines, Azure Virtual Machine Scale Sets, Azure Service Fabric 및 Azure Cloud Services에 대한 지원이 포함됩니다.  
예제는 [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 배포 모델을 지원하는 템플릿이 필요합니다.  


## <a name="overview"></a>개요

다음 다이어그램에서는 Azure 리소스에 대해 Profiler가 작동하는 방식을 보여 줍니다. 예로 Azure Virtual Machine을 사용합니다.

![개요](./media/enable-profiler-compute/overview.png)


Profiler를 완전히 사용하려면 다음 세 위치에 작업 구성을 수행해야 합니다.

1. Application Insights 인스턴스 포털 블레이드
2. 응용 프로그램 소스 코드(예: ASP.NET 웹 응용 프로그램)
3. 환경 배포 정의 소스 코드(예: 가상 컴퓨터 배포 템플릿 json 파일)


## <a name="configure-the-application-insights-instance"></a>Application Insights 인스턴스 구성

사용하려는 Application Insights 인스턴스에 대해 Azure Portal 블레이드를 만들거나 방문하고 해당 계측 키를 기록해 둡니다. 다른 구성 단계에 유용할 것입니다.

  ![키의 위치](./media/enable-profiler-compute/CopyAIKey.png)

이 인스턴스는 응용 프로그램이 각 요청에 원격 분석 데이터를 보내도록 구성된 것과 동일해야 합니다.
또한, Profiler 결과도 이 인스턴스에서 사용 가능할 수 있게 됩니다.  

Azure Portal에서 [프로파일러 활성화](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)에 따라 Profiler에 대한 AI 인스턴스 구성을 완료하세요. 이 연습을 위해 웹앱을 연결할 필요는 없으며, Profiler가 Portal에서 사용하도록 설정되어 있는지 확인하면 됩니다.


## <a name="configure-the-application-source-code"></a>응용 프로그램 소스 코드 구성

원격 분석 데이터를 각 `Request` 작업의 Application Insights 인스턴스에 보내도록 응용 프로그램을 구성해야 합니다.  

1. 응용 프로그램 프로젝트에 [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started)를 추가합니다. nuget 패키지 버전이 다음과 같은지 확인하세요.  
  - ASP.NET 응용 프로그램: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 이상 버전
  - ASP.NET Core 응용 프로그램: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 이상 버전
  - 기타 .NET 및 .NET 응용 프로그램(예: Service Fabric 상태 비저장 서비스, 클라우드 서비스 작업자 역할): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 또는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 이상  

2. 응용 프로그램이 ASP.NET 또는 ASP.NET Core 응용 프로그램(예: Cloud Services 작업자 역할, Service Fabric 상태 비저장 API)이 *아닌* 경우 다음 추가 계측 설정이 필요합니다.  

  2.1. 응용 프로그램 코드를 응용 프로그램 수명의 초기 지점에 추가합니다.  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  이 전역 계측 키 구성에 대한 자세한 내용은 [Application Insights를 통해 Service Fabric 사용](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)을 참조하세요.  

  2.2. 계측하려는 코드 조각의 경우 `StartOperation<RequestTelemetry>` `using` 문을 다음 예와 같이 추가하세요.

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> 다른 `StartOperation<RequestTelemetry>` 범위 내에서 `StartOperation<RequestTelemetry>` 호출은 지원되지 않습니다. 대신, 중첩된 범위에서는 `StartOperation<DependencyTelemetry>`를 사용할 수 있습니다. 예제:  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>환경 배포 정의 구성

Profiler 및 응용 프로그램이 실행하는 환경은 가상 컴퓨터, 가상 컴퓨터 확장 집합, Service Fabric 클러스터 또는 Cloud Services가 될 수 있습니다.  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>가상 컴퓨터, 가상 컴퓨터 확장 집합 또는 Service Fabric

전체 예:  
  * [가상 컴퓨터](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [가상 컴퓨터 확장 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric 클러스터](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. [.NET framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상이 사용 중인지 확인하려면 배포된 OS가 `Windows Server 2012 R2` 이상인지 확인하는 것으로 충분합니다.

2. 배포 템플릿 파일에서 [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) 확장명을 찾은 후 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가하여 `ApplicationInsightsProfiler` 속성 값을 자체 AI 계측 키로 대체합니다.  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

배포 템플릿에 Diagnostics 확장명을 추가하는 방법은 예제와 [이 가이드](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.


### <a name="cloud-services"></a>클라우드 서비스

1. [.NET framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용하고 있는지 확인하려면 `ServiceConfiguration.*.cscfg` 파일에 `"5"` 이상으로 `osFamily`이 포함되어 있는지 확인하는 것으로 충분합니다.

2. 응용 프로그램 역할에 대한 [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) `diagnostics.wadcfgx` 파일을 찾습니다.  
![Diagnostics 구성 파일의 위치](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
파일을 찾을 수 없는 경우 [이 가이드](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)에서 Azure Cloud Services 프로젝트에서 Diagnostics 확장명을 활성화하는 방법을 참조해 주세요.

3. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다.  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> `diagnostics.wadcfgx` 파일에 `ApplicationInsights` 유형의 다른 싱크도 포함되어 있는 경우에는 다음 모두 세 가지의 계측 키가 일치해야 합니다.  
>  * 응용 프로그램에서 사용하는 계측 키  
>  * `ApplicationInsights` 싱크에서 사용하는 계측 키  
>  * `ApplicationInsightsProfiler` 싱크에서 사용하는 계측 키  
>
>`ApplicationInsights` 싱크에서 사용하는 실제 계측 키는 `ServiceConfiguration.*.cscfg` 파일에서 확인할 수 있습니다.  
>Visual Studio 15.5 Azure SDK 릴리스 후에는 응용 프로그램과 `ApplicationInsightsProfiler` 싱크에서 사용하는 계측 키만 서로 일치하면 됩니다.


## <a name="environment-deployment-and-runtime-configurations"></a>환경 배포 및 런타임 구성

1. 수정된 환경 배포 정의를 배포합니다.  
일반적으로, 수정 사항을 적용하려면 PowerShell cmdlets 또는 Visual Studio를 통한 전체 템플릿 배포 또는 Cloud Services 배포가 관련되어 있습니다.  
다음은 Diagnostics 확장명만 수정하는 기존 `Virtual Machines`에 대한 대안적인 접근 방법입니다.  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. 원하는 응용 프로그램이 [IIS](https://www.microsoft.com/web/platform/server.aspx)를 통해 실행 중인 경우라면 `IIS Http Tracing` Windows 기능을 다시 활성화해야 합니다.  
  환경에 대한 원격 액세스를 설정하려면 [Windows 기능 추가]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) 창을 사용하거나 PowerShell에서 다음을 실행하세요(관리자로서).  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli)를 사용하여 다음을 실행할 수 있습니다.  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler를 사용하도록 설정하는 방법

독립 실행형 모드로 AI Profiler 실행이라고도 합니다(Diagnostics 확장명 수정과 연결 안 됨).  
온-프레미스 서버에서 Profiler를 공식 지원할 계획은 없습니다.
이 시나리오 실험에 관심이 있는 경우 [여기](https://github.com/ramach-msft/AIProfiler-Standalone)에서 지원 코드를 다운로드할 수 있습니다.  
해당 코드를 유지관리하거나 이와 관련된 문제 및 기능 요청에 응답할 책임은 *없습니다*.


## <a name="next-steps"></a>다음 단계

- 응용 프로그램에 대한 트래픽을 생성하고(예: [가용성 테스트](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability) 실행) 10~15분 후에 Application Insights 인스턴스로 추적을 보내기 시작하세요.

- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)을 참조하세요.

- [Profiler 문제 해결](app-insights-profiler.md#troubleshooting)에서 Profiler 문제 해결에 대한 도움말을 찾아 보세요.

- [Application Insights Profiler](app-insights-profiler.md)에서 Profiler에 대해 자세히 읽어보세요.
