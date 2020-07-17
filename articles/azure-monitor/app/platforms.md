---
title: 'Application Insights: 언어, 플랫폼 및 통합 | Microsoft Docs'
description: Application Insights 사용할 수 있는 언어, 플랫폼 및 통합
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 153d4ad3d95c182dcc4f2aa3bad857d7e1984cc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891104"
---
# <a name="supported-languages"></a>지원되는 언어

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>지원되는 플랫폼 및 프레임워크

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>이미 배포 된 응용 프로그램에 대 한 계측 (코드 없는, 에이전트 기반)
* [Azure VM 및 Azure 가상 머신 확장 집합](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 이미 운영 중인 앱용](../../azure-monitor/app/monitor-performance-live-website-now.md)
* 웹 및 작업자 역할을 모두 포함 하는 [Azure Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>코드를 통한 계측 (Sdk)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [유니버설 Windows 앱](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [Windows 데스크톱 애플리케이션, 서비스 및 작업자 역할](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>로깅 프레임워크
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog 또는 System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J 또는 Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash 플러그 인](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>내보내기 및 데이터 분석
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>지원되지 않는 SDK
커뮤니티에서 지 원하는 여러 Sdk가 있습니다. 그러나 Azure Monitor은이 페이지에 나열 된 지원 되는 Sdk를 사용할 때만 지원 합니다. 다른 언어에 대 한 지원을 확장 하는 기회를 지속적으로 평가 하 고 있으므로 [GitHub 발표](https://github.com/microsoft/ApplicationInsights-Announcements/issues) 페이지에 따라 최신 SDK 뉴스를 받을 수 있습니다. 
