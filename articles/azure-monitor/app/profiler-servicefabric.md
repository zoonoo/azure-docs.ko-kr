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
ms.openlocfilehash: 73e453385a6916b6999691afbacd1f102ce7270d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55865746"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Service Fabric 애플리케이션 프로파일링

또한 다음과 같은 서비스에서 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>환경 배포 정의 설정

Application Insights Profiler는 Azure 진단에 포함되어 있습니다. Service Fabric 클러스터용 Azure Resource Manager 템플릿을 사용하여 Azure 진단 확장을 설치할 수 있습니다. [Service Fabric 클러스터에 Azure 진단을 설치하는 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)을 다운로드하세요.

환경을 설정하려면 다음 작업을 수행합니다.

1. [.NET framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상이 사용 중인지 확인하려면 배포된 OS가 `Windows Server 2012 R2` 이상인지 확인하는 것으로 충분합니다.

1. 배포 템플릿 파일에서 [Azure 진단](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) 확장을 검색합니다.

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
  설정이 올바른 경우 Azure 진단 확장이 설치될 때 Application Insights Profiler가 설치되고 사용하도록 설정됩니다. 

1. Service Fabric 애플리케이션에 Application Insights를 추가합니다.  
  Profiler가 요청을 위한 프로필을 수집하려면 애플리케이션이 Application Insights로 요청 데이터를 전송해야 합니다. 자세한 내용은 [Service Fabric 프로젝트용 Application Insights SDK](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) 페이지를 참조하세요.

1. 애플리케이션을 다시 배포합니다.

> [팁] 가상 머신의 경우에는 위의 JSON 기반 단계를 수행하는 대신 Azure Portal의 **가상 머신** > **진단 설정** > **싱크** > **진단 데이터를 Application Insights로 보내기를 사용으로 설정**한 다음 Application Insights 계정이나 특정 iKey를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure Portal에서 [Profiler 추적](profiler-overview.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
* Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
