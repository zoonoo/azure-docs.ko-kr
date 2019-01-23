---
title: Application Insights를 사용하여 라이브 Azure Cloud Services 프로파일링 | Microsoft Docs
description: Azure Cloud Services에 대해 Application Insights Profiler를 사용하도록 설정합니다.
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
ms.openlocfilehash: 01147f19a6a10361609c01bc6b3f1ac07d1ff86b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358034"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights로 라이브 Azure Cloud Services 프로파일링

다음과 같은 서비스에 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 애플리케이션](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler는 Azure 진단 확장과 함께 설치됩니다. Profiler를 설치하고 Application Insights 리소스로 프로필을 전송하도록 Azure 진단을 구성하기만 하면 됩니다.

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services에 대해 Profiler 사용
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용하고 있는지 확인합니다. 해당 *ServiceConfiguration.\*.cscfg* 파일에 "5" 이상의 `osFamily` 값이 있는지 확인하는 것으로 충분합니다.

1. [Azure Cloud Services에 Application Insights SDK](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)를 추가합니다.

1. Application Insights를 사용하여 요청을 추적합니다.

    * ASP.NET 웹 역할의 경우 Application Insights에서 요청을 자동으로 추적할 수 있습니다.

    * 작업자 역할의 경우 [요청을 추적하는 코드를 추가](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)합니다.

1. 다음을 수행하여 Profiler를 사용할 수 있도록 Azure 진단 확장을 구성합니다.

    a. 애플리케이션 역할에 대한 [Azure 진단](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)*diagnostics.wadcfgx* 파일을 다음과 같이 찾습니다.  

      ![진단 구성 파일의 위치](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      파일을 찾을 수 없는 경우 [Azure Cloud Services 및 Virtual Machines에 대한 진단 설정](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)을 참조하세요.

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
    > *ServiceConfiguration.\*.cscfg* 파일의 `ApplicationInsights` 싱크에 사용되는 실제 계측 키 값을 찾을 수 있습니다. 
    > Visual Studio 15.5 Azure SDK 릴리스 후에는 애플리케이션과 ApplicationInsightsProfiler 싱크에 사용되는 계측 키만 서로 일치하면 됩니다.

1. 새 진단 구성을 사용하여 서비스를 배포하면 서비스에서 실행되도록 Application Insights Profiler가 구성됩니다.
 
## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/monitor-web-app-availability) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
* Profiler 문제를 해결하려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
