---
title: Azure 애플리케이션 Insights 에이전트 개요 | Microsoft Docs
description: Application Insights 에이전트에 대 한 개요입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 0fa0b0d5e3620fc45a104ea31fd3bcbedd673da1
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318949"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>온-프레미스 서버에 대 한 Azure Monitor Application Insights 에이전트 배포

> [!IMPORTANT]
> 이 지침은 Application Insights 에이전트의 온-프레미스 및 비 Azure 클라우드 배포에 권장 됩니다. [Azure 가상 머신 및 가상 머신 확장 집합 배포](./azure-vm-vmss-apps.md)에 권장 되는 방법은 다음과 같습니다.

Application Insights 에이전트 (이전의 명명 된 상태 모니터 V2)는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)에 게시 된 PowerShell 모듈입니다.
[상태 모니터](./monitor-performance-live-website-now.md)를 대체 합니다.
원격 분석은 앱을 [모니터링할](./app-insights-overview.md) 수 있는 Azure Portal 전송 됩니다.

> [!NOTE]
> 이 모듈은 현재 IIS에서 호스트 되는 .NET 웹 앱의 코드 없는 계측만 지원 합니다. SDK를 사용 하 여 ASP.NET Core, Java 및 Node.js 응용 프로그램을 계측 합니다.

## <a name="powershell-gallery"></a>PowerShell 갤러리

Application Insights 에이전트는에 https://www.powershellgallery.com/packages/Az.ApplicationMonitor 있습니다.

![PowerShell 갤러리](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>지침
- 간결한 코드 샘플을 가져오려면 [시작 지침](status-monitor-v2-get-started.md) 을 참조 하세요.
- 시작 하는 방법에 대 한 심층 [정보는 자세한 지침](status-monitor-v2-detailed-instructions.md) 을 참조 하세요.

## <a name="powershell-api-reference"></a>PowerShell API 참조
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- 에이전트에서 프록시 설치를 지원 Application Insights 합니까?

  *예*. Application Insights 에이전트를 다운로드 하는 방법에는 여러 가지가 있습니다. 컴퓨터에서 인터넷에 액세스할 수 있는 경우 매개 변수를 사용 하 여 PowerShell 갤러리에 등록할 수 있습니다 `-Proxy` .
또한 모듈을 수동으로 다운로드 하 고 컴퓨터에 설치 하거나 직접 사용할 수 있습니다.
이러한 각 옵션에 대 한 [자세한 지침은](status-monitor-v2-detailed-instructions.md)을 참조 하십시오.

- V2는 ASP.NET Core 응용 프로그램을 지원 상태 모니터 합니까?

  *아니요*. ASP.NET Core 응용 프로그램 모니터링을 사용 하도록 설정 하는 지침은 [ASP.NET Core 응용 프로그램에 대 한 Application Insights](./asp-net-core.md)를 참조 하세요. ASP.NET Core 응용 프로그램에 대 한 StatusMonitor를 설치할 필요가 없습니다. ASP.NET Core 응용 프로그램이 IIS에서 호스팅되는 경우에도 마찬가지입니다.

- 성공적으로 작동 하 고 있는지 확인 어떻게 할까요??

  - [ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) cmdlet을 사용 하 여 사용이 성공 했는지 확인할 수 있습니다.
  - 앱에서 원격 분석을 전송 하 고 있는지 신속 하 게 확인 하려면 [라이브 메트릭을](./live-stream.md) 사용 하는 것이 좋습니다.

  - [Log Analytics](../log-query/get-started-portal.md) 를 사용 하 여 현재 원격 분석을 보내는 모든 클라우드 역할을 나열할 수도 있습니다.
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>다음 단계

원격 분석 보기:

* [메트릭을 탐색](../platform/metrics-charts.md) 하 여 성능 및 사용량을 모니터링 합니다.
* [이벤트와 로그를 검색](./diagnostic-search.md) 하 여 문제를 진단 합니다.
* 고급 쿼리를 위해 [분석을 사용](../log-query/log-query-overview.md) 합니다.
* [대시보드를 만듭니다](./overview-dashboard.md).

원격 분석 더 추가:

* [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
* 웹 [클라이언트 원격 분석을 추가](./javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
* 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](./asp-net.md) 합니다.

