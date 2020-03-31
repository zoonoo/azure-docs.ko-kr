---
title: Azure 모니터를 통해 Azure 가상 시스템 모니터링
description: Azure 모니터를 사용하여 Azure의 가상 컴퓨터에서 모니터링 데이터를 수집하고 분석하는 방법을 설명합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283942"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure 모니터를 통해 Azure 가상 시스템 모니터링
이 문서에서는 Azure Monitor를 사용하여 Azure 가상 컴퓨터에서 모니터링 데이터를 수집하고 분석하여 상태를 유지하는 방법에 대해 설명합니다. 다른 [Azure 리소스와](monitor-azure-resource.md)마찬가지로 Azure Monitor를 사용하여 가용성 및 성능을 모니터링할 수 있지만 게스트 운영 및 시스템 및 실행되는 워크로드도 모니터링해야 하므로 다른 리소스와 고유합니다. 

> [!NOTE]
> 이 문서에서는 Azure Monitor에서 가상 컴퓨터를 모니터링하기 위한 개념 및 옵션에 대한 전체 개요를 제공합니다. 기본 개념에 집중하지 않고 가상 컴퓨터를 빠르게 모니터링하려면 [빠른 시작: Azure 모니터를 사용하여 Azure 가상 시스템 모니터링을](../learn/quick-monitor-azure-vm.md)참조하십시오.


## <a name="differences-from-other-azure-resources"></a>다른 Azure 리소스의 차이점
[Azure 모니터를 사용하여 Azure 리소스를 모니터링하면](monitor-azure-resource.md) Azure 리소스에서 생성된 모니터링 데이터와 Azure Monitor의 기능을 사용하여 이 데이터를 분석하고 경고하는 방법을 설명합니다. Azure 가상 컴퓨터에서 동일한 모니터링 데이터를 수집하고 다음과 같은 차이점을 사용하여 조치를 취하여 조치를 취합니다.

- [플랫폼 메트릭은](../platform/data-platform-metrics.md) 가상 시스템에 대해 자동으로 수집되지만 가상 시스템 호스트에 대해서만 [수집됩니다.](#monitoring-data) 게스트 운영 체제에서 성능 데이터를 수집하려면 에이전트가 필요합니다. 
- 가상 시스템은 Azure 리소스 내에서 수행된 작업에 대한 통찰력을 제공하는 [리소스 로그를](../platform/platform-logs-overview.md) 생성하지 않습니다. 에이전트를 사용하여 게스트 운영 체제에서 로그 데이터를 수집합니다.
- 가상 시스템에 대한 [진단 설정을](../platform/diagnostic-settings.md) 만들어 저장소 및 이벤트 허브와 같은 다른 대상으로 플랫폼 메트릭을 보낼 수 있지만 Azure Portal에서는 이러한 진단 설정을 구성할 수 없습니다. 

## <a name="monitoring-data"></a>데이터 모니터링
Azure의 가상 컴퓨터는 다음 다이어그램에 표시된 [로그](../platform/data-platform-logs.md) 및 [메트릭을 생성합니다.](../platform/data-platform-metrics.md)

![개요](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>가상 컴퓨터 호스트
Azure의 가상 시스템은 [모니터링 데이터에](monitor-azure-resource.md#monitoring-data)설명된 대로 다른 Azure 리소스와 동일한 가상 시스템 호스트에 대한 다음 데이터를 생성합니다.

- [플랫폼 메트릭](../platform/data-platform-metrics.md) - 일정한 간격으로 자동으로 수집되고 특정 시간에 리소스의 일부 측면을 설명하는 수치 값입니다. 가상 시스템 호스트에 대해 플랫폼 메트릭이 수집되지만 게스트 운영 체제에 대한 메트릭을 수집하려면 진단 확장이 필요합니다.
- [활동 로그](../platform/platform-logs-overview.md) - 외부(관리 평면)에서 구독의 각 Azure 리소스에 대한 작업에 대한 통찰력을 제공합니다. 가상 컴퓨터의 경우 시작 시점 및 구성 변경과 같은 정보가 포함됩니다.


### <a name="guest-operating-system"></a>게스트 운영 체제
가상 시스템의 게스트 운영 체제에서 데이터를 수집하려면 각 가상 컴퓨터에서 로컬로 실행되고 Azure Monitor로 데이터를 보내는 에이전트가 필요합니다. Azure Monitor에는 각각 서로 다른 데이터를 수집하고 다른 위치에 데이터를 쓰는 여러 에이전트를 사용할 수 있습니다. Azure Monitor 에이전트 개요에서 다른 [에이전트에 대한](../platform/agents-overview.md)자세한 비교를 가져옵니다. 

- [로그 분석 에이전트](../platform/agents-overview.md#log-analytics-agent) - Azure, 기타 클라우드 환경 및 온-프레미스의 가상 시스템에 사용할 수 있습니다. Azure 모니터 로그에 데이터를 수집합니다. VM 및 모니터링 솔루션에 대한 Azure 모니터를 지원합니다. 시스템 센터 운영 관리자에 사용되는 에이전트와 동일합니다.
- [종속성 에이전트](../platform/agents-overview.md#dependency-agent) - 가상 컴퓨터에서 실행되는 프로세스 및 해당 종속성에 대한 데이터를 수집합니다. 로그 분석 에이전트를 사용하여 Azure로 데이터를 전송하고 VM, 서비스 맵 및 와이어 데이터 2.0 솔루션에 대한 Azure 모니터를 지원합니다.
- [Azure 진단 확장](../platform/agents-overview.md#azure-diagnostics-extension) - Azure 모니터 가상 컴퓨터에서만 사용할 수 있습니다. 여러 위치에 데이터를 수집할 수 있지만 주로 Windows 가상 컴퓨터용 Azure Monitor 메트릭으로 게스트 성능 데이터를 수집하는 데 사용됩니다.
- [Telegraf 에이전트](../platform/collect-custom-metrics-linux-telegraf.md) - Linux VM에서 성능 데이터를 Azure 모니터 메트릭으로 수집합니다.


## <a name="configuration-requirements"></a>구성 요구 사항
가상 컴퓨터를 모니터링하기 위해 Azure Monitor의 모든 기능을 활성화하려면 가상 시스템 호스트 및 게스트 운영 체제에서 [Azure 모니터 메트릭](../platform/data-platform-logs.md) 및 Azure 모니터 로그에 대한 모니터링 데이터를 수집해야 [합니다.](../platform/data-platform-logs.md) 다음 표에는 이 컬렉션을 활성화하기 위해 수행해야 하는 구성이 나열되어 있습니다. 특정 요구 사항에 따라 이러한 단계를 모두 수행하지 않도록 선택할 수 있습니다.

| 구성 단계 | 완료된 작업 | 기능 사용 가능 |
|:---|:---|:---|
| 구성 없음 | - 메트릭에 수집 된 호스트 플랫폼 메트릭.<br>- 활동 로그가 수집되었습니다. | - 호스트에 대한 메트릭 탐색기.<br>- 호스트에 대한 메트릭 경고.<br>- 활동 로그 경고. |
| [VM용 Azure Monitor 사용](#enable-azure-monitor-for-vms) | - 로그 분석 에이전트가 설치되었습니다.<br>- 종속성 에이전트가 설치되었습니다.<br>- 로그에 수집 된 게스트 성능 데이터.<br>- 로그에 수집 된 프로세스 및 종속성 세부 정보. | - 게스트 성능 데이터에 대한 성능 차트 및 통합 문서.<br>- 게스트 성능 데이터에 대한 로그 쿼리.<br>- 게스트 성능 데이터에 대한 로그 경고.<br>- 종속성 맵. |
| [진단 확장 및 텔레그라프 에이전트 설치](#enable-diagnostics-extension-and-telegraf-agent) | - 지표에 수집 된 게스트 성능 데이터. | - 게스트에 대한 메트릭 탐색기.<br>- 게스트에 대한 메트릭 경고.  |
| [로그 분석 작업 영역 구성](#configure-log-analytics-workspace) | - 손님으로부터 수집 된 이벤트. | - 게스트 이벤트에 대한 로그 쿼리.<br>- 게스트 이벤트에 대한 로그 알림. |
| [가상 시스템에 대한 진단 설정 만들기](#collect-platform-metrics-and-activity-log) | - 로그에 수집 된 플랫폼 메트릭.<br>- 로그에 수집 된 활동 로그. | - 호스트 메트릭에 대한 Loq 쿼리.<br>- 호스트 메트릭에 대한 로그 경고.<br>- 활동 로그에 대한 로그 쿼리.

이러한 각 구성 단계는 다음 섹션에 설명되어 있습니다.

### <a name="enable-azure-monitor-for-vms"></a>VM용 Azure Monitor 사용
[VM용 Azure 모니터는](vminsights-overview.md) Azure 모니터에서 가상 컴퓨터를 모니터링하기 위한 기본 도구인 Azure 모니터의 [인사이트입니다.](insights-overview.md) 표준 Azure Monitor 기능에 대해 다음과 같은 추가 값을 제공합니다.

- 로그 분석 에이전트 및 종속성 에이전트의 온보딩을 간소화하여 가상 시스템 게스트 운영 체제 및 워크로드를 모니터링할 수 있습니다. 
- 가상 시스템의 게스트 운영 체제에서 핵심 성능 메트릭을 분석할 수 있는 미리 정의된 추세 성능 차트 및 통합 문서입니다.
- 각 가상 컴퓨터에서 실행되는 프로세스와 다른 컴퓨터 및 외부 원본과 상호 연결된 구성 요소를 표시하는 종속성 맵입니다.

![VM용 Azure Monitor](media/monitor-vm-azure/vminsights-01.png)

![VM용 Azure Monitor](media/monitor-vm-azure/vminsights-02.png)


Azure 포털의 가상 시스템 메뉴에서 Insights 옵션에서 VM에 대한 **Azure** 모니터를 사용하도록 설정합니다. 자세한 내용 및 기타 구성 방법에 [대한 VM용 Azure 모니터 활성화 개요를](vminsights-enable-overview.md) 참조하십시오.

![VM용 Azure Monitor 사용](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>로그 분석 작업 영역 구성
VM용 Azure 모니터에서 사용하는 로그 분석 에이전트는 데이터를 [로그 분석 작업 영역으로](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)보냅니다. Log Analytics 작업 영역을 구성하여 에이전트에서 추가 성능 데이터, 이벤트 및 기타 모니터링 데이터를 수집할 수 있습니다. 작업 영역에 연결하는 에이전트가 자동으로 구성을 다운로드하고 정의된 데이터 수집을 즉시 시작하기 때문에 한 번만 구성하면 됩니다. 

**시작**하기에서 작업 영역 구성을 선택 하 여 VM에 대 한 Azure 모니터에서 직접 작업 영역에 대 한 구성에 액세스할 수 **있습니다.** 작업 영역 이름을 클릭하여 메뉴를 엽니다.

![작업 영역 구성](media/monitor-vm-azure/workspace-configuration.png)

작업 영역 메뉴에서 **고급 설정을** 선택한 다음 **데이터를** 선택하여 데이터 원본을 구성합니다. Windows 에이전트의 경우 Windows 이벤트 로그를 선택하고 *시스템* 및 *응용 프로그램과*같은 일반적인 이벤트 로그를 **추가합니다.** Linux 에이전트의 경우 **Syslog를** 선택하고 *커널* 및 *데몬과*같은 공용 시설을 추가합니다. 사용 가능한 데이터 원본 목록과 데이터 원본 구성에 대한 자세한 내용은 [Azure Monitor의 에이전트](../platform/agent-data-sources.md) 데이터 원본을 참조하십시오. 

![이벤트 구성](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 작업 영역 구성에서 수집할 성능 카운터를 구성할 수 있지만 VM용 Azure Monitor에서 수집한 성능 카운터와 중복될 수 있습니다. VM용 Azure Monitor는 분당 한 번씩 가장 일반적인 카운터 집합을 수집합니다. VM용 Azure Monitor에서 아직 수집하지 않은 카운터를 수집하려는 경우 또는 성능 데이터를 사용하는 기존 쿼리가 있는 경우에만 작업 영역에서 수집할 성능 카운터를 구성합니다.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>진단 확장 및 텔레그라프 에이전트 사용
VM용 Azure 모니터는 로그 분석 작업 영역으로 데이터를 수집하는 로그 분석 에이전트를 기반으로 합니다. 이렇게 하면 [로그 쿼리,](../log-query/log-query-overview.md)로그 경고 및 [통합 문서와](../platform/workbooks-overview.md)같은 [Azure Monitor의 여러 기능이](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) [지원됩니다.](../platform/alerts-log.md) [진단 확장은](../platform/diagnostics-extension-overview.md) Windows 가상 시스템의 게스트 운영 체제에서 Azure Storage로 성능 데이터를 수집하고 선택적으로 성능 데이터를 [Azure Monitor 메트릭으로](../platform/data-platform-metrics.md)보냅니다. Linux 가상 시스템의 경우 [Telegraf 에이전트가](../platform/collect-custom-metrics-linux-telegraf.md) Azure 메트릭으로 데이터를 보내야 합니다.  이렇게 하면 [메트릭 탐색기](../platform/metrics-getting-started.md) 및 메트릭 [경고와](../platform/alerts-metric.md)같은 Azure Monitor의 다른 기능을 사용할 수 있습니다. Azure Event Hubs를 사용하여 Azure Monitor 외부에서 이벤트 및 성능 데이터를 전송하도록 진단 확장을 구성할 수도 있습니다.

VM 메뉴의 진단 설정 옵션에서 Azure 포털에 단일 Windows 가상 컴퓨터에 대한 진단 확장을 **설치합니다.** 싱크 탭에서 **Azure 모니터를** 사용하도록 설정하는 옵션을 **선택합니다.** 여러 가상 시스템에 대한 템플릿 또는 명령줄에서 확장을 사용하려면 [설치 및 구성을](../platform/diagnostics-extension-overview.md#installation-and-configuration)참조하십시오. 로그 분석 에이전트와 달리 수집할 데이터는 각 가상 컴퓨터의 확장에 대한 구성에 정의됩니다.

![진단 설정](media/monitor-vm-azure/diagnostic-setting.png)

Linux 가상 컴퓨터에서 Telegraf 에이전트 구성에 대한 자세한 내용은 [Telegraf 설치 및 구성을](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) 참조하십시오. **진단 설정** 메뉴 옵션은 Linux에서 사용할 수 있지만 Azure 저장소로만 데이터를 보낼 수 있습니다.

### <a name="collect-platform-metrics-and-activity-log"></a>플랫폼 메트릭 및 활동 로그 수집
Azure 포털에서 각 가상 시스템 호스트에 대해 수집된 플랫폼 메트릭 및 활동 로그를 볼 수 있습니다. 이 데이터를 VM용 Azure 모니터와 동일한 로그 분석 작업 영역에 수집하여 가상 시스템에 대해 수집된 다른 모니터링 데이터로 분석합니다. 이 컬렉션은 진단 [설정으로](../platform/diagnostic-settings.md)구성됩니다. [구독에 대한 진단 설정으로](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)활동 로그를 수집합니다.

가상 시스템에 대한 진단 설정으로 플랫폼 메트릭을 수집합니다. 다른 Azure 리소스와 달리 Azure 포털에서 가상 컴퓨터에 대한 진단 설정을 만들 수는 없지만 [다른 메서드를](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)사용해야 합니다. 다음 예제는 PowerShell 및 CLI를 모두 사용하여 가상 시스템에 대한 메트릭을 수집하는 방법을 보여 주며 있습니다.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 
가상 시스템에 대한 모니터링 데이터 컬렉션을 구성하면 Azure 포털에서 데이터에 액세스하기 위한 여러 옵션이 있습니다.

- Azure **모니터** 메뉴를 사용하여 모니터링되는 모든 리소스의 데이터에 액세스합니다. 
- 대규모 가상 컴퓨터 집합을 모니터링하기 위해 VM에 Azure 모니터를 사용합니다.
- Azure 포털의 메뉴에서 단일 가상 시스템에 대한 데이터를 분석합니다. 아래 표에는 가상 시스템 메뉴를 모니터링하기 위한 다양한 옵션이 나열되어 있습니다.

![Azure Portal에서 모니터링](media/monitor-vm-azure/monitor-menu.png)

| 메뉴 옵션 | 설명 |
|:---|:---|
| 개요 | 가상 시스템 호스트에 대한 [플랫폼 메트릭을 표시합니다.](../platform/data-platform-metrics.md) 그래프를 클릭하여 [메트릭 탐색기에서](../platform/metrics-getting-started.md)이 데이터로 작업합니다. |
| 활동 로그 | 현재 가상 시스템에 대해 필터링된 [활동 로그](../platform/activity-log-view.md) 항목입니다. |
| 자세한 정보 | 현재 가상 시스템에 대한 맵이 선택된 [VM용 Azure 모니터를](../insights/vminsights-overview.md) 엽니다. |
| 경고 | 현재 가상 시스템에 대한 [경고를](../platform/alerts-overview.md) 볼 수 있습니다.  |
| 메트릭 | 범위가 현재 가상 컴퓨터로 설정된 [메트릭 탐색기를](../platform/metrics-getting-started.md) 엽니다. |
| 진단 설정 | 현재 가상 시스템에 대한 [진단 확장을](../platform/diagnostics-extension-overview.md) 활성화하고 구성합니다. |
| Advisor 권장 사항 | [Azure Advisor의](/azure/advisor/)현재 가상 컴퓨터에 대한 권장 사항 |
| 로그 | 현재 가상 컴퓨터로 설정된 [범위로](../log-query/scope.md) [로그 분석을 엽니다.](../log-query/log-query-overview.md#what-is-log-analytics) |
| 연결 모니터 | 네트워크 [감시자 연결 모니터를](../../network-watcher/connection-monitor-preview.md) 열어 현재 가상 컴퓨터와 다른 가상 시스템 간의 연결을 모니터링합니다. |


## <a name="analyzing-metric-data"></a>메트릭 데이터 분석
가상 컴퓨터의 메뉴에서 메트릭을 열어 메트릭 탐색기를 사용하여 가상 시스템에 대한 메트릭을 분석할 수 **있습니다.** 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작을](../platform/metrics-getting-started.md) 참조하십시오. 

메트릭에 가상 컴퓨터에서 사용하는 두 가지 네임스페이스가 있습니다.

| 네임스페이스 | 설명 |
|:---|:---|
| 가상 머신 호스트 | 모든 Azure 가상 시스템에 대해 자동으로 수집된 메트릭호스트입니다. [Microsoft.Compute/가상 머신의](../platform/metrics-supported.md#microsoftcomputevirtualmachines)자세한 메트릭 목록. |
| 가상 머신 게스트 | 진단 확장이 설치되어 Azure Monitor 싱크로 보내도록 구성된 가상 시스템에서 수집된 게스트 운영 체제 메트릭입니다. |

![메트릭](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>로그 데이터 분석
Azure 가상 컴퓨터는 Azure 모니터 로그에 다음 데이터를 수집합니다. 

VM용 Azure 모니터를 사용하면 *InsightsMetrics* 테이블에 기록된 미리 결정된 성능 카운터 집합을 수집할 수 있습니다. 이 테이블은 [컨테이너에 대한 Azure 모니터에서](container-insights-overview.md)사용하는 것과 동일한 테이블입니다. 

| 데이터 원본 | 요구 사항 | 테이블 |
|:---|:---|:---|
| VM용 Azure Monitor | 각 가상 컴퓨터에서 활성화합니다. | 인사이트메트릭<br>VM바운드 포트<br>VM컴퓨터<br>VMConnection<br>VMProcess<br>자세한 [내용은 Azure 모니터에서 VM에 대한 로그를 쿼리하는 방법을](vminsights-log-search.md) 참조하십시오. |
| 활동 로그 | 구독에 대한 진단 설정입니다. | AzureActivity |
| 호스트 메트릭 | 가상 시스템에 대한 진단 설정입니다. | AzureMetrics |
| 게스트 운영 체제의 데이터 원본 | 로그 분석 에이전트를 활성화하고 데이터 원본을 구성합니다. | 각 데이터 원본에 대한 설명서를 참조하십시오. |


> [!NOTE]
> Log Analytics 에이전트가 수집한 성능 데이터는 *Perf* 테이블에 기록되며 VM용 Azure 모니터는 *InsightsMetrics* 테이블로 수집합니다. 이는 동일한 데이터이지만 테이블의 구조는 다릅니다. *Perf를*기반으로 하는 기존 쿼리가 있는 경우 *InsightsMetrics를*사용하려면 다시 작성해야 합니다.


## <a name="alerts"></a>경고
Azure [Monitor의 경고는](../platform/alerts-overview.md) 모니터링 데이터에 중요한 조건이 발견되면 사전에 알려주며 로직 앱을 시작하거나 웹후크를 호출하는 등의 작업을 시작할 수 있습니다. 경고 규칙은 경고를 만들어야 하는 시기를 결정하는 데 사용되는 논리를 정의합니다. Azure Monitor는 경고 규칙에 사용되는 데이터를 수집하지만 Azure 구독에서 경고 조건을 정의하는 규칙을 만들어야 합니다.

다음 섹션에서는 경고 규칙의 유형과 각 규칙을 사용해야 하는 시기에 대한 권장 사항을 설명합니다. 이 권장 사항은 경고 규칙 유형의 기능 및 비용을 기반으로 합니다. 경고에 대한 자세한 내용은 [Azure 모니터 가격 책정을](https://azure.microsoft.com/pricing/details/monitor/)참조하십시오.


### <a name="activity-log-alert-rules"></a>활동 로그 경고 규칙
[활동 로그 경고 규칙은](../platform/alerts-activity-log.md) 활동 로그에서 특정 기준과 일치하는 항목이 생성될 때 발생합니다. 필요한 논리가 활동 로그에 있는 경우 비용이 들지 않으므로 첫 번째 선택이어야 합니다. 

활동 로그 경고의 대상 리소스는 특정 가상 컴퓨터, 리소스 그룹의 모든 가상 컴퓨터 또는 구독의 모든 가상 컴퓨터일 수 있습니다.

예를 들어 신호 이름에 대한 가상 시스템 전원 *끄기를* 선택하여 중요한 가상 시스템이 중지된 경우 경고를 만듭니다.

![활동 로그 경고](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>메트릭 경고 규칙
[메트릭 값이](../platform/alerts-metric.md) 임계값을 초과하면 메트릭 경고 규칙이 발생합니다. 특정 임계값을 정의하거나 Azure Monitor가 기록 데이터를 기반으로 임계값을 동적으로 결정할 수 있도록 허용할 수 있습니다.  로그 경고 규칙보다 비용이 적게 들고 응답성이 높기 때문에 메트릭 데이터에서 가능하면 메트릭 경고를 사용합니다. 또한 메트릭이 임계값 아래로 떨어질 때 스스로 해결된다는 의미이기도 합니다.

활동 로그 경고의 대상 리소스는 특정 가상 컴퓨터이거나 리소스 그룹의 모든 가상 시스템일 수 있습니다.

예를 들어 가상 시스템의 프로세서가 특정 값을 초과할 때 경고를 만들려면 *백분율 CPU를* 신호 유형으로 사용하여 메트릭 경고 규칙을 만듭니다. 특정 임계값을 설정하거나 Azure Monitor에서 동적 임계값을 설정할 수 있도록 합니다. 

![메트릭 경고](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>로그 경고
[로그 경고 규칙은](../platform/alerts-log.md) 예약된 로그 쿼리의 결과가 특정 기준과 일치할 때 발생합니다. 로그 쿼리 경고는 경고 규칙중 가장 비싸고 응답성이 가장 적지만 가장 다양한 데이터에 액세스할 수 있으며 다른 경고 규칙에서 수행할 수 없는 복잡한 논리를 수행할 수 있습니다. 

로그 쿼리의 대상 리소스는 로그 분석 작업 영역입니다. 쿼리의 특정 컴퓨터에 대해 필터링합니다.

예를 들어 특정 리소스 그룹의 가상 컴퓨터가 오프라인 상태인지 확인하는 경고를 만들려면 마지막 10분 동안 하트비트를 놓친 각 컴퓨터에 대한 레코드를 반환하는 다음 쿼리를 사용합니다. 하나 이상의 컴퓨터에 누락된 하트비트가 있는 경우 1의 임계값을 사용합니다.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![로그 경고](media/monitor-vm-azure/log-alert-01.png)

구독의 모든 Windows 가상 컴퓨터에서 과도한 수의 실패한 로그온이 발생한 경우 경고를 만들려면 지난 시간 동안 실패한 각 로그온 이벤트에 대한 레코드를 반환하는 다음 쿼리를 사용합니다. 허용할 실패한 로그온 수로 설정된 임계값을 사용합니다. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![로그 경고](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
시스템 센터 운영 관리자(SCOM)는 가상 시스템에서 워크로드를 세부적으로 모니터링합니다. 모니터링 플랫폼과 다양한 구현 전략을 비교하면 [클라우드 모니터링 가이드를](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) 참조하십시오.

계속 사용하려는 기존 SCOM 환경이 있는 경우 Azure Monitor와 통합하여 추가 기능을 제공할 수 있습니다. Azure Monitor에서 사용하는 로그 분석 에이전트는 SCOM에 사용되는 것과 동일하므로 가상 컴퓨터가 두 데이터 모두에 데이터를 전송하는 모니터링을 수행할 수 있습니다. VM용 Azure Monitor에 에이전트를 추가하고 위에 지정된 대로 추가 데이터를 수집하도록 작업 영역을 구성해야 하지만 가상 시스템은 수정 없이 SCOM 환경에서 기존 관리 팩을 계속 실행할 수 있습니다.

기존 SCOM 기능을 보강하는 Azure 모니터의 기능은 다음과 같습니다.

- 로그 분석을 사용하여 로그 및 성능 데이터를 대화식으로 분석할 수 있습니다.
- 로그 경고를 사용하여 여러 가상 컴퓨터에서 경고 조건을 정의하고 SCOM의 경고를 사용하여 불가능한 장기 추세를 사용합니다.   

기존 SCOM 관리 그룹을 Log Analytics 작업 영역에 연결하는 방법에 대한 자세한 내용은 [운영 관리자를 Azure 모니터에](../platform/om-agents.md) 연결참조하세요.


## <a name="next-steps"></a>다음 단계

* [로그 쿼리를 사용하여 Azure Monitor 로그에서 데이터를 분석하는 방법을 알아봅니다.](../log-query/get-started-queries.md)
* [Azure Monitor에서 메트릭 및 로그를 사용하여 경고에 대해 알아봅니다.](../platform/alerts-overview.md)

