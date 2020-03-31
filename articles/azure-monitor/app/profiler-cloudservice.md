---
title: Application Insights를 사용하여 라이브 Azure Cloud Services 프로파일링 | Microsoft Docs
description: Azure Cloud Services에 대해 Application Insights Profiler를 사용하도록 설정합니다.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671667"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights로 라이브 Azure Cloud Services 프로파일링

다음과 같은 서비스에 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 애플리케이션](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure 가상 시스템](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler는 Azure Diagnostics 확장과 함께 설치됩니다. Profiler를 설치하고 Application Insights 리소스로 프로필을 전송하도록 Azure Diagnostics를 구성하기만 하면 됩니다.

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services에 대해 Profiler 사용
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용하고 있는지 확인합니다. OS 패밀리 4를 사용하는 경우 .NET Framework 4.6.1 또는 [시작 작업을](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet)통해 최신. OS 패밀리 5에는 기본적으로 .NET Framework의 호환되는 버전이 포함되어 있습니다. 

1. [Azure Cloud Services에 Application Insights SDK](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)를 추가합니다.

    **클라우드 서비스에 대한 WAD에서 제공되는 프로파일러의 버그가 수정되었습니다.** 클라우드 서비스에 대한 WAD (1.12.2.0)의 최신 버전은 앱 인사이트 SDK의 모든 최신 버전과 함께 작동합니다. 클라우드 서비스 호스트는 WAD를 자동으로 업그레이드하지만 즉각적인 것은 아닙니다. 업그레이드를 강제하려면 서비스를 다시 배포하거나 노드를 다시 부팅할 수 있습니다.

1. Application Insights를 사용하여 요청을 추적합니다.

    * ASP.NET 웹 역할의 경우 Application Insights에서 요청을 자동으로 추적할 수 있습니다.

    * 작업자 역할의 경우 [요청을 추적하는 코드를 추가합니다.](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

1. 프로파일러를 사용하도록 사용하도록 Azure 진단 확장을 구성합니다.

    a. 애플리케이션 역할에 대한 [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* 파일을 다음과 같이 찾습니다.  

      ![진단 구성 파일의 위치](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      파일을 찾을 수 없는 경우 [Azure Cloud Services 및 Virtual Machines에 대한 진단 설정](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)을 참조하세요.

    b. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다.  

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
    > *diagnostics.wadcfgx* 파일에 ApplicationInsights 형식의 다른 싱크도 포함된 경우 다음 세 가지 계측 키가 모두 일치해야 합니다.  
    > * 애플리케이션에 사용되는 키입니다. 
    > * ApplicationInsights 싱크에 사용되는 키 
    > * ApplicationInsightsProfiler 싱크에 사용되는 키 
    >
    > 서비스 구성에서 `ApplicationInsights` 싱크에서 사용하는 실제 계측 키 값을 찾을 수 *있습니다.\* cscfg* 파일. 
    > Visual Studio 15.5 Azure SDK 릴리스 후에는 애플리케이션과 ApplicationInsightsProfiler 싱크에 사용되는 계측 키만 서로 일치하면 됩니다.

1. 새 진단 구성을 사용하여 서비스를 배포하면 서비스에서 실행되도록 Application Insights Profiler가 구성됩니다.
 
## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure 포털에서 [프로파일러 추적을](profiler-overview.md?toc=/azure/azure-monitor/toc.json) 참조하십시오.
* Profiler 문제를 해결하려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
