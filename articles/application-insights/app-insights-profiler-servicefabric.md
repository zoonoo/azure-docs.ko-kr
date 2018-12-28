---
title: Application Insights를 사용하여 라이브 Azure Service Fabric 응용 프로그램 프로파일링 | Microsoft Docs
description: Service Fabric 응용 프로그램에 대해 Profiler 사용
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
ms.openlocfilehash: 3eb5b2300ea2af7bc778e0831d105f286eab247c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721411"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Service Fabric 응용 프로그램 프로파일링

또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>환경 배포 정의 설정

Application Insights Profiler는 WAD(Windows Azure 진단)에 포함되어 있습니다. Service Fabric 클러스터에 대한 Azure RM 템플릿을 사용하여 WAD 확장을 설치할 수 있습니다. 예제 템플릿은 다음과 같습니다. [**Service Fabric 클러스터에서 WAD를 설치하는 템플릿.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

환경을 설정하려면 다음 작업을 수행합니다.
1. [.NET framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상이 사용 중인지 확인하려면 배포된 OS가 `Windows Server 2012 R2` 이상인지 확인하는 것으로 충분합니다.

1. 배포 템플릿 파일에서 [Azure 진단](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) 확장명을 검색한 다음, 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다. `ApplicationInsightsProfiler` 속성 값을 고유한 Application Insights 계측 키로 바꿉니다.  

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

      배포 템플릿에 진단 확장을 추가하는 방법에 대한 정보는 [Windows VM 및 Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 사용](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.
1. Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터를 배포합니다. 설정이 올바른 경우 WAD 확장이 설치될 때 Application Insights Profiler가 설치되고 사용하도록 설정됩니다. 
1. Service Fabric 응용 프로그램에 Application Insights를 추가합니다. Profiler가 요청을 위한 프로필을 수집하려면 응용 프로그램이 Application Insights로 요청 데이터를 전송해야 합니다. [여기](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)에서 지침을 찾을 수 있습니다.
1. 응용 프로그램을 다시 배포합니다.

> [팁] Virtual Machines의 경우 위의 json 기반 단계 대신, Azure Portal에서 **Virtual Machines** > **진단 설정** > **싱크**로 이동한 후 Application Insights로 진단 데이터를 보냅니다.를 **사용**으로 설정하고 Application Insights 계정 또는 특정 ikey를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 응용 프로그램에 대한 트래픽을 생성합니다(예: [가용성 테스트](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability) 시작). 그런 다음 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
- Azure Portal에서 [Profiler 추적](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)을 참조하세요.
- [Profiler 문제 해결](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)에서 Profiler 문제 해결에 대한 도움을 받으세요.