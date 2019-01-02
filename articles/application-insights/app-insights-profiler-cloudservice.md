---
title: Application Insights를 사용하여 라이브 Azure Cloud Services 프로파일링 | Microsoft Docs
description: Cloud Services 리소스에 대해 Application Insights Profiler를 사용하도록 설정합니다.
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
ms.openlocfilehash: 5bb70bf56efac28029401b69ee4f87c2738c52e3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721853"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights로 라이브 Azure Cloud Services 프로파일링

또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric 응용 프로그램](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler는 WAD(Windows Azure 진단) 확장과 함께 설치됩니다. Profiler를 설치하고 Application Insights 리소스로 프로필을 전송하도록 WAD를 구성하기만 하면 됩니다.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Azure Cloud Service에 대해 Profiler 사용
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용하고 있는지 확인합니다.  해당 *ServiceConfiguration.\*.cscfg* 파일에 "5" 이상의 `osFamily` 값이 있는지 확인하는 것으로 충분합니다.
1. [클라우드 서비스에 Application Insights SDK](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json)를 추가합니다.
1. Application Insights를 사용하여 요청을 추적합니다.

    ASP.Net 웹 역할의 경우 Application Insights에서 요청을 자동으로 추적할 수 있습니다.

    작업자 역할의 경우 [요청을 추적하는 코드를 추가합니다.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Profiler를 사용하도록 WAD(Windows Azure 진단) 확장을 구성합니다.



    1. 응용 프로그램 역할에 대한 [Azure 진단](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* 파일을 다음과 같이 찾습니다.  

       ![진단 구성 파일의 위치](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        파일을 찾을 수 없는 경우 Azure Cloud Services 프로젝트에서 진단 확장을 사용하도록 설정하는 방법을 알아보려면 [Azure Cloud Services 및 가상 머신에 대한 진단 설정](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)을 참조하세요.

    1. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다.  

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

    >   **참고:** *diagnostics.wadcfgx* 파일에 `ApplicationInsights` 형식의 다른 싱크도 포함되어 있는 경우 다음 세 가지 계측 키가 모두 일치해야 합니다.  
    >  * 응용 프로그램에 사용되는 키입니다.  
    >  * `ApplicationInsights` 싱크에 사용되는 키입니다.  
    >  * `ApplicationInsightsProfiler` 싱크에 사용되는 키입니다.  
    >
    > *ServiceConfiguration.\*.cscfg* 파일의 `ApplicationInsights` 싱크에 사용되는 실제 계측 키 값을 찾을 수 있습니다.  
    > Visual Studio 15.5 Azure SDK 릴리스 후에는 애플리케이션과 `ApplicationInsightsProfiler` 싱크에서 사용하는 계측 키만 서로 일치해야 합니다.
1. 새 진단 구성을 사용하여 서비스를 배포하면 서비스에서 실행되도록 Application Insights Profiler가 구성됩니다.
 
## <a name="next-steps"></a>다음 단계

- 응용 프로그램에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability) 시작). 그런 다음 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- [Profiler 문제 해결](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)에서 Profiler 문제 해결에 대한 도움을 받으세요.