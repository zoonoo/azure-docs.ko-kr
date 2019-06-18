---
title: Application Insights를 사용하여 라이브 Azure Service Fabric 애플리케이션 프로파일링 | Microsoft Docs
description: Service Fabric 애플리케이션에 대해 Profiler 사용
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 5c01c2721a29bf142ee0ba53c9bc29ec66a7278f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727916"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Service Fabric 애플리케이션 프로파일링

또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>환경 배포 정의 설정

Application Insights Profiler는 Azure Diagnostics에 포함되어 있습니다. Service Fabric 클러스터용 Azure Resource Manager 템플릿을 사용하여 Azure Diagnostics 확장을 설치할 수 있습니다. [Service Fabric 클러스터에 Azure 진단을 설치하는 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)을 다운로드하세요.

환경을 설정하려면 다음 작업을 수행합니다.

1. Profiler는.NET Framework 및.NET Core를 지원 합니다. .NET Framework를 사용 하는 경우 사용 했는지 확인 하십시오 [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상. 배포 된 OS 중인지 확인 하려면 부족 `Windows Server 2012 R2` 이상. Profiler는.NET Core 2.1 및 최신 응용 프로그램을 지원합니다.

1. 배포 템플릿 파일에서 [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) 확장을 검색합니다.

1. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다. `ApplicationInsightsProfiler` 속성 값을 고유한 Application Insights 계측 키로 바꿉니다.  

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

1. Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터를 배포합니다.  
  설정이 올바른 경우 Azure Diagnostics 확장이 설치될 때 Application Insights Profiler가 설치되고 사용하도록 설정됩니다. 

1. Service Fabric 애플리케이션에 Application Insights를 추가합니다.  
  요청에 대 한 프로필을 수집 하려면 Profiler에 대 한 응용 프로그램이 Application Insights를 사용 하 여 작업 추적 해야 합니다. 상태 비저장 Api에 대 한 지침을 참조할 수 있습니다 [프로 파일링에 대 한 요청 추적](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)합니다. 다른 종류의 앱에서 사용자 지정 작업을 추적 하는 방법에 대 한 자세한 내용은 참조 하세요. [Application Insights.NET SDK를 사용 하 여 사용자 지정 작업 추적](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)합니다.

1. 애플리케이션을 다시 배포합니다.


## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure Portal에서 [Profiler 추적](profiler-overview.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
* Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
