---
title: Azure 응용 프로그램 인사이트 에이전트 개요 | 마이크로 소프트 문서
description: 응용 프로그램 인사이트 에이전트의 개요입니다. 웹 사이트를 다시 배포하지 않고 웹 사이트 성능을 모니터링합니다. 온-프레미스, VM 또는 Azure에서 호스팅되는 ASP.NET 웹 앱과 함께 작동합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770960"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>온-프레미스 서버에 Azure 모니터 응용 프로그램 인사이트 에이전트 배포

> [!IMPORTANT]
> 이 가이드는 응용 프로그램 인사이트 에이전트의 온-프레미스 및 비 Azure 클라우드 배포에 권장됩니다. [Azure 가상 컴퓨터 및 가상 시스템 집합 배포에 권장되는](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)방법은 다음과 같습니다.

응용 프로그램 인사이트 에이전트(이전 이름 상태 모니터 V2)는 [PowerShell 갤러리에](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)게시된 PowerShell 모듈입니다.
[상태 모니터를](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now)대체합니다.
원격 분석이 앱을 [모니터링할](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) 수 있는 Azure 포털로 전송됩니다.

> [!NOTE]
> 이 모듈은 현재 IIS에서 호스팅되는 .NET 웹 앱의 코드 없는 계측만 지원합니다. SDK를 사용하여 코어, Java 및 Node.js 응용 ASP.NET 계측합니다.

## <a name="powershell-gallery"></a>PowerShell 갤러리

응용 프로그램 인사이트 https://www.powershellgallery.com/packages/Az.ApplicationMonitor에이전트는 다음과 같습니다.

![PowerShell 갤러리](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 간결한 코드 샘플로 시작하려면 [시작하기 지침을](status-monitor-v2-get-started.md) 참조하십시오.
- 시작하는 방법에 대한 [자세한 지침을](status-monitor-v2-detailed-instructions.md) 참조하십시오.

## <a name="powershell-api-reference"></a>파워쉘 API 참조
- [비활성화-애플리케이션 인사이트모니터링](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [비활성화 계측엔진](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [애플리케이션 인사이트 모니터링 지원](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [인에이블 계측 엔진](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [응용 프로그램 인사이트 모니터링구성](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [응용 프로그램 인사이트 모니터링 상태 얻기](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [설정 응용 프로그램 인사이트모니터링구성](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [시작-응용 프로그램 인사이트모니터링추적](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>문제 해결
- [문제 해결](status-monitor-v2-troubleshoot.md)
- [알려진 문제](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- 응용 프로그램 인사이트 에이전트가 프록시 설치를 지원합니까?

  *예*. 응용 프로그램 인사이트 에이전트를 다운로드하는 방법에는 여러 가지가 있습니다. 컴퓨터에 인터넷에 액세스할 수 있는 경우 매개 변수를 사용하여 `-Proxy` PowerShell 갤러리에 온보온할 수 있습니다.
모듈을 수동으로 다운로드하여 컴퓨터에 설치하거나 직접 사용할 수도 있습니다.
이러한 각 옵션은 자세한 [지침에](status-monitor-v2-detailed-instructions.md)설명되어 있습니다.

- 상태 모니터 v2는 핵심 응용 ASP.NET 지원합니까?

  *No*. ASP.NET 핵심 애플리케이션을 모니터링하는 방법에 대한 지침은 [ASP.NET 핵심 응용 프로그램에 대한 응용 프로그램 인사이트를](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)참조하십시오. ASP.NET 핵심 응용 프로그램에 대한 StatusMonitor를 설치할 필요가 없습니다. ASP.NET 코어 응용 프로그램이 IIS에서 호스팅되는 경우에도 마찬가지입니다.

- 사용 설정이 성공했는지 확인하려면 어떻게 해야 합니까?

  - [응용 프로그램 인사이트 모니터링 상태](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) cmdlet을 사용하여 인에이블먼트가 성공했는지 확인할 수 있습니다.
  - [라이브 메트릭을](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) 사용하여 앱이 원격 분석을 보내는지 빠르게 확인하는 것이 좋습니다.

  - [Log Analytics를](../log-query/get-started-portal.md) 사용하여 현재 원격 분석을 보내는 모든 클라우드 역할을 나열할 수도 있습니다.
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>다음 단계

원격 분석 보기:

* [메트릭을 탐색하여](../../azure-monitor/platform/metrics-charts.md) 성능 및 사용량을 모니터링합니다.
* [이벤트 및 로그를 검색하여](../../azure-monitor/app/diagnostic-search.md) 문제를 진단합니다.
* 고급 쿼리에 [애널리틱스를 사용합니다.](../../azure-monitor/app/analytics.md)
* [대시보드를 만듭니다.](../../azure-monitor/app/overview-dashboard.md)

원격 분석 더 추가:

* [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
* [웹 클라이언트 원격 분석을 추가하여](../../azure-monitor/app/javascript.md) 웹 페이지 코드의 예외를 확인하고 추적 호출을 사용하도록 설정합니다.
* 추적 및 로그 호출을 삽입할 수 있도록 [코드에 응용 프로그램 인사이트 SDK를 추가합니다.](../../azure-monitor/app/asp-net.md)

