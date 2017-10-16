---
title: "Azure 계산 리소스에 Azure Application Insights Profiler를 사용하도록 설정 | Microsoft Docs"
description: "Azure Cloud Services 리소스에서 호스트되는 ASP.NET 응용 프로그램에 프로파일러를 설정하는 방법을 알아봅니다."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 65ba755f35df7bd09dd652ac6fccf96a878c6ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Azure Cloud Services 리소스에서 Application Insights Profiler를 사용하도록 설정

이 연습에는 Azure Cloud Services 리소스에서 호스트되는 ASP.NET 응용 프로그램에서 Azure Application Insights Profiler를 사용하도록 설정하는 방법을 보여 줍니다. 예제에는 Azure Virtual Machines, 가상 컴퓨터 확장 집합 및 Azure Service Fabric에 대한 지원이 포함됩니다. 예제는 모두 Azure Resource Manager 배포 모델을 지원하는 템플릿이 필요합니다. 배포 모델에 대한 자세한 내용은 [Azure Resource Manager 및 클래식 배포: 배포 모델 및 리소스 상태 이해](/azure-resource-manager/resource-manager-deployment-model)를 검토하세요.

## <a name="overview"></a>개요

다음 다이어그램에서는 Azure Cloud Services 리소스에 대해 프로파일러가 작동하는 방식을 보여 줍니다. 예로 Azure Virtual Machine을 사용합니다.

![개요](./media/enable-profiler-compute/overview.png) 처리를 위한 정보를 수집하고 Azure Portal에 표시하려면 Azure Cloud Services 리소스에 대한 진단 에이전트 구성 요소를 설치해야 합니다. 이 연습의 나머지 부분에서는 Application Insights Profiler를 사용하도록 설정하기 위해 진단 에이전트를 설치 및 구성하는 방법에 대한 지침을 제공합니다.

## <a name="prerequisites-for-the-walkthrough"></a>연습을 위한 필수 구성 요소

* VM에서 프로파일러 에이전트를 설치하는 배포 Resource Manager 템플릿([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) 또는 확장 집합([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)).

* 프로파일링을 위해 Application Insights 인스턴스가 사용하도록 설정되어 있어야 합니다. 자세한 지침은 [프로파일링 사용](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)을 참조하세요.

* .NET Framework 4.6.1 이상이 대상 Azure Cloud Services 리소스에 설치되어 있습니다.

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Azure 구독에서 리소스 그룹 만들기
다음 예제는 PowerShell 스크립트를 사용하여 리소스 그룹을 만드는 방법을 보여 줍니다.

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>리소스 그룹에서 Application Insights 리소스 만들기
**Application Insights** 블레이드에서 이 예제와 같이 리소스에 대한 정보를 입력합니다. 

![Application Insights 블레이드](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 Application Insights 계측 키 적용

1. 템플릿을 아직 다운로드하지 않은 경우 [GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)에서 다운로드합니다.

2. Application Insights 키를 찾습니다.
   
   ![키의 위치](./media/enable-profiler-compute/copyaikey.png)

3. 템플릿 값을 바꿉니다.
   
   ![템플릿에서 대체된 값](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>웹 응용 프로그램을 호스트할 Azure VM 만들기
1. 암호를 저장할 보안 문자열을 만듭니다.

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Azure Resource Manager 템플릿을 배포합니다.

   PowerShell 콘솔에서 Resource Manager 템플릿이 포함된 폴더로 디렉터리를 변경합니다. 템플릿을 배포하려면 다음 명령을 실행합니다.

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

스크립트가 성공적으로 실행된 후에는 리소스 그룹에서 **MyWindowsVM**이라는 이름의 VM을 찾을 수 있어야 합니다.

## <a name="configure-web-deploy-on-the-vm"></a>VM에서 웹 배포 구성
Visual Studio에서 웹 응용 프로그램을 게시할 수 있도록 VM에서 웹 배포가 사용하도록 설정되었는지 확인합니다.

WebPI를 통해 VM에 웹 배포를 수동으로 설치하려면 [IIS 8.0 이상에서 웹 배포 설치 및 구성](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)을 참조하세요. Azure Resource Manager 템플릿을 사용하여 웹 배포 설치를 자동화하는 방법에 대한 예는 [웹 응용 프로그램 만들기, 구성 및 Azure VM에 배포](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)를 참조하세요.

ASP.NET MVC 응용 프로그램을 배포하는 경우 서버 관리자로 이동하여, **역할 및 기능 추가** > **웹 서버(IIS)** > **웹 서버** > **응용 프로그램 개발**을 선택하고 서버에서 ASP.NET 4.5를 사용하도록 설정합니다.

![ASP.NET 추가](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>프로젝트에 Azure Application Insights SDK 설치
1. Visual Studio에서 ASP.NET 웹 응용 프로그램을 엽니다.

2. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **연결된 서비스**를 선택합니다.

3. **Application Insights**를 선택합니다.

4. 페이지의 지침을 따릅니다. 이전에 만든 Application Insights 리소스를 선택합니다.

5. **등록** 단추를 선택합니다.


## <a name="publish-the-project-to-an-azure-vm"></a>Azure VM에 프로젝트 게시
여러 가지 방법으로 Azure VM에 응용 프로그램을 게시할 수 있습니다. 하나는 Visual Studio 2017을 사용하는 것입니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

2. 게시 대상으로 **Microsoft Azure Virtual Machines**를 선택하고 단계를 수행합니다.

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. 응용 프로그램에 대해 부하 테스트를 실행합니다. Application Insights 인스턴스 포털 웹 페이지에서 결과를 볼 수 있습니다.


## <a name="enable-the-profiler"></a>프로파일러 활성화

1. Application Insights **성능** 블레이드로 이동하고 오른쪽 위 모서리에 있는 **프로파일러**를 클릭하여 구성합니다.

   ![프로파일러 구성 단추](./media/enable-profiler-compute/PerformanceTriageViewPofilerButton.png)

2. **프로파일러 사용**을 선택합니다.

   ![프로파일러 사용 단추](./media/enable-profiler-compute/enableprofiler2.png)


## <a name="add-a-performance-test-to-your-application"></a>응용 프로그램에 성능 테스트 추가
이러한 단계를 수행하여 Application Insights Profiler에 표시될 샘플 데이터를 수집할 수 있습니다.

1. 이전에 만든 Application Insights 리소스를 찾아봅니다. 

2. **가용성** 블레이드로 이동하여 웹 요청을 응용 프로그램 URL로 보내는 성능 테스트를 추가합니다. 

   ![성능 테스트 추가](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>성능 데이터 보기

1. 프로파일러가 데이터를 수집 및 분석하도록 10-15분 동안 대기합니다. 

2. Application Insights 리소스의 **성능** 블레이드로 이동하여 응용 프로그램이 부하 상태에서 어떻게 수행되는지 확인합니다. 개수 열 기준으로 작업 그리드를 정렬하여 사용량이 충분한, 속도가 느린 관심 작업에 집중합니다. 기간 분포보다 높은 프로파일러 레인을 살펴봄으로써 프로파일러 추적이 어떤 기간 범위에 있는지 관찰합니다. 응용 프로그램을 오래 모니터링할수록 더 많은 추적 프로파일러가 수집되므로 더 많은 분포는 프로파일러 추적에서 지원하는 풍부한 코드 수준 예제에서 다루게 됩니다. 

   ![성능 심사 보기에서 프로파일러 추적](./media/enable-profiler-compute/PerformanceTriageViewProfilerTraces.png)

    95 백분위수에 세 번째 스파이크와 같이 관심 있는 기간 범위로 확대할 수 있습니다. 이는 작업 수행 단추에서 샘플 및 프로파일러 추적 수를 제한합니다. 

    ![기간 범위 확대](./media/enable-profiler-compute/DurationRangeZoomedTo95th.png)

    이제 **프로파일러 추적** 버튼 클릭하여 적절한 추적이 포함된 프로파일러를 엽니다.

3. **예제** 아래에서 아이콘을 선택하여 **추적 보기** 블레이드를 엽니다.

   ![추적 보기 블레이드 열기](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>기존 템플릿으로 작업

1. 배포 템플릿에서 Azure 진단 리소스 선언을 찾습니다.
   
   선언이 없으면 다음 예제에서 선언과 유사하게 하나 만들 수 있습니다. [Azure Resource Explorer 웹 사이트](https://resources.azure.com)에서 템플릿을 업데이트할 수 있습니다.

2. 게시자를 `Microsoft.Azure.Diagnostics`에서 `AIP.Diagnostics.Test`로 변경합니다.

3. `typeHandlerVersion`의 경우 `0.0`을 사용합니다.

4. `autoUpgradeMinorVersion`이 `true`로 설정되어 있는지 확인합니다.

5. 다음 예제와 같이 새 `ApplicationInsightsProfiler` 싱크 인스턴스를 `WadCfg` 설정 개체에 추가합니다.

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>가상 컴퓨터 크기 집합에서 프로파일러 사용
프로파일러를 사용하도록 설정하는 방법을 보려면 [WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json) 템플릿을 다운로드합니다. VM 템플릿의 동일한 변경 내용을 가상 컴퓨터 확장 집합의 진단 확장 리소스에 적용합니다.

확장 집합의 각 인스턴스가 인터넷에 액세스할 수 있는지 확인합니다. 그러면 프로파일러 에이전트가 표시 및 분석을 위해 수집된 샘플을 Application Insights에 보낼 수 있습니다.

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Service Fabric 응용 프로그램에서 프로파일러 사용
1. 프로파일러 에이전트를 설치하는 Azure 진단 확장이 있는 Service Fabric 클러스터를 프로비전합니다.

2. 프로젝트에 Application Insights SDK를 설치하고 Application Insights 키를 구성합니다.

3. 계측 원격 분석에 응용 프로그램 코드를 추가합니다.

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>프로파일러 에이전트를 설치하는 Azure 진단 확장이 있는 Service Fabric 클러스터를 프로비전합니다.
Service Fabric 클러스터는 보안 또는 비보안일 수 있습니다. 액세스하는 데 인증서가 필요하지 않도록 게이트웨이 클러스터를 비보안으로 설정할 수 있습니다. 비즈니스 논리와 데이터를 호스트하는 클러스터는 안전해야 합니다. 보안 및 비보안 Service Fabric 클러스터 모두에 프로파일러를 사용하도록 설정할 수 있습니다. 이 연습에서는 비보안 클러스터를 예제로 사용하여 프로파일러를 사용하도록 설정하기 위해 어떠한 내용을 변경해야 하는지 설명합니다. 동일한 방식으로 보안 클러스터를 프로비전할 수 있습니다.

1. [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)을 다운로드합니다. VM 및 가상 컴퓨터 확장 집합과 동일하게 `Application_Insights_Key`를 Application Insights 키로 바꿉니다.

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. PowerShell 스크립트를 사용하여 템플릿을 배포합니다.

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>프로젝트에 Application Insights SDK를 설치하고 Application Insights 키를 구성합니다.
[NuGet 패키지](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)에서 Application Insights SDK를 설치합니다. 안정적인 버전 2.3 이상을 설치하도록 합니다. 

프로젝트에 Application Insights를 구성하는 방법에 대한 정보는 [Application insights를 통해 Service Fabric 사용](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)을 참조하세요.

### <a name="add-application-code-to-instrument-telemetry"></a>계측 원격 분석에 응용 프로그램 코드 추가
1. 계측하려는 코드 조각의 경우 using 문으로 묶습니다. 

   다음 예제에서는 `RunAsync` 메서드로 작업을 수행하며 `telemetryClient` 클래스가 시작된 후 원격 분석을 캡처합니다. 이벤트에는 응용 프로그램 간에 고유한 이름이 필요합니다.

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Service Fabric 클러스터에 응용 프로그램을 배포합니다. 앱이 실행되기까지 10분 동안 기다립니다. 더 나은 결과를 위해 앱에서 부하 테스트를 실행할 수 있습니다. Application Insights 포털의 **성능** 블레이드로 이동하면 프로파일링 추적 예가 표시됩니다.

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>다음 단계

- [프로파일러 문제 해결](app-insights-profiler.md#troubleshooting)에서 프로파일러 문제 해결에 대한 도움말을 찾아 보세요.

- [Application Insights Profiler](app-insights-profiler.md)에서 프로파일러에 대해 자세히 읽어보세요.
