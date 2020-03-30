---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249088"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure 모니터 에이전트 개요

가상 컴퓨터 및 기타 컴퓨팅 리소스에는 게스트 운영 체제 및 워크로드의 성능과 가용성을 측정하기 위해 모니터링 데이터를 수집해야 하는 에이전트가 필요합니다. 이 문서에서는 Azure Monitor에서 사용하는 에이전트에 대해 설명하고 특정 환경에 대한 요구 사항을 충족하는 데 필요한 에이전트를 결정하는 데 도움이 됩니다.

> [!NOTE]
> Azure 모니터에는 최근 Azure 모니터 및 로그 분석이 통합되어 있기 때문에 현재 여러 에이전트가 있습니다. 해당 기능에는 중복될 수 있지만 각 기능은 고유한 기능을 가지고 있습니다. 요구 사항에 따라 가상 시스템에 하나 이상의 에이전트가 필요할 수 있습니다. 

특정 가상 시스템에 대한 단일 에이전트를 완전히 충족할 수 없는 특정 요구 사항 집합이 있을 수 있습니다. 예를 들어 Azure 진단 확장이 필요하지만 Log Analytics 에이전트 및 종속성 에이전트가 필요한 VM용 Azure Monitor의 기능을 활용하려는 메트릭 경고를 사용할 수 있습니다. 이와 같은 경우 여러 에이전트를 사용할 수 있으며 각 에이전트의 기능이 필요한 고객에게 는 일반적인 시나리오입니다.

## <a name="summary-of-agents"></a>에이전트 요약

다음 표는 Windows 및 Linux용 Azure 모니터 에이전트를 빠르게 비교합니다. 각각에 대한 자세한 내용은 아래 섹션에서 제공됩니다. 

### <a name="windows-agents"></a>Windows 에이전트

| | 진단<br>확장 (WAD) | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|
| 지원되는 환경 | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | 
| 에이전트 요구 사항  | None | None | 로그 분석 에이전트필요 |
| 수집되는 데이터 | 이벤트 로그<br>ETW 이벤트<br>성능<br>파일 기반 로그<br>IIS 로그<br>.NET 앱 로그<br>크래시 덤프<br>에이전트 진단 로그 | 이벤트 로그<br>성능<IIS logs><br>파일 기반 로그<br>인사이트 및 솔루션<br>기타 서비스 | 프로세스 세부 정보 및 종속성<br>네트워크 연결 메트릭 |
| 전송된 데이터 | Azure Storage<br>Azure 모니터 메트릭<br>이벤트 허브 | Azure Monitor 로그 | Azure Monitor 로그 |


### <a name="linux-agents"></a>Linux 에이전트

| | 진단<br>확장 (LAD) | 텔레그라프<br>에이전트 | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|
| 지원되는 환경 | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 |
| 에이전트 요구 사항  | None | None | None | 로그 분석 에이전트필요 |
| 수집되는 데이터 | syslog<br>성능 | 성능 | syslog<br>성능| 프로세스 세부 정보 및 종속성<br>네트워크 연결 메트릭 |
| 전송된 데이터 | Azure Storage<br>이벤트 허브 | Azure 모니터 메트릭 | Azure Monitor 로그 | Azure Monitor 로그 |

## <a name="log-analytics-agent"></a>Log Analytics 에이전트

[Log Analytics 에이전트는](log-analytics-agent.md) Azure, 다른 클라우드 공급자 및 온-프레미스에서 게스트 운영 체제 및 가상 시스템의 워크로드에서 모니터링 데이터를 수집합니다. 로그 분석 작업 영역으로 데이터를 수집합니다. Log Analytics 에이전트는 시스템 센터 운영 관리자에서 사용하는 것과 동일한 에이전트이며, 멀티홈 에이전트 컴퓨터를 사용하여 관리 그룹 및 Azure Monitor와 동시에 통신할 수 있습니다. 이 에이전트는 Azure Monitor의 특정 인사이트 및 솔루션에도 필요합니다.


> [!NOTE]
> Windows용 로그 분석 에이전트를 MMA(Microsoft 모니터링 에이전트)라고도 합니다. Linux용 로그 분석 에이전트를 OMS 에이전트라고도 합니다.



필요한 경우 로그 분석 에이전트를 사용합니다.

* Azure 외부의 가상 또는 물리적 컴퓨터에서 로그 및 성능 데이터를 수집합니다. 
* 로그 쿼리와 같은 [Azure Monitor Log에서](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 지원하는 기능을 활용하기 위해 [로그](../log-query/log-query-overview.md)분석 작업 영역으로 데이터를 보냅니다.
* [VM용 Azure 모니터를](../insights/vminsights-overview.md) 사용하면 가상 컴퓨터를 대규모로 모니터링하고 다른 리소스 및 외부 프로세스에 대한 프로세스 및 종속성을 모니터링할 수 있습니다.  
* Azure 보안 센터 또는 [Azure](../../security-center/security-center-intro.md) [Sentinel을](../../sentinel/overview.md)사용하여 가상 시스템의 보안을 관리합니다.
* [Azure 자동화 업데이트 관리,](../../automation/automation-update-management.md)Azure 자동화 상태 [구성](../../automation/automation-dsc-overview.md)또는 Azure 자동화 변경 변경 추적 [및 인벤토리를](../../automation/change-tracking.md) 사용하여 Azure VM에 대한 포괄적인 관리를 제공합니다.
* 다른 [솔루션을](../monitor-reference.md#insights-and-core-solutions) 사용하여 특정 서비스 또는 응용 프로그램을 모니터링합니다.

로그 분석 에이전트의 제한 사항은 다음과 같습니다.

- Azure 모니터 메트릭, Azure 저장소 또는 Azure 이벤트 허브로 데이터를 보낼 수 없습니다.

## <a name="azure-diagnostics-extension"></a>Azure 진단 확장

Azure 진단 확장은 게스트 운영 체제및 Azure 가상 컴퓨터 및 기타 계산 리소스의 워크로드에서 모니터링 데이터를 [수집합니다.](diagnostics-extension-overview.md) 주로 Azure Storage에 데이터를 수집하지만 Azure 모니터 메트릭 및 Azure 이벤트 허브와 같은 다른 대상으로 데이터를 보낼 데이터 싱크를 정의할 수도 있습니다.

필요한 경우 Azure 진단 확장을 사용합니다.

- 보관을 위해 데이터를 Azure 저장소로 보내거나 Azure [저장소 탐색기와](../../vs-azure-tools-storage-manage-with-storage-explorer.md)같은 도구를 사용하여 분석합니다.
- [Azure Monitor 메트릭으로](data-platform-metrics.md) 데이터를 보내 메트릭 탐색기를 사용하여 분석하고 실시간 [메트릭 경고](../../azure-monitor/platform/alerts-metric-overview.md) 및 자동 [크기 조정(Windows만](autoscale-overview.md) 해당)과 같은 기능을 [활용합니다.](metrics-getting-started.md)
- [Azure 이벤트 허브를](diagnostics-extension-stream-event-hubs.md)사용하여 타사 도구로 데이터를 보냅니다.
- [부팅 진단을](../../virtual-machines/troubleshooting/boot-diagnostics.md) 수집하여 VM 부팅 문제를 조사합니다.

Azure 진단 확장의 제한 사항은 다음과 같습니다.

- Azure 리소스에서만 사용할 수 있습니다.
- Azure 모니터 로그에 데이터를 보낼 수 있는 기능이 제한되어 있습니다.

## <a name="telegraf-agent"></a>텔레그라프 에이전트

[InfluxData Telegraf 에이전트는](collect-custom-metrics-linux-telegraf.md) Linux 컴퓨터에서 Azure 모니터 메트릭으로 성능 데이터를 수집하는 데 사용됩니다.

필요한 경우 텔레그라프 에이전트를 사용하십시오.

* [Azure Monitor 메트릭으로](data-platform-metrics.md) 데이터를 보내 메트릭 탐색기를 사용하여 분석하고 실시간 [메트릭 경고](../../azure-monitor/platform/alerts-metric-overview.md) 및 자동 [크기 조정(Linux](autoscale-overview.md) 전용)과 같은 기능을 [활용합니다.](metrics-getting-started.md) 



## <a name="dependency-agent"></a>종속성 에이전트

종속성 에이전트는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행되는 프로세스에 대한 검색된 데이터를 수집합니다. 

필요한 경우 종속성 에이전트를 사용합니다.

* VM 또는 [서비스 맵](../insights/service-map.md) 솔루션에 대한 맵 기능 [Azure 모니터를](../insights/vminsights-overview.md) 사용합니다.

종속성 에이전트를 사용할 때 다음을 고려하십시오.

- 종속성 에이전트는 로그 분석 에이전트를 동일한 가상 시스템에 설치해야 합니다.
- Linux VM에서 로그 분석 에이전트는 Azure 진단 확장 전에 설치해야 합니다.

## <a name="extensions-compared-to-agents"></a>에이전트와 비교한 확장

[Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux용](../../virtual-machines/extensions/oms-linux.md) 로그 분석 확장은 Azure 가상 시스템에 로그 분석 에이전트를 설치합니다. [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux용](../../virtual-machines/extensions/agent-dependency-linux.md) Azure 모니터 종속성 확장은 Azure 가상 시스템에 종속성 에이전트를 설치합니다. 이들은 위에서 설명한 것과 동일한 에이전트이지만 [가상 컴퓨터 확장을](../../virtual-machines/extensions/overview.md)통해 관리할 수 있습니다. 가능하면 확장을 사용하여 에이전트를 설치하고 관리해야 합니다.


## <a name="next-steps"></a>다음 단계

각 에이전트에 대한 자세한 내용은 다음에서 확인하십시오.

- [로그 분석 에이전트 개요](log-analytics-agent.md)
- [Azure 진단 확장 개요](diagnostics-extension-overview.md)
- [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](collect-custom-metrics-linux-telegraf.md)
