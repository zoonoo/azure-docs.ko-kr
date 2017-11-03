---
title: "Azure Compute 응용 프로그램에 Application Insights Profiler를 사용하도록 설정 | Microsoft Docs"
description: "Azure Compute에서 실행 중인 응용 프로그램에서 Application Insights Profiler를 설정하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: 66ea24cfe9dd03ed62c06daa76ee043886ad7bcc
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/26/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Azure VM, Service Fabric 및 Cloud Services에서 Application Insights Profiler 사용

이 문서에서는 Azure Compute 리소스에서 호스팅하는 ASP.NET 응용 프로그램에서 Azure Application Insights Profiler를 사용하는 방법을 보여줍니다. 

이 문서의 예제에는 Azure Virtual Machines, 가상 컴퓨터 확장 집합, Azure Service Fabric 및 Azure Cloud Services에 대한 지원이 포함됩니다. 예제는 [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) 배포 모델을 지원하는 템플릿이 필요합니다.  


## <a name="overview"></a>개요

다음 이미지는 Azure 리소스와 함께 Application Insights Profiler를 사용하는 방법을 보여줍니다. 이미지는 예로 Azure Virtual Machine을 사용합니다.

  ![개요](./media/enable-profiler-compute/overview.png)

Profiler를 완전히 활성화하려면 다음과 같은 세 가지 위치에서 구성을 변경해야 합니다.

* Azure Portal의 Application Insights 인스턴스 창
* 응용 프로그램 소스 코드(예: ASP.NET 웹 응용 프로그램)
* 환경 배포 정의 소스 코드(예: VM 배포 템플릿 .json 파일)


## <a name="set-up-the-application-insights-instance"></a>Application Insights 인스턴스 설정

Azure Portal에서 사용하려는 Application Insights 인스턴스를 만들거나 이동합니다. 인스턴스 계측 키를 적어둡니다. 다른 구성 단계에서 계측 키를 사용합니다.

  ![계측 키의 위치](./media/enable-profiler-compute/CopyAIKey.png)

이 인스턴스는 응용 프로그램과 동일해야 합니다. 각 요청에 원격 분석 데이터를 보내도록 구성됩니다.
Profiler 결과도 이 인스턴스에서 사용할 수 있습니다.  

Azure Portal에서 [프로파일러 사용](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)에 설명된 단계를 완료하여 Profiler에 대한 Application Insights 인스턴스의 설정을 마칩니다. 이 문서의 예제에서 웹앱에 연결할 필요가 없습니다. Profiler가 포털에서 활성화되어 있는지 확인합니다.


## <a name="set-up-the-application-source-code"></a>응용 프로그램 소스 코드 설정

원격 분석 데이터를 각 `Request` 작업의 Application Insights 인스턴스에 보내도록 응용 프로그램을 설정합니다.  

1. 응용 프로그램 프로젝트에 [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started)를 추가합니다. NuGet 패키지 버전이 다음과 같은지 확인하세요.  
  - ASP.NET 응용 프로그램: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 이상
  - ASP.NET Core 응용 프로그램: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 이상
  - 기타 .NET 및 .NET Core 응용 프로그램(예: Service Fabric 상태 비저장 서비스 또는 Cloud Services 작업자 역할): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 또는 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 이상  

2. 응용 프로그램이 ASP.NET 또는 ASP.NET Core 응용 프로그램(예: Cloud Services 작업자 역할 또는 Service Fabric 상태 비저장 API)이 *아닌* 경우 다음 추가 계측 설정이 필요합니다.  

  1. 다음 코드를 응용 프로그램 수명 초기에 추가합니다.  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  이 전역 계측 키 구성에 대한 자세한 내용은 [Application Insights를 통해 Service Fabric 사용](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)을 참조하세요.  

  2. 계측하려는 코드 조각의 경우 다음 예와 같이 `StartOperation<RequestTelemetry>` **USING** 문을 추가합니다.

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

  다른 `StartOperation<RequestTelemetry>` 범위 내에서 `StartOperation<RequestTelemetry>` 호출은 지원되지 않습니다. 대신, 중첩된 범위에서는 `StartOperation<DependencyTelemetry>`를 사용할 수 있습니다. 예:  

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


## <a name="set-up-the-environment-deployment-definition"></a>환경 배포 정의 설정

Profiler 및 응용 프로그램이 실행하는 환경은 가상 컴퓨터, 가상 컴퓨터 확장 집합, Service Fabric 클러스터 또는 Cloud Services 인스턴스가 될 수 있습니다.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>가상 컴퓨터, 가상 컴퓨터 확장 집합 또는 Service Fabric

전체 예:  
  * [가상 컴퓨터](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [가상 컴퓨터 확장 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric 클러스터](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. [.NET framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상이 사용 중인지 확인하려면 배포된 OS가 `Windows Server 2012 R2` 이상인지 확인하는 것으로 충분합니다.

2. 배포 템플릿 파일에서 [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) 확장명을 찾은 후 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다. `ApplicationInsightsProfiler` 속성 값을 고유한 Application Insights 계측 키로 바꿉니다.  
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

  배포 템플릿에 진단 확장을 추가하는 방법에 대한 정보는 [Windows VM 및 Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 사용](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.


### <a name="cloud-services"></a>Cloud Services

1. [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용 중인지 확인하려면 해당 ServiceConfiguration.\*.cscfg 파일에 **"5"** 이상의 `osFamily` 값이 있는지 확인하는 것으로 충분합니다.

2. 응용 프로그램 역할에 대한 [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx 파일을 찾습니다.  
  ![Diagnostics 구성 파일의 위치](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  파일을 찾을 수 없는 경우 Cloud Services 프로젝트에서 진단 확장을 사용하도록 설정하는 방법을 알아보려면 [Azure Cloud Services 및 가상 컴퓨터에 대한 진단 설정](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)을 참조하세요.

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
> `diagnostics.wadcfgx` 파일에 `ApplicationInsights` 형식의 다른 싱크도 포함되어 있는 경우 다음 세 가지 계측 키가 모두 일치해야 합니다.  
>  * 응용 프로그램에서 사용하는 계측 키  
>  * `ApplicationInsights` 싱크에서 사용하는 계측 키  
>  * `ApplicationInsightsProfiler` 싱크에서 사용하는 계측 키  
>
> ServiceConfiguration.\*.cscfg 파일의 `ApplicationInsights` 싱크에서 사용하는 실제 계측 키 값을 찾을 수 있습니다.  
> Visual Studio 15.5 Azure SDK 릴리스 후에는 응용 프로그램과 `ApplicationInsightsProfiler` 싱크에서 사용하는 계측 키만 서로 일치해야 합니다.


## <a name="environment-deployment-and-runtime-configurations"></a>환경 배포 및 런타임 구성

1. 수정된 환경 배포 정의를 배포합니다.  

  수정 사항을 적용하려면 일반적으로 PowerShell cmdlets 또는 Visual Studio를 통한 전체 템플릿 배포 또는 클라우드 서비스 게시가 관련되어 있습니다.  

  해당 Azure Diagnostics 확장명만 수정하는 기존 가상 컴퓨터에 대한 대안 방법은 다음과 같습니다.  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. 원하는 응용 프로그램이 [IIS](https://www.microsoft.com/web/platform/server.aspx)를 통해 실행 중인 경우라면 `IIS Http Tracing` Windows 기능을 활성화합니다.  
  
  1. 환경에 대한 원격 액세스를 설정하려면 [Windows 기능 추가]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) 창을 사용하거나 PowerShell에서 (관리자로서) 다음 명령을 실행하세요.  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. 원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli)를 사용하여 다음 명령을 실행할 수 있습니다.  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler 사용

온-프레미스 서버에 Profiler를 사용하도록 설정하면 독립 실행형 모드에서 Application Insights Profiler를 실행하는 것과 같습니다(Azure Diagnostics 확장 수정과 연결되지 않음). 

온-프레미스 서버에서 Profiler를 공식적으로 지원할 계획은 없습니다. 이 시나리오를 실험하려는 경우 [지원 코드를 다운로드](https://github.com/ramach-msft/AIProfiler-Standalone)할 수 있습니다. 해당 코드를 유지 관리하거나 코드와 관련된 문제 및 기능 요청에 응답할 책임은 *없습니다*.

## <a name="next-steps"></a>다음 단계

- 응용 프로그램에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability) 시작). 그런 다음 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)을 참조하세요.
- [Profiler 문제 해결](app-insights-profiler.md#troubleshooting)에서 Profiler 문제 해결에 대한 도움을 받으세요.
- [Application Insights Profiler](app-insights-profiler.md)에서 프로파일러에 대해 자세히 읽어보세요.
