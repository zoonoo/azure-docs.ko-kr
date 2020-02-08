---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069407"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Azure Monitor 에이전트 개요 
가상 머신과 같은 계산 리소스는 [다른 클라우드 리소스](../insights/monitor-azure-resource.md)와 마찬가지로 성능 및 가용성을 모니터링 하는 데이터를 생성 합니다. 또한 계산 리소스에는 게스트 운영 체제와 모니터링 해야 하는 워크 로드가 있습니다. 리소스 내부에서이 모니터링 데이터를 수집 하려면 에이전트가 필요 합니다. 이 문서에서는 Azure Monitor에서 사용 하는 에이전트를 설명 하 고 특정 환경에 대 한 요구 사항을 충족 하는 데 필요한 사항을 결정 하는 데 도움을 줍니다.

## <a name="summary-of-agents"></a>에이전트 요약

> [!NOTE]
> 현재 Azure Monitor 및 Log Analytics의 최근 통합으로 인해 Azure Monitor에는 여러 개의 에이전트가 있습니다. 두 에이전트는 일부 기능을 공유 하지만 특정 에이전트에 대해서는 다른 기능이 고유 합니다. 요구 사항에 따라 에이전트 중 하나 또는 둘 다가 필요할 수 있습니다. 

Azure Monitor에는 각각 특정 기능을 제공 하는 3 개의 에이전트가 있습니다. 요구 사항에 따라 가상 머신 및 기타 계산 리소스에 단일 에이전트 또는 여러 개를 설치할 수 있습니다.

* [Azure Diagnostics 확장](#azure-diagnostic-extension)
* [Log Analytics 에이전트](#log-analytics-agent)
* [종속성 에이전트](#dependency-agent)

다음 표에서는 여러 에이전트를 빠르게 비교 하 여 보여 줍니다. 각 항목에 대 한 자세한 내용은이 문서의 나머지 부분을 참조 하세요.

| | Azure 진단 확장 | Log Analytics 에이전트 | 종속성 에이전트 |
|:---|:---|:---|:---|
| 지원 되는 환경 | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 |
| 운영 체제 | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| 에이전트 종속성  | None | None | Log Analytics 에이전트가 필요 합니다. |
| 수집되는 데이터 | 이벤트 로그<br>ETW 이벤트<br>syslog<br>성능<br>IIS 로그<br>출력 로그를 추적하는 .NET 앱<br>크래시 덤프 | 이벤트 로그<br>syslog<br>성능<br>IIS 로그<br>사용자 지정 로그<br>솔루션의 데이터 | 프로세스 세부 정보 및 종속성<br>네트워크 연결 메트릭 |
| 데이터 전송 대상 | Azure Storage<br>Azure Monitor 메트릭<br>이벤트 허브 | Azure Monitor 로그 | Azure Monitor 로그 |



## <a name="azure-diagnostic-extension"></a>Azure 진단 확장
[Azure 진단 확장](../../azure-monitor/platform/diagnostics-extension-overview.md) 은 게스트 운영 체제에서 모니터링 데이터를 수집 하 고 Azure compute 리소스의 작업을 수집 합니다. 주로 Azure Storage으로 데이터를 수집 합니다. 저장소에서 Log Analytics 작업 영역으로 데이터를 복사 하도록 Azure Monitor를 구성할 수 있습니다. Azure Monitor 메트릭에 게스트 성능 데이터를 수집할 수도 있습니다.

Azure 진단 확장은 종종 Windows Azure 진단 (WAD) 또는 Linux Azure 진단 (꼬마) 확장 이라고 합니다.


### <a name="scenarios-supported"></a>지원 되는 시나리오

Azure 진단 확장에서 지 원하는 시나리오는 다음과 같습니다.

* Azure compute 리소스에서 로그 및 성능 데이터를 수집 합니다.
* 게스트 운영 체제의 로그 및 성능 데이터를 Azure storage에 보관 합니다.
* [Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같은 도구를 사용 하 여 저장소의 모니터링 데이터를 봅니다.
* 메트릭 데이터베이스의 성능 데이터를 수집 하 여 거의 실시간 [메트릭 경고](../../azure-monitor/platform/alerts-metric-overview.md) 및 [자동 크기](autoscale-overview.md)조정과 같은 [Azure Monitor 메트릭에](data-platform-metrics.md) 의해 지원 되는 기능을 활용 합니다. 
* [저장소에서 Log Analytics 작업 영역으로](azure-storage-iis-table.md) 모니터링 데이터를 수집 하 여 [로그 쿼리와](../log-query/log-query-overview.md)같은 [Azure Monitor 로그](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 에서 지원 되는 기능을 활용 합니다.
* [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)를 사용 하 여 타사 도구로 모니터링 데이터를 보냅니다.
* [부팅 진단](../../virtual-machines/troubleshooting/boot-diagnostics.md)을 사용하여 VM 부팅 문제를 조사합니다.
* VM에서 실행 되는 응용 프로그램의 데이터를 다른 응용 프로그램 모니터링과 통합 하기 위해 [Application Insights로](diagnostics-extension-to-application-insights.md) 복사 합니다.

## <a name="log-analytics-agent"></a>Log Analytics 에이전트
[Log Analytics 에이전트](log-analytics-agent.md) 는 게스트 운영 체제의 모니터링 데이터와 Azure, 다른 클라우드 공급자 및 온-프레미스의 가상 컴퓨터 작업을 수집 합니다. Log Analytics 작업 영역으로 데이터를 수집 합니다.

Log Analytics 에이전트는 System Center Operations Manager에서 사용 하는 것과 동일한 에이전트 이며, 멀티홈 에이전트 컴퓨터에서 관리 그룹과 통신 하 고 동시에 Azure Monitor 합니다. 이 에이전트는 Azure Monitor의 특정 솔루션에도 필요 합니다.

Windows 용 Log Analytics 에이전트를 종종 MMA (Microsoft Management Agent) 라고 합니다. Linux 용 Log Analytics 에이전트를 OMS 에이전트 라고 합니다.


### <a name="scenarios-supported"></a>지원 되는 시나리오

Log Analytics 에이전트에서 지 원하는 시나리오는 다음과 같습니다.

* Azure, 다른 클라우드 공급자 및 온-프레미스의 가상 머신에서 로그 및 성능 데이터를 수집 합니다. 
* Log Analytics 작업 영역에 대 한 모니터링 데이터를 수집 하 여 [로그 쿼리와](../log-query/log-query-overview.md)같은 [Azure Monitor 로그](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 에서 지원 되는 기능을 활용 합니다.
* 컨테이너 등에 대해 [VM용 Azure Monitor](../insights/vminsights-overview.md), [Azure Monitor 등의](../insights/container-insights-overview.md)Azure Monitor 모니터링 솔루션을 사용 합니다.  
* Log Analytics 에이전트가 필요한 [Azure 센티널](../../sentinel/overview.md) 을 사용 하 여 가상 컴퓨터의 보안을 관리 합니다.
* Log Analytics 에이전트가 필요한 [Azure Security Center](../../security-center/security-center-intro.md) 를 사용 하 여 가상 컴퓨터의 보안을 관리 합니다.
* [Azure Automation](../../automation/automation-intro.md) 기능을 사용 하 여 수명 주기 동안 Azure vm의 포괄적인 관리를 제공 합니다.  Log Analytics 에이전트를 필요로 하는 이러한 기능의 예는 다음과 같습니다.
  * 운영 체제 업데이트의 [Azure Automation 업데이트 관리](../../automation/automation-update-management.md).
  * 일관성 있는 구성 상태를 유지 하기 위해 [상태 구성을 Azure Automation](../../automation/automation-dsc-overview.md) 합니다.
  * [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking.md)를 사용하여 구성 변경 내용을 추적합니다.

## <a name="dependency-agent"></a>종속성 에이전트
종속성 에이전트는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 합니다. 이 에이전트는 [서비스 맵](../insights/service-map.md) 및 맵 기능 [VM용 Azure Monitor](../insights/vminsights-overview.md)필요 합니다. 종속성 에이전트는 Log Analytics 에이전트가 필요 하 고 Azure Monitor의 Log Analytics 작업 영역에 데이터를 씁니다.


## <a name="using-multiple-agents"></a>여러 에이전트 사용
특정 가상 컴퓨터에 대 한 Azure 진단 확장 또는 Log Analytics 에이전트를 사용 하기 위한 특정 요구 사항이 있을 수 있습니다. 예를 들어 Azure 진단 확장을 요구 하는 메트릭 경고를 사용 하는 것이 좋습니다. 그러나 종속성 에이전트 및 Log Analytics 에이전트가 필요한 VM용 Azure Monitor의 맵 기능을 사용할 수도 있습니다. 이 경우 여러 에이전트를 사용할 수 있으며,이는 각각의 기능을 필요로 하는 고객을 위한 일반적인 시나리오입니다.

### <a name="considerations"></a>고려 사항

- 종속성 에이전트를 사용 하려면 Log Analytics 에이전트가 동일한 가상 컴퓨터에 설치 되어 있어야 합니다.
- Linux Vm에서는 Azure 진단 확장 전에 Log Analytics 에이전트를 설치 해야 합니다.


## <a name="next-steps"></a>다음 단계

- Azure, 데이터 센터 또는 기타 클라우드 환경에서 호스팅되는 컴퓨터에 에이전트를 배포하기 위한 요구 사항 및 지원되는 방법을 검토하려면 [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)를 참조하세요.

