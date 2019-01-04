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
ms.openlocfilehash: b98c75c1fb42c6eec2473dee1d6661d86138bd7a
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725678"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Application Insights Profiler를 사용하여 Azure Virtual Machine 또는 가상 머신 확장 집합에서 실행되는 웹앱 프로파일링
또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>가상 머신 또는 확장 집합에서 Profiler 배포
이 페이지에서는 Azure VM 또는 Azure 가상 머신 확장 집합에서 실행되는 Application Insights Profiler를 가져오는 데 필요한 단계를 안내합니다. Application Insights Profiler는 VM용 Windows Azure 진단 확장과 함께 설치됩니다. 확장은 프로파일러를 실행하도록 구성되어야 하며 App Insights SDK는 애플리케이션에 빌드해야 합니다.

1. Application Insights SDK를 [ASP.Net 애플리케이션](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) 또는 일반 [.NET 애플리케이션](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json)에 추가합니다. 요청에 대한 프로필을 보려면 Application Insights에 요청 원격 분석을 전송해야 합니다.
1. VM에 Windows Azure 진단 확장을 설치합니다. 전체 Resource Manager 템플릿 예제를 보려면 다음을 참조하세요.  
    * [가상 머신](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [가상 머신 확장 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    중요한 부분은 WadCfg에서 ApplicationInsightsProfilerSink입니다. 프로파일러에서 iKey에 데이터를 보내도록 WAD에 알리도록 이 섹션에 다른 싱크를 추가합니다.
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

   수정 사항을 적용하려면 일반적으로 PowerShell cmdlet 또는 Visual Studio를 통한 전체 템플릿 배포 또는 클라우드 서비스 기반 게시가 필요합니다.  

   Azure 진단 확장명만 수정하는 기존 가상 머신에 대한 대안으로 다음과 같은 PowerShell 명령을 사용할 수 있습니다. Get-AzureRmVMDiagnosticsExtension 명령에 의해 반환되는 구성에 위에서 설명한 것처럼 ProfilerSink를 추가하기만 하면 됩니다. 그런 다음, 업데이트된 구성을 Set-AzureRmVMDiagnosticsExcension 명령에 전달합니다.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. 원하는 애플리케이션이 [IIS](https://www.microsoft.com/web/downloads/platform.aspx)를 통해 실행 중인 경우라면 `IIS Http Tracing` Windows 기능을 활성화합니다.

   a. 환경에 대한 원격 액세스를 설정한 다음, [Windows 기능 추가]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) 창을 사용하거나 PowerShell에서 (관리자로서) 다음 명령을 실행합니다.  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. 원격 액세스 설정에 문제가 있을 경우에는 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 다음 명령을 실행할 수 있습니다.  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. 애플리케이션을 배포합니다.

## <a name="can-profiler-run-on-on-premises-servers"></a>온-프레미스 서버에서 프로파일러를 실행할 수 있나요?
온-프레미스 서버에 대한 Application Insights Profiler를 지원하는 계획이 없습니다.

## <a name="next-steps"></a>다음 단계

- 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- [Profiler 문제 해결](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)에서 Profiler 문제 해결에 대한 도움을 받으세요.
