---
title: 'Application Insights: 언어, 플랫폼 및 통합 | Microsoft Docs'
description: Application Insights 사용할 수 있는 언어, 플랫폼 및 통합
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: dd2fb0c9ef157e3706ca3d902f7feebaf4c2f279
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478904"
---
# <a name="supported-languages"></a>지원되는 언어

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)

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
* [Android](https://github.com/Microsoft/ApplicationInsights-Android)(App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md)(App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
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

## <a name="unsupported-community-sdks"></a>지원 되지 않는 커뮤니티 Sdk

많은 커뮤니티 Application Insights Sdk가 있습니다. 커뮤니티 Sdk는 Microsoft에서 공식적으로 지원/유지 관리 되지 않습니다. 공식적으로 지원 되는 목록에 없는 SDK에 대 한 지원을 제공할 수 없습니다. 