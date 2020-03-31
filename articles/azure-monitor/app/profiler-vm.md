---
title: Azure VM - 응용 프로그램 인사이트 프로파일러의 프로필 웹 앱
description: Application Insights Profiler를 사용하여 Azure VM에서 웹앱 프로파일링
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671582"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Application Insights Profiler를 사용하여 Azure Virtual Machine 또는 가상 머신 확장 집합에서 실행되는 웹앱 프로파일링

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

또한 다음과 같은 서비스에서 Azure Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure 클라우드 서비스](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure 서비스 패브릭](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>가상 머신 또는 가상 머신 확장 집합에서 Profiler 배포
이 문서에서는 Azure VM(Virtual Machine) 또는 Azure Virtual Machine Scale Set에서 실행되는 Application Insights Profiler를 가져오는 방법을 보여 줍니다. Profiler는 VM용 Azure Diagnostics 확장과 함께 설치됩니다. Profiler를 실행하도록 확장을 구성하고 애플리케이션에 Application Insights SDK를 빌드합니다.

1. [ASP.NET 응용 프로그램에](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)응용 프로그램 인사이트 SDK를 추가합니다.

   요청에 대한 프로필을 보려면 Application Insights에 요청 원격 분석을 전송해야 합니다.

1. VM에 Azure Diagnostics 확장을 설치합니다. 전체 Resource Manager 템플릿 예제를 보려면 다음을 참조하세요.  
   * [가상 머신](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [가상 머신 크기 집합](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     중요한 부분은 WadCfg에서 ApplicationInsightsProfilerSink입니다. Profiler가 iKey에 데이터를 보내도록 Azure Diagnostics를 구성하려면 이 섹션에 다른 싱크를 추가합니다.
    
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

   다음 PowerShell 명령은 Azure 진단 확장만 터치하는 기존 가상 시스템에 대한 대체 방법입니다. Get-AzVMDiagnosticsExtension 명령에 의해 반환되는 구성에 앞에서 언급한 프로파일러싱크를 추가합니다. 그런 다음 업데이트된 구성을 Set-AzVMDiagnosticsExtension 명령으로 전달합니다.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
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

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>Azure 리소스 탐색기를 사용하여 프로파일러 싱크 설정
아직 포털에서 응용 프로그램 인사이트 프로파일러 싱크를 설정할 수 있는 방법이 없습니다. 위에서 설명한 것과 같은 powershell을 사용하는 대신 Azure 리소스 탐색기를 사용하여 싱크를 설정할 수 있습니다. 그러나 VM을 다시 배포하면 싱크가 손실됩니다. 이 설정을 유지하려면 VM을 배포할 때 사용하는 구성을 업데이트해야 합니다.

1. 가상 시스템에 설치된 확장을 확인하여 Windows Azure 진단 확장이 설치되어 있는지 확인합니다.  

    ![WAD 확장이 설치되어 있는지 확인][wadextension]

2. VM에 대한 VM 진단 확장을 찾습니다. 로 [https://resources.azure.com](https://resources.azure.com)이동합니다. 리소스 그룹인 Microsoft.Compute 가상 머신, 가상 컴퓨터 이름 및 확장을 확장합니다.  

    ![Azure 리소스 탐색기에서 WAD 구성으로 이동][azureresourceexplorer]

3. WadCfg 아래의 싱크콘피그 노드에 응용 프로그램 인사이트 프로파일러 싱크를 추가합니다. 아직 싱크콘fig 섹션이 없는 경우 추가해야 할 수 있습니다. 설정에서 적절한 응용 프로그램 인사이트 iKey를 지정해야 합니다. 탐색기 모드를 오른쪽 상단 모서리의 읽기/쓰기로 전환하고 파란색 '편집' 버튼을 눌러야 합니다.

    ![응용 프로그램 인사이트 프로파일러 싱크 추가][resourceexplorersinksconfig]

4. 구성 편집이 완료되면 'Put'을 누릅니다. 퍼팅에 성공하면 화면 중앙에 녹색 검사가 나타납니다.

    ![변경 사항을 적용하기 위한 put 요청 보내기][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>온-프레미스 서버에서 Profiler를 실행할 수 있나요?
온-프레미스 서버에 대한 Application Insights Profiler를 지원하는 계획이 없습니다.

## <a name="next-steps"></a>다음 단계

- 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure 포털에서 [프로파일러 추적을](profiler-overview.md?toc=/azure/azure-monitor/toc.json) 참조하십시오.
- Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
