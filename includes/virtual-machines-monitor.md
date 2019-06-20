---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: ac400c86af8236ff5d67b8b6fbf99f6f4b1d36c9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182176"
---
진단 및 로그 데이터를 수집, 보기 및 분석하여 VM을 모니터링하는 데 많은 기회를 활용할 수 있습니다. VM의 간단한 [모니터링](../articles/azure-monitor/overview.md)을 수행하려면 Azure Portal에서 VM에 대한 개요 화면을 사용할 수 있습니다. [확장](../articles/virtual-machines/windows/extensions-features.md)을 사용하여 추가 메트릭 데이터를 수집하도록 VM에서 진단을 구성할 수 있습니다. [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) 및 [Log Analytics](../articles/azure-monitor/log-query/log-query-overview.md)와 같은 더 많은 고급 모니터링 옵션을 사용할 수도 있습니다.

## <a name="diagnostics-and-metrics"></a>진단 및 메트릭 

Azure Portal, Azure CLI, Azure PowerShell 및 프로그래밍 API(애플리케이션 프로그래밍 인터페이스)에서 [메트릭](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)을 사용하여 [진단 데이터](https://docs.microsoft.com/cli/azure/vm/diagnostics)의 컬렉션을 설정하고 모니터링할 수 있습니다. 예를 들어 다음을 수행할 수 있습니다.

- **VM에 대한 기본 메트릭을 관찰합니다.** Azure Portal의 개요 화면에서 표시된 기본 메트릭은 CPU 사용량, 네트워크 사용량, 총 디스크 바이트 및 초당 디스크 작업을 포함합니다.

- **부트 진단의 수집을 활성화하고 Azure Portal을 사용하여 봅니다.** 자신의 이미지를 Azure로 가져 오거나 플랫폼 이미지 중 하나를 부팅 할 때 VM이 부팅 불가능한 상태가 되는 데에는 많은 이유가 있을 수 있습니다. 설정 화면의 모니터링 섹션 아래에서 부트 진단에 대한 **사용**을 클릭하여 VM을 만들 때 부트 진단을 쉽게 활성화할 수 있습니다.

    VM이 부팅할 때 부트 진단 에이전트에서는 부팅 출력을 캡처하여 Azure 저장소에 저장합니다. 이 데이터는 VM 부팅 문제를 해결하는 데 사용할 수 있습니다. 명령줄 도구에서 VM을 만들 때 부트 진단을 사용하도록 자동으로 설정되지 않습니다. 부팅 진단을 사용하도록 설정하기 전에 먼저 부팅 로그를 저장할 스토리지 계정을 만들어야 합니다. Azure Portal에서 부트 진단을 사용하도록 설정하면 저장소 계정은 자동으로 만들어집니다.

    VM을 만들 때 부트 진단을 활성화하지 않은 경우 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 또는 [Azure Resource Manager 템플릿](../articles/virtual-machines/windows/extensions-diagnostics-template.md)을 사용하여 나중에 항상 활성화할 수 있습니다.

- **게스트 OS 진단 데이터의 컬렉션을 활성화합니다.** VM을 만들 때 설정 화면에서 게스트 OS 진단을 활성화하는 기회가 있습니다. 진단 데이터의 컬렉션을 활성화하는 경우 [Linux용 IaaSDiagnostics 확장](../articles/virtual-machines/linux/diagnostic-extension.md) 또는 [Windows용 IaaSDiagnostics 확장](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)이 VM에 추가됩니다. 이를 통해 추가 디스크, CPU 및 메모리 데이터를 수집할 수 있습니다.

    수집된 진단 데이터를 사용하여 VM에 대한 자동 크기 조정을 구성할 수 있습니다. 데이터를 저장하고 성능이 정확하지 않을 때 알려주는 경고를 설정하도록 로그를 구성할 수 있습니다.

## <a name="alerts"></a>경고

특정 성능 메트릭을 기반으로 하는 [경고](../articles/azure-monitor/platform/alerts-overview.md)를 만들 수 있습니다. 경고를 받을 수 있는 경우의 예는 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지는 경우를 포함합니다. [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell) 또는 [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli)를 사용하여 [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md)에서 경고를 구성할 수 있습니다.

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md)는 영향을 주는 Azure 서비스에 문제가 있을 때 개인 설정된 지침 및 지원을 제공하고 예정된 계획된 유지 관리에 대해 준비하도록 도움을 줍니다. Azure Service Health는 대상 및 유연한 알림을 사용하여 사용자 및 팀에 경고합니다.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md)는 Azure 문제가 리소스에 영향을 줄 때 문제를 진단하고 지원을 받는 데 도움이 됩니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제와 관련된 도움이 필요할 때 기술 지원을 제공합니다.

## <a name="azure-activity-log"></a>Azure 동작 로그

[Azure 활동 로그](../articles/azure-monitor/platform/activity-logs-overview.md)는 Azure에서 발생한 구독 수준 이벤트에 대한 정보를 제공하는 구독 로그입니다. 로그에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. Azure Portal에서 활동 로그를 클릭하여 VM에 대한 로그를 확인할 수 있습니다.

활동 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- [활동 로그 이벤트에서 경고](../articles/azure-monitor/platform/activity-logs-overview.md)를 만듭니다.
- 타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)에서 수집하도록 [Event Hub로 스트리밍합니다](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md).
- [PowerBI 콘텐츠 팩](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)을 사용하여 PowerBI에서 분석합니다.
- 보관 또는 수동 검사를 위해 [저장소 계정에 저장합니다](../articles/azure-monitor/platform/archive-activity-log.md). 로그 프로필을 사용하여 보존 기간(일)을 지정할 수 있습니다.

[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 또는 [REST API 모니터](https://docs.microsoft.com/rest/api/monitor/)를 사용하여 활동 로그 데이터에 액세스할 수도 있습니다.

[Azure 진단 로그](../articles/azure-monitor/platform/diagnostic-logs-overview.md)는 해당 작업에 대한 풍부하고 빈번한 데이터를 제공하는 VM에서 내보낸 로그입니다. 진단 로그는 VM 내에서 수행된 작업에 대한 정보를 제공하여 활동 로그에서 다릅니다.

진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 감사 또는 수동 검사를 위해 [저장소 계정에 저장합니다](../articles/azure-monitor/platform/archive-diagnostic-logs.md). 리소스 진단 설정을 사용하여 보존 기간(일)을 지정할 수 있습니다.
- 타사 서비스 또는 사용자 지정 분석 솔루션(예: PowerBI)으로 수집을 위해 [Event Hubs로 스트림](../articles/azure-monitor/platform/diagnostic-logs-stream-event-hubs.md)합니다.
- [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md)를 사용하여 분석합니다.

## <a name="advanced-monitoring"></a>고급 모니터링

- [Azure Monitor](../articles/azure-monitor/overview.md)는 클라우드 및 온-프레미스 환경을 모니터링하여 해당 가용성 및 성능을 유지하는 서비스입니다. 클라우드 및 온-프레미스 환경에서 원격 분석의 수집, 분석 및 작업에 대한 포괄적인 솔루션을 제공합니다. 애플리케이션을 수행하는 방법과 애플리케이션 및 종속된 리소스에 영향을 주는 문제를 사전에 식별하는 방법을 파악할 수 있습니다. [Linux VM](../articles/virtual-machines/linux/extensions-oms.md) 또는 [Windows VM](../articles/virtual-machines/windows/extensions-oms.md)에 Log Analytics 에이전트를 설치하여 로그 데이터를 수집하고 Log Analytics 작업 영역에 저장하는 확장을 설치할 수 있습니다.

    Windows 및 Linux VM의 경우 로그를 수집하는 좋은 방법은 Log Analytics 에이전트를 설치하는 것입니다. VM에 Log Analytics 에이전트를 설치하는 가장 쉬운 방법은 [Log Analytics VM 확장](../articles/log-analytics/log-analytics-azure-vm-extension.md)을 사용하는 것입니다. 이 확장을 사용하면 설치 프로세스가 간소화되고 지정한 Log Analytics 작업 영역에 데이터를 전송하도록 에이전트가 자동으로 구성됩니다. 에이전트도 자동으로 업그레이드되므로 최신 기능 및 수정 사항을 받아볼 수 있습니다.

- [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md)를 통해 VM 및 위치한 네트워크와 관련된 연결된 리소스를 모니터링할 수 있습니다. [Linux VM](../articles/virtual-machines/linux/extensions-nwa.md) 또는 [Windows VM](../articles/virtual-machines/windows/extensions-nwa.md)에 Network Watcher 에이전트 확장을 설치할 수 있습니다.

- [VM용 Azure Monitor](../articles/azure-monitor/insights/vminsights-overview.md)는 Windows 및 Linux VM의 성능과 상태를 분석하여 대규모 Azure VM(가상 머신)을 모니터링하며, 여기에는 VM의 여러 프로세스, 그리고 다른 리소스 및 외부 프로세스에 대한 상호 연결된 종속성이 포함됩니다. 

## <a name="next-steps"></a>다음 단계
- [Azure PowerShell을 사용하여 Windows Virtual Machine 모니터링](../articles/virtual-machines/windows/tutorial-monitoring.md) 또는 [Azure CLI를 사용하여 Linux Virtual Machine 모니터링](../articles/virtual-machines/linux/tutorial-monitoring.md)의 단계를 안내합니다.
- [모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)에 대한 모범 사례에 대해 자세히 알아봅니다.
