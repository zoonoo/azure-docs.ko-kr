---
title: Azure Monitor를 사용 하 여 Azure virtual machines 모니터링
description: Azure Monitor를 사용 하 여 Azure의 가상 머신에서 모니터링 데이터를 수집 하 고 분석 하는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283942"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure virtual machines 모니터링
이 문서에서는 Azure Monitor를 사용 하 여 Azure 가상 머신에서 모니터링 데이터를 수집 및 분석 하 여 상태를 유지 하는 방법을 설명 합니다. 가상 머신은 [다른 Azure 리소스](monitor-azure-resource.md)와 마찬가지로 Azure Monitor의 가용성과 성능에 대해 모니터링할 수 있지만 게스트 운영 및 시스템 및 해당 운영 체제에서 실행 되는 워크 로드를 모니터링 해야 하기 때문에 다른 리소스에서 고유 합니다. 

> [!NOTE]
> 이 문서에서는 Azure Monitor에서 가상 컴퓨터를 모니터링 하는 데 필요한 개념과 옵션에 대 한 전체 개요를 제공 합니다. 기본 개념에 집중 하지 않고 가상 머신 모니터링을 신속 하 게 시작 하려면 [빠른 시작: Azure Monitor을 사용 하 여 Azure virtual machine](../learn/quick-monitor-azure-vm.md)모니터링을 참조 하세요.


## <a name="differences-from-other-azure-resources"></a>다른 Azure 리소스와의 차이점
[Azure Monitor를 사용 하 여 azure 리소스를 모니터링](monitor-azure-resource.md) 하면 azure 리소스에서 생성 되는 모니터링 데이터 및 Azure Monitor의 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법에 대해 설명 합니다. Azure virtual machines에서 동일한 모니터링 데이터를 수집 하 고 작업할 수 있으며 다음과 같은 차이점이 있습니다.

- [플랫폼 메트릭은](../platform/data-platform-metrics.md) 가상 컴퓨터 [호스트](#monitoring-data)에 대해서만 자동으로 수집 됩니다. 게스트 운영 체제에서 성능 데이터를 수집 하려면 에이전트가 필요 합니다. 
- 가상 머신은 Azure 리소스 내에서 수행 된 작업에 대 한 통찰력을 제공 하는 [리소스 로그](../platform/platform-logs-overview.md) 를 생성 하지 않습니다. 에이전트를 사용 하 여 게스트 운영 체제에서 로그 데이터를 수집 합니다.
- 저장소 및 event hubs와 같은 다른 대상에 플랫폼 메트릭을 전송 하는 가상 컴퓨터에 대 한 [진단 설정을](../platform/diagnostic-settings.md) 만들 수 있지만 이러한 진단 설정은 Azure Portal에서 구성할 수 없습니다. 

## <a name="monitoring-data"></a>데이터 모니터링
Azure에서 Azure의 가상 컴퓨터는 다음 다이어그램에 표시 된 [로그](../platform/data-platform-logs.md) 및 [메트릭을](../platform/data-platform-metrics.md) 생성 합니다.

![개요](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>가상 컴퓨터 호스트
Azure의 가상 머신은 [데이터 모니터링](monitor-azure-resource.md#monitoring-data)에 설명 된 대로 다른 azure 리소스와 동일한 가상 머신 호스트에 대 한 다음 데이터를 생성 합니다.

- [플랫폼 메트릭](../platform/data-platform-metrics.md) -정기적으로 자동으로 수집 되는 숫자 값으로, 특정 시간에 리소스의 일부 측면을 설명 합니다. 플랫폼 메트릭은 가상 컴퓨터 호스트에 대해 수집 되지만, 게스트 운영 체제에 대 한 메트릭을 수집 하려면 진단 확장이 필요 합니다.
- [활동 로그](../platform/platform-logs-overview.md) -외부 (관리 평면)에서 구독의 각 Azure 리소스에 대 한 작업에 대 한 통찰력을 제공 합니다. 가상 컴퓨터의 경우 이러한 정보는 시작 된 시간과 구성을 변경 하는 것과 같은 정보를 포함 합니다.


### <a name="guest-operating-system"></a>게스트 운영 체제
가상 컴퓨터의 게스트 운영 체제에서 데이터를 수집 하려면 각 가상 컴퓨터에서 로컬로 실행 되 고 Azure Monitor 데이터를 전송 하는 에이전트가 필요 합니다. 서로 다른 데이터를 수집 하 고 다른 위치에 데이터를 기록 하는 Azure Monitor에 여러 에이전트를 사용할 수 있습니다. [Azure Monitor 에이전트 개요](../platform/agents-overview.md)에서 다른 에이전트에 대 한 자세한 비교를 확인 하세요. 

- [Log Analytics 에이전트](../platform/agents-overview.md#log-analytics-agent) -Azure의 가상 컴퓨터, 다른 클라우드 환경 및 온-프레미스에서 사용할 수 있습니다. Azure Monitor 로그에 대 한 데이터를 수집 합니다. VM용 Azure Monitor 및 모니터링 솔루션을 지원 합니다. System Center Operations Manager에 사용 되는 것과 동일한 에이전트입니다.
- [종속성 에이전트](../platform/agents-overview.md#dependency-agent) -가상 머신에서 실행 되는 프로세스 및 해당 종속성에 대 한 데이터를 수집 합니다. 는 Log Analytics 에이전트를 사용 하 여 Azure로 데이터를 전송 하 고 VM용 Azure Monitor, 서비스 맵 및 Wire Data 2.0 솔루션을 지원 합니다.
- [Azure 진단 확장](../platform/agents-overview.md#azure-diagnostics-extension) -Azure Monitor 가상 컴퓨터에만 사용할 수 있습니다. 는 여러 위치로 데이터를 수집할 수 있지만 주로 게스트 성능 데이터를 Windows 가상 머신에 대 한 Azure Monitor 메트릭으로 수집 하는 데 사용 됩니다.
- [Telegraf agent](../platform/collect-custom-metrics-linux-telegraf.md) -Linux vm에서 Azure Monitor 메트릭에 대 한 성능 데이터를 수집 합니다.


## <a name="configuration-requirements"></a>구성 요구 사항
가상 컴퓨터를 모니터링 하는 Azure Monitor의 모든 기능을 사용 하도록 설정 하려면 가상 컴퓨터 호스트 및 게스트 운영 체제에서 [Azure Monitor 메트릭과](../platform/data-platform-logs.md) [Azure Monitor 로그](../platform/data-platform-logs.md)에 대 한 모니터링 데이터를 수집 해야 합니다. 다음 표에서는이 컬렉션을 사용 하도록 설정 하기 위해 수행 해야 하는 구성을 나열 합니다. 특정 요구 사항에 따라 이러한 단계를 모두 수행 하지 않도록 선택할 수 있습니다.

| 구성 단계 | 작업 완료 됨 | 기능 사용 |
|:---|:---|:---|
| 구성 없음 | -메트릭에 수집 된 호스트 플랫폼 메트릭.<br>-활동 로그가 수집 되었습니다. | -호스트의 메트릭 탐색기<br>-호스트에 대 한 메트릭 경고입니다.<br>-활동 로그 경고입니다. |
| [VM용 Azure Monitor 사용](#enable-azure-monitor-for-vms) | -Log Analytics 에이전트가 설치 되어 있습니다.<br>-종속성 에이전트가 설치 되었습니다.<br>-로그에 수집 된 게스트 성능 데이터입니다.<br>-로그에 수집 된 프로세스 및 종속성 세부 정보입니다. | -게스트 성능 데이터에 대 한 성능 차트 및 통합 문서입니다.<br>-게스트 성능 데이터에 대 한 로그 쿼리<br>-게스트 성능 데이터에 대 한 로그 경고입니다.<br>-종속성 맵 |
| [진단 확장 및 telegraf 에이전트 설치](#enable-diagnostics-extension-and-telegraf-agent) | -메트릭에 수집 된 게스트 성능 데이터입니다. | -게스트에 대 한 메트릭 탐색기<br>-게스트에 대 한 메트릭 경고입니다.  |
| [Log Analytics 작업 영역 구성](#configure-log-analytics-workspace) | -게스트에서 수집 된 이벤트. | -게스트 이벤트에 대 한 로그 쿼리<br>-게스트 이벤트에 대 한 로그 경고입니다. |
| [가상 컴퓨터에 대 한 진단 설정 만들기](#collect-platform-metrics-and-activity-log) | -로그에 수집 된 플랫폼 메트릭입니다.<br>-로그에 수집 된 활동 로그입니다. | -Loq 쿼리 호스트 메트릭<br>-호스트 메트릭에 대 한 로그 경고입니다.<br>-활동 로그에 대 한 로그 쿼리

이러한 각 구성 단계는 다음 섹션에 설명 되어 있습니다.

### <a name="enable-azure-monitor-for-vms"></a>VM용 Azure Monitor 사용
[VM용 Azure Monitor](vminsights-overview.md) 은 Azure Monitor [에서 가상](insights-overview.md) 컴퓨터를 모니터링 하는 기본 도구인 Azure Monitor에 대 한 정보입니다. 표준 Azure Monitor 기능에 대해 다음과 같은 추가 가치를 제공 합니다.

- 가상 컴퓨터 게스트 운영 체제 및 워크 로드의 모니터링을 사용 하도록 설정 하는 Log Analytics 에이전트 및 종속성 에이전트의 등록을 간소화 했습니다. 
- 가상 컴퓨터의 게스트 운영 체제에서 핵심 성능 메트릭을 분석할 수 있는 미리 정의 된 추세 성능 차트 및 통합 문서입니다.
- 각 가상 머신에서 실행 중인 프로세스와 다른 컴퓨터 및 외부 원본과 상호 연결 된 구성 요소를 표시 하는 종속성 맵입니다.

![VM용 Azure Monitor](media/monitor-vm-azure/vminsights-01.png)

![VM용 Azure Monitor](media/monitor-vm-azure/vminsights-02.png)


Azure Portal의 virtual machine 메뉴에 있는 **Insights** 옵션에서 VM용 Azure Monitor를 사용 하도록 설정 합니다. 세부 정보 및 기타 구성 방법에 대해서는 [VM용 Azure Monitor 사용 개요](vminsights-enable-overview.md) 를 참조 하세요.

![VM용 Azure Monitor 사용](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Log Analytics 작업 영역 구성
VM용 Azure Monitor에서 사용 하는 Log Analytics 에이전트는 데이터를 [Log Analytics 작업 영역](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured)으로 보냅니다. Log Analytics 작업 영역을 구성 하 여 에이전트에서 추가 성능 데이터, 이벤트 및 기타 모니터링 데이터의 수집을 사용 하도록 설정할 수 있습니다. 작업 영역에 연결 하는 에이전트가 자동으로 구성을 다운로드 하 고 정의 된 데이터 수집을 즉시 시작 하므로 한 번만 구성 하면 됩니다. 

**시작**에서 **작업 영역 구성** 을 선택 하 여 VM용 Azure Monitor에서 직접 작업 영역에 대 한 구성에 액세스할 수 있습니다. 작업 영역 이름을 클릭 하 여 해당 메뉴를 엽니다.

![작업 영역 구성](media/monitor-vm-azure/workspace-configuration.png)

작업 영역 메뉴에서 **고급 설정** 을 선택 하 고 데이터 원본을 구성 하려면 **데이터** 를 선택 합니다. Windows 에이전트의 경우 **Windows 이벤트 로그** 를 선택 하 고 *시스템* 및 *응용 프로그램과*같은 일반적인 이벤트 로그를 추가 합니다. Linux 에이전트의 경우 **Syslog** 를 선택 하 고 *커닝* 및 *디먼*과 같은 일반적인 기능을 추가 합니다. 사용할 수 있는 데이터 원본의 목록과이를 구성 하는 방법에 대 한 자세한 내용은 [Azure Monitor의 에이전트 데이터 원본](../platform/agent-data-sources.md) 을 참조 하세요. 

![이벤트 구성](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> 작업 영역 구성에서 수집 되는 성능 카운터를 구성할 수 있지만이는 VM용 Azure Monitor에서 수집 된 성능 카운터와 중복 될 수 있습니다. VM용 Azure Monitor는 분당 1 회 빈도에서 가장 일반적인 카운터 집합을 수집 합니다. VM용 Azure Monitor에서 아직 수집 하지 않은 카운터를 수집 하려는 경우 또는 성능 데이터를 사용 하는 기존 쿼리가 있는 경우에만 작업 영역에서 수집 되는 성능 카운터를 구성 합니다.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>진단 확장 및 Telegraf 에이전트 사용
VM용 Azure Monitor는 Log Analytics 작업 영역으로 데이터를 수집 하는 Log Analytics 에이전트를 기반으로 합니다. 이는 [로그 쿼리](../log-query/log-query-overview.md), [로그 경고](../platform/alerts-log.md)및 [통합 문서](../platform/workbooks-overview.md)와 같은 [Azure Monitor의 여러 기능](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 을 지원 합니다. [진단 확장](../platform/diagnostics-extension-overview.md) 은 Windows 가상 컴퓨터의 게스트 운영 체제에서 성능 데이터를 수집 하 여 Azure Storage 하 고 선택적으로 성능 데이터를 [Azure Monitor 메트릭에](../platform/data-platform-metrics.md)보냅니다. Linux 가상 머신의 경우 Azure 메트릭에 데이터를 전송 하려면 [Telegraf 에이전트가](../platform/collect-custom-metrics-linux-telegraf.md) 필요 합니다.  이를 통해 [메트릭 탐색기](../platform/metrics-getting-started.md) 및 [메트릭 알림과](../platform/alerts-metric.md)같은 Azure Monitor의 다른 기능을 사용할 수 있습니다. Azure Event Hubs를 사용 하 여 Azure Monitor 외부에서 이벤트 및 성능 데이터를 보내도록 진단 확장을 구성할 수도 있습니다.

VM 메뉴의 **진단 설정** 옵션에서 Azure Portal 단일 Windows 가상 머신에 대 한 진단 확장을 설치 합니다. **싱크** 탭에서 **Azure Monitor** 사용 하도록 설정 하는 옵션을 선택 합니다. 여러 가상 컴퓨터에 대해 템플릿이나 명령줄에서 확장을 사용 하도록 설정 하려면 [설치 및 구성](../platform/diagnostics-extension-overview.md#installation-and-configuration)을 참조 하세요. Log Analytics 에이전트와는 달리 수집할 데이터는 각 가상 컴퓨터의 확장에 대 한 구성에 정의 되어 있습니다.

![진단 설정](media/monitor-vm-azure/diagnostic-setting.png)

Linux 가상 머신에서 Telegraf 에이전트를 구성 하는 방법에 대 한 자세한 내용은 [Telegraf 설치 및 구성](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) 을 참조 하세요. **진단 설정** 메뉴 옵션은 Linux에서 사용할 수 있지만 Azure storage로 데이터를 보낼 수만 있습니다.

### <a name="collect-platform-metrics-and-activity-log"></a>플랫폼 메트릭 및 활동 로그 수집
Azure Portal에서 각 가상 컴퓨터 호스트에 대해 수집 된 플랫폼 메트릭 및 활동 로그를 볼 수 있습니다. 이 데이터를 VM용 Azure Monitor와 동일한 Log Analytics 작업 영역으로 수집 하 여 가상 머신에 대해 수집 된 다른 모니터링 데이터를 분석 합니다. 이 컬렉션은 [진단 설정](../platform/diagnostic-settings.md)으로 구성 됩니다. [구독에 대 한 진단 설정을](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)사용 하 여 활동 로그를 수집 합니다.

가상 컴퓨터에 대 한 진단 설정을 사용 하 여 플랫폼 메트릭을 수집 합니다. 다른 Azure 리소스와 달리 Azure Portal에서 가상 머신에 대 한 진단 설정을 만들 수는 없지만 [다른 방법을](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)사용 해야 합니다. 다음 예에서는 PowerShell 및 CLI를 사용 하 여 가상 컴퓨터에 대 한 메트릭을 수집 하는 방법을 보여 줍니다.

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
가상 컴퓨터에 대 한 모니터링 데이터 컬렉션을 구성 하 고 나면 Azure Portal에서 액세스할 수 있는 여러 옵션이 있습니다.

- **Azure Monitor** 메뉴를 사용 하 여 모니터링 되는 모든 리소스의 데이터에 액세스할 수 있습니다. 
- 규모에 맞게 가상 머신 집합을 모니터링 하기 위해 VM용 Azure Monitor를 사용 합니다.
- Azure Portal의 메뉴에서 단일 가상 컴퓨터에 대 한 데이터를 분석 합니다. 아래 표에는 virtual machines 메뉴 모니터링을 위한 다양 한 옵션이 나와 있습니다.

![Azure Portal에서 모니터링](media/monitor-vm-azure/monitor-menu.png)

| 메뉴 옵션 | Description |
|:---|:---|
| 개요 | 가상 컴퓨터 호스트의 [플랫폼 메트릭을](../platform/data-platform-metrics.md) 표시 합니다. [메트릭 탐색기](../platform/metrics-getting-started.md)에서이 데이터로 작업 하려면 그래프를 클릭 합니다. |
| 활동 로그 | 현재 가상 머신에 대해 필터링 된 [활동 로그](../platform/activity-log-view.md) 항목입니다. |
| 자세한 정보 | 현재 선택 된 가상 컴퓨터의 맵과 함께 [VM용 Azure Monitor](../insights/vminsights-overview.md) 를 엽니다. |
| 경고 | 현재 가상 컴퓨터에 대 한 [경고](../platform/alerts-overview.md) 를 봅니다.  |
| 메트릭 | 범위가 현재 가상 머신으로 설정 된 [메트릭 탐색기](../platform/metrics-getting-started.md) 를 엽니다. |
| 진단 설정 | 현재 가상 컴퓨터에 대 한 [진단 확장](../platform/diagnostics-extension-overview.md) 을 사용 하도록 설정 하 고 구성 합니다. |
| Advisor 권장 사항 | [Azure Advisor](/azure/advisor/)에서 현재 가상 컴퓨터에 대 한 권장 사항입니다. |
| 로그 | [범위가](../log-query/scope.md) 현재 가상 머신으로 설정 된 [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) 를 엽니다. |
| 연결 모니터 | [Network Watcher 연결 모니터](../../network-watcher/connection-monitor-preview.md) 를 열어 현재 가상 머신과 다른 가상 머신 간의 연결을 모니터링 합니다. |


## <a name="analyzing-metric-data"></a>메트릭 데이터 분석
가상 컴퓨터의 메뉴에서 **메트릭을** 열어 메트릭 탐색기를 사용 하 여 가상 컴퓨터에 대 한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대 한 자세한 내용은 [Azure 메트릭 탐색기 시작](../platform/metrics-getting-started.md) 을 참조 하세요. 

가상 컴퓨터에서 메트릭에 사용 되는 두 개의 네임 스페이스는 다음과 같습니다.

| 네임스페이스 | Description |
|:---|:---|
| 가상 머신 호스트 | 모든 Azure virtual machines에 대해 자동으로 수집 된 호스트 메트릭입니다. [Microsoft. Compute/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines)의 자세한 메트릭 목록입니다. |
| 가상 컴퓨터 게스트 | 진단 확장을 설치 하 고 Azure Monitor 싱크로 보내도록 구성 된 가상 머신에서 수집 된 게스트 운영 체제 메트릭입니다. |

![메트릭](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>로그 데이터 분석
Azure virtual machines는 다음 데이터를 수집 하 여 로그를 Azure Monitor 합니다. 

VM용 Azure Monitor *InsightsMetrics* 테이블에 기록 되는 미리 정의 된 성능 카운터 집합을 수집할 수 있도록 합니다. 이는 [컨테이너에 대 한 Azure Monitor](container-insights-overview.md)에서 사용 하는 것과 동일한 테이블입니다. 

| 데이터 원본 | 요구 사항 | 테이블 |
|:---|:---|:---|
| VM용 Azure Monitor | 각 가상 머신에서를 사용 하도록 설정 합니다. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>자세한 내용은 [VM용 Azure Monitor에서 로그를 쿼리 하는 방법을](vminsights-log-search.md) 참조 하세요. |
| 활동 로그 | 구독에 대 한 진단 설정입니다. | AzureActivity |
| 호스트 메트릭 | 가상 컴퓨터에 대 한 진단 설정입니다. | AzureMetrics |
| 게스트 운영 체제의 데이터 원본 | Log Analytics 에이전트를 사용 하도록 설정 하 고 데이터 원본을 구성 합니다. | 각 데이터 원본에 대 한 설명서를 참조 하세요. |


> [!NOTE]
> Log Analytics 에이전트에서 수집 하는 성능 데이터는 VM용 Azure Monitor를 *InsightsMetrics* 테이블에 수집 하는 동안 *성능 테이블에* 기록 됩니다. 이는 동일한 데이터 이지만 테이블의 구조가 다릅니다. *성능*기반 쿼리를 사용 하는 경우 *InsightsMetrics*를 사용 하도록 다시 작성 해야 합니다.


## <a name="alerts"></a>경고
Azure Monitor에서 [경고](../platform/alerts-overview.md) 는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 미리 알리고 논리 앱을 시작 하거나 웹 후크를 호출 하는 등의 작업을 실행할 수 있습니다. 경고 규칙은 경고를 만들어야 하는 시기를 결정 하는 데 사용 되는 논리를 정의 합니다. Azure Monitor는 경고 규칙에서 사용 하는 데이터를 수집 하지만 Azure 구독에서 경고 조건을 정의 하는 규칙을 만들어야 합니다.

다음 섹션에서는 각각을 사용 해야 하는 경우에 대 한 경고 규칙 및 권장 사항의 유형을 설명 합니다. 이 권장 사항은 경고 규칙 유형의 기능 및 비용을 기반으로 합니다. 경고에 대 한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.


### <a name="activity-log-alert-rules"></a>활동 로그 경고 규칙
활동 로그 [경고 규칙](../platform/alerts-activity-log.md) 은 활동 로그에서 특정 조건과 일치 하는 항목을 만들 때 발생 합니다. 필요한 논리가 활동 로그에 있는 경우 처음으로 선택 해야 하므로 비용이 들지 않습니다. 

활동 로그 경고에 대 한 대상 리소스는 특정 가상 머신, 리소스 그룹의 모든 가상 머신 또는 구독의 모든 가상 머신 일 수 있습니다.

예를 들어 신호 이름에 대해 *전원 끄기 가상 머신을* 선택 하 여 중요 한 가상 머신이 중지 된 경우 경고를 만듭니다.

![활동 로그 경고](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>메트릭 경고 규칙
메트릭 [경고 규칙](../platform/alerts-metric.md) 은 메트릭 값이 임계값을 초과할 때 발생 합니다. 특정 임계값을 정의 하거나 Azure Monitor를 사용 하 여 기록 데이터를 기반으로 임계값을 동적으로 결정할 수 있습니다.  메트릭 데이터를 사용 하는 경우에는 더 저렴 하 고 로그 경고 규칙 보다 응답성이 높은 메트릭 데이터를 사용 합니다. 메트릭이 임계값 아래로 떨어질 때 스스로 해결 하는 상태 저장 의미 이기도 합니다.

활동 로그 경고에 대 한 대상 리소스는 특정 가상 머신 또는 리소스 그룹의 모든 가상 머신 일 수 있습니다.

예를 들어 가상 머신의 프로세서가 특정 값을 초과 하는 경우 경고를 만들려면 신호 유형으로 *CPU 백분율* 을 사용 하는 메트릭 경고 규칙을 만듭니다. 특정 임계값을 설정 하거나 Azure Monitor 허용 하 여 동적 임계값을 설정 합니다. 

![메트릭 경고](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>로그 경고
[로그 경고 규칙](../platform/alerts-log.md) 은 예약 된 로그 쿼리의 결과가 특정 조건과 일치 하는 경우에 발생 합니다. 로그 쿼리 경고는 가장 비용이 많이 들고 최소한의 경고 규칙에 대 한 응답성 이지만 가장 다양 한 데이터에 액세스할 수 있으며 다른 경고 규칙에서 수행할 수 없는 복잡 한 논리를 수행할 수 있습니다. 

로그 쿼리의 대상 리소스는 Log Analytics 작업 영역입니다. 쿼리에서 특정 컴퓨터를 필터링 합니다.

예를 들어 특정 리소스 그룹의 가상 컴퓨터가 오프 라인 상태 인지 확인 하는 경고를 만들려면 지난 10 분 동안 하트 비트가 누락 된 각 컴퓨터에 대 한 레코드를 반환 하는 다음 쿼리를 사용 합니다. 하나 이상의 컴퓨터에 하트 비트가 누락 된 경우 발생 하는 임계값 1을 사용 합니다.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![로그 경고](media/monitor-vm-azure/log-alert-01.png)

구독의 모든 Windows 가상 머신에서 실패 한 로그온 횟수가 과도 하 게 발생 한 경우 경고를 만들려면 지난 시간에 실패 한 각 로그온 이벤트에 대 한 레코드를 반환 하는 다음 쿼리를 사용 합니다. 허용 되는 로그인 실패 횟수로 설정 된 임계값을 사용 합니다. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![로그 경고](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
SCOM (System Center Operations Manager)은 가상 컴퓨터의 워크 로드에 대 한 세부적인 모니터링을 제공 합니다. 모니터링 플랫폼과 구현에 대 한 다양 한 전략의 비교는 [클라우드 모니터링 가이드](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) 를 참조 하세요.

기존 SCOM 환경을 계속 사용 하려는 경우 Azure Monitor와 통합 하 여 추가 기능을 제공할 수 있습니다. Azure Monitor에서 사용 하는 Log Analytics 에이전트는 SCOM에 사용 되는 것과 동일 하 게 모니터링 하 여 가상 컴퓨터에서 두 데이터를 모두 전송 합니다. VM용 Azure Monitor에 에이전트를 추가 하 고 위에 지정 된 대로 추가 데이터를 수집 하도록 작업 영역을 구성 해야 하지만 가상 컴퓨터는 수정 하지 않고 SCOM 환경에서 기존 관리 팩을 계속 실행할 수 있습니다.

기존 SCOM 기능을 보강 하는 Azure Monitor 기능은 다음과 같습니다.

- Log Analytics를 사용 하 여 로그 및 성능 데이터를 대화형으로 분석할 수 있습니다.
- 로그 경고를 사용 하 여 여러 가상 컴퓨터에서 경고 조건을 정의 하 고 SCOM에서 경고를 사용할 수 없는 장기적인 추세를 사용 합니다.   

기존 SCOM 관리 그룹을 Log Analytics 작업 영역에 연결 하는 방법에 대 한 자세한 내용은 [Azure Monitor에 연결 Operations Manager을](../platform/om-agents.md) 참조 하세요.


## <a name="next-steps"></a>다음 단계

* [로그 쿼리를 사용 하 여 Azure Monitor 로그의 데이터를 분석 하는 방법을 알아봅니다.](../log-query/get-started-queries.md)
* [Azure Monitor에서 메트릭과 로그를 사용 하 여 경고에 대해 알아봅니다.](../platform/alerts-overview.md)

