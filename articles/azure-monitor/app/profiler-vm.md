---
title: Application Insights Profiler를 사용하여 Azure VM에서 실행되는 웹앱 프로파일링 | Microsoft Docs
description: Application Insights Profiler를 사용하여 Azure VM에서 웹앱 프로파일링
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 3f720cdf369e7377f16bb2ea9cba7e898097cc29
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359785"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Application Insights Profiler를 사용하여 Azure Virtual Machine 또는 가상 머신 확장 집합에서 실행되는 웹앱 프로파일링

또한 다음과 같은 서비스에서 Azure Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>가상 머신 또는 가상 머신 확장 집합에서 Profiler 배포
이 문서에서는 Azure VM(Virtual Machine) 또는 Azure Virtual Machine Scale Set에서 실행되는 Application Insights Profiler를 가져오는 방법을 보여 줍니다. Profiler는 VM용 Azure 진단 확장과 함께 설치됩니다. Profiler를 실행하도록 확장을 구성하고 애플리케이션에 Application Insights SDK를 빌드합니다.

1. Application Insights SDK를 [ASP.NET 애플리케이션](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) 또는 일반 [.NET 애플리케이션](https://docs.microsoft.com/azure/application-insights/windows-services?toc=/azure/azure-monitor/toc.json)에 추가합니다.  
  요청에 대한 프로필을 보려면 Application Insights에 요청 원격 분석을 전송해야 합니다.

1. VM에 Azure 진단 확장을 설치합니다. 전체 Resource Manager 템플릿 예제를 보려면 다음을 참조하세요.  
    * [가상 머신](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [가상 머신 확장 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    중요한 부분은 WadCfg에서 ApplicationInsightsProfilerSink입니다. Profiler가 iKey에 데이터를 보내도록 Azure 진단을 구성하려면 이 섹션에 다른 싱크를 추가합니다.
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. 수정된 환경 배포 정의를 배포합니다.  

   수정 사항을 적용하면 일반적으로 PowerShell cmdlet 또는 Visual Studio를 통한 전체 템플릿 배포 또는 클라우드 서비스 기반 게시가 진행됩니다.  

   Azure 진단 확장명만 수정하는 기존 가상 머신에 대한 대안으로 다음과 같은 PowerShell 명령을 사용할 수 있습니다. 앞에서 언급한 ProfilerSink를 Get-AzureRmVMDiagnosticsExtension 명령에 의해 반환된 구성에 추가한 다음, 업데이트된 구성을 Set-AzureRmVMDiagnosticsExtension 명령에 전달합니다.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 원하는 애플리케이션이 [IIS](https://www.microsoft.com/web/downloads/platform.aspx)를 통해 실행 중인 경우라면 `IIS Http Tracing` Windows 기능을 활성화합니다.

   a. 환경에 대한 원격 액세스를 설정한 후, [Windows 기능 추가]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 창을 사용합니다. 또는 관리자 권한으로 PowerShell에서 다음 명령을 실행합니다.  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 다음 명령을 실행할 수 있습니다.  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 애플리케이션을 배포합니다.

## <a name="can-profiler-run-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler를 실행할 수 있나요?
온-프레미스 서버에 대한 Application Insights Profiler를 지원하는 계획이 없습니다.

## <a name="next-steps"></a>다음 단계

- 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
