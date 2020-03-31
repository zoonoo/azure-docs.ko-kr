---
title: '애플리케이션 인사이트: 언어, 플랫폼 및 통합 | 마이크로 소프트 문서'
description: 애플리케이션 인사이트에 사용할 수 있는 언어, 플랫폼 및 통합
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: cbda252fece638393ee7757dc165c4b009a0195e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136726"
---
# <a name="supported-languages"></a>지원되는 언어

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [자바 스크립트](../../azure-monitor/app/javascript.md)
* [Node.js](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>지원되는 플랫폼 및 프레임워크

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>이미 배포된 애플리케이션을 위한 계측(코드없는 에이전트 기반)
* [Azure VM 및 Azure 가상 시스템 규모 집합](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - 이미 운영 중인 앱용](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [웹](../../azure-monitor/app/cloudservices.md)및 작업자 역할을 모두 포함하는 Azure 클라우드 서비스
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>코드를 통한 계측(SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.js](https://www.npmjs.com/package/applicationinsights)
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
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>지원되지 않는 SDK
커뮤니티에서 지원하는 여러 SDK가 존재한다는 것을 알고 있습니다. 그러나 Azure Monitor는 이 페이지에 나열된 지원되는 SDK를 사용할 때만 지원을 제공합니다. 다른 언어에 대한 지원을 확대할 수 있는 기회를 지속적으로 평가하고 있으므로 [GitHub 공지 사항](https://github.com/microsoft/ApplicationInsights-Announcements/issues) 페이지를 팔로우하여 최신 SDK 뉴스를 받아보세요. 
