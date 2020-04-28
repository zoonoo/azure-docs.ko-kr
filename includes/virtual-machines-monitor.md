---
title: 포함 파일
description: 포함 파일
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865620"
---
Azure에서 호스팅되는 VM이 크게 증가함에 따라 지원하는 응용 프로그램 및 인프라 서비스에 영향을 주는 성능 및 상태 문제를 식별하는 것이 중요합니다. 기본 모니터링은 기본적으로 Azure에서 메트릭 유형 CPU 사용량, 디스크 사용률, 메모리 사용률 및 호스트 하이퍼바이저에서 수집한 네트워크 트래픽에 의해 제공됩니다. [확장 프로그램을](../articles/virtual-machines/windows/extensions-features.md) 사용하여 추가 메트릭 및 로그 데이터를 수집하여 게스트 운영 체제에서 VM의 진단을 구성할 수 있습니다.

게스트 운영 체제, .NET 기반 또는 Java 웹 응용 프로그램 구성 요소에서 실행되는 게스트 운영 체제의 성능 및 상태 문제를 검색하고 진단하기 위해 Azure Monitor는 VM용 Azure 모니터 및 응용 프로그램 인사이트와 같은 포괄적인 기능을 통해 중앙 집중식 모니터링을 제공합니다.

## <a name="diagnostics-and-metrics"></a>진단 및 메트릭 

Azure Portal, Azure CLI, Azure PowerShell 및 프로그래밍 API(애플리케이션 프로그래밍 인터페이스)에서 [메트릭](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md)을 사용하여 [진단 데이터](https://docs.microsoft.com/cli/azure/vm/diagnostics)의 컬렉션을 설정하고 모니터링할 수 있습니다. 예를 들어, 다음을 수행할 수 있습니다.

- **VM에 대한 기본 메트릭을 관찰합니다.** Azure Portal의 개요 화면에서 표시된 기본 메트릭은 CPU 사용량, 네트워크 사용량, 총 디스크 바이트 및 초당 디스크 작업을 포함합니다.

- **부트 진단의 수집을 활성화하고 Azure Portal을 사용하여 봅니다.** 자신의 이미지를 Azure로 가져 오거나 플랫폼 이미지 중 하나를 부팅 할 때 VM이 부팅 불가능한 상태가 되는 데에는 많은 이유가 있을 수 있습니다. 설정 화면의 모니터링 섹션 아래에서 부트 진단에 대한 **사용**을 클릭하여 VM을 만들 때 부트 진단을 쉽게 활성화할 수 있습니다.

    VM이 부팅할 때 부트 진단 에이전트에서는 부팅 출력을 캡처하여 Azure Storage에 저장합니다. 이 데이터는 VM 부팅 문제를 해결하는 데 사용할 수 있습니다. 명령줄 도구에서 VM을 만들 때 부트 진단을 사용하도록 자동으로 설정되지 않습니다. 부팅 진단을 사용하도록 설정하기 전에 먼저 부팅 로그를 저장할 스토리지 계정을 만들어야 합니다. Azure Portal에서 부트 진단을 사용하도록 설정하면 스토리지 계정은 자동으로 만들어집니다.

    VM을 만들 때 부트 진단을 활성화하지 않은 경우 [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) 또는 [Azure Resource Manager 템플릿](../articles/virtual-machines/windows/extensions-diagnostics-template.md)을 사용하여 나중에 항상 활성화할 수 있습니다.

- **게스트 OS 진단 데이터의 컬렉션을 활성화합니다.** VM을 만들 때 설정 화면에서 게스트 OS 진단을 활성화하는 기회가 있습니다. 진단 데이터의 컬렉션을 활성화하는 경우 [Linux용 IaaSDiagnostics 확장](../articles/virtual-machines/linux/diagnostic-extension.md) 또는 [Windows용 IaaSDiagnostics 확장](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)이 VM에 추가됩니다. 이를 통해 추가 디스크, CPU 및 메모리 데이터를 수집할 수 있습니다.

    수집된 진단 데이터를 사용하여 VM에 대한 자동 크기 조정을 구성할 수 있습니다. 데이터를 저장하도록 [Azure Monitor Logs를](../articles/azure-monitor/platform/data-platform-logs.md) 구성하고 성능이 맞지 않을 때 알려주도록 경고를 설정할 수도 있습니다.

## <a name="alerts"></a>경고

특정 성능 메트릭을 기반으로 [경고를](../articles/azure-monitor/platform/alerts-overview.md) 만들 수 있습니다. 경고를 받을 수 있는 경우의 예는 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지는 경우를 포함합니다. Azure 리소스 관리자 템플릿 또는 [Azure CLI를](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)사용하여 [Azure 포털에서](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)경고를 구성할 수 [있습니다.](../articles/azure-monitor/platform/alerts-metric-create-templates.md)

## <a name="azure-service-health"></a>Azure Service Health

[Azure Service Health](../articles/service-health/service-health-overview.md)는 영향을 주는 Azure 서비스에 문제가 있을 때 개인 설정된 지침 및 지원을 제공하고 예정된 계획된 유지 관리에 대해 준비하도록 도움을 줍니다. Azure Service Health는 대상 및 유연한 알림을 사용하여 사용자 및 팀에 경고합니다.

## <a name="azure-resource-health"></a>Azure Resource Health

[Azure Resource Health](../articles/service-health/resource-health-overview.md)는 Azure 문제가 리소스에 영향을 줄 때 문제를 진단하고 지원을 받는 데 도움이 됩니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제와 관련된 도움이 필요할 때 기술 지원을 제공합니다.

## <a name="azure-activity-log"></a>Azure 동작 로그

[Azure 활동 로그](../articles/azure-monitor/platform/platform-logs-overview.md)는 Azure에서 발생한 구독 수준 이벤트에 대한 정보를 제공하는 구독 로그입니다. 로그에는 Azure Resource Manager 작동 데이터에서 서비스 상태 이벤트 업데이트에 이르기까지 광범위한 데이터가 포함됩니다. Azure Portal에서 활동 로그를 클릭하여 VM에 대한 로그를 확인할 수 있습니다.

활동 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 활동 [로그 이벤트에 경고를](../articles/azure-monitor/platform/platform-logs-overview.md)만듭니다.
- Power BI와 같은 타사 서비스 또는 사용자 지정 분석 솔루션으로 수집할 수 있도록 [이벤트 허브로 스트리밍합니다.](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md)
- Power BI 콘텐츠 팩을 사용하여 [Power BI에서](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)분석합니다.
- 보관 또는 수동 검사를 [위해 저장소 계정에 저장합니다.](../articles/azure-monitor/platform/archive-activity-log.md) 로그 프로필을 사용하여 보존 기간(일)을 지정할 수 있습니다.

[Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor) 또는 [REST API 모니터](https://docs.microsoft.com/rest/api/monitor/)를 사용하여 활동 로그 데이터에 액세스할 수도 있습니다.

[Azure 리소스 로그는](../articles/azure-monitor/platform/platform-logs-overview.md) VM에서 내보낸 로그로, 해당 작업에 대한 풍부하고 빈번한 데이터를 제공합니다. 리소스 로그는 VM 내에서 수행된 작업에 대한 통찰력을 제공하여 활동 로그와 다릅니다.

진단 로그를 통해 수행할 수 있는 몇 가지 작업은 다음과 같습니다.

- 감사 또는 수동 검사를 [위해 저장소 계정에 저장합니다.](../articles/azure-monitor/platform/archive-diagnostic-logs.md) 리소스 진단 설정을 사용하여 보존 기간(일)을 지정할 수 있습니다.
- Power BI와 같은 타사 서비스 또는 사용자 지정 분석 솔루션으로 수집할 수 있도록 [이벤트 허브로 스트리밍합니다.](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [로그 분석으로](../articles/log-analytics/log-analytics-azure-storage.md)분석합니다.

## <a name="advanced-monitoring"></a>고급 모니터링

Azure VM 및 가상 시스템 규모 집합에서 지원하는 응용 프로그램 또는 서비스의 가시성을 위해 VM에서 실행 중인 게스트 OS 또는 워크로드에 대한 문제를 식별하여 응용 프로그램의 가용성 또는 성능에 영향을 미치는지 또는 응용 프로그램에 문제가 있는지 파악하려면 VM 및 [응용 프로그램 인사이트용](../articles/azure-monitor/app/app-insights-overview.md) [Azure 모니터를](../articles/azure-monitor/insights/vminsights-overview.md) 모두 사용하도록 설정합니다.

VM용 Azure Monitor는 발견된 다른 리소스 및 외부 프로세스에 대한 다양한 프로세스 및 상호 연결된 종속성을 포함하여 Windows 및 Linux VM의 성능과 상태를 분석하여 대규모로 Azure 가상 시스템(VM)을 모니터링합니다. 여기에는 문제를 조사하는 데 도움이 되고 VM의 용량을 평가하는 데 도움이 되는 몇 가지 추세 성능 차트가 포함되어 있습니다. 종속성 맵에는 모니터링되는 컴퓨터와 모니터링되지 않는 컴퓨터, 프로세스와 이러한 컴퓨터 간의 실패한 네트워크 연결 및 활성 네트워크 연결이 표시되고 표준 네트워크 연결 메트릭이 있는 추세 차트가 표시됩니다. 응용 프로그램 인사이트와 결합하면 응용 프로그램을 모니터링하고 HTTP 요청, 예외 등과 같은 원격 분석을 캡처하므로 VM과 응용 프로그램 간의 문제를 상호 연관시킬 수 있습니다. Azure 모니터 경고를 구성하여 VM용 Azure [Monitor에서](../articles/azure-monitor/platform/alerts-overview.md) 수집한 모니터링 데이터에서 검색된 중요한 조건에 대해 경고합니다.

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell을 사용하여 Windows Virtual Machine 모니터링](../articles/virtual-machines/windows/tutorial-monitoring.md) 또는 [Azure CLI를 사용하여 Linux Virtual Machine 모니터링](../articles/virtual-machines/linux/tutorial-monitoring.md)의 단계를 안내합니다.

- [모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)에 대한 모범 사례에 대해 자세히 알아봅니다.
