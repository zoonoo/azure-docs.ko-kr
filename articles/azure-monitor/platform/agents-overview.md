---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a9786c1f596a9f59e63886fa503bddac58ee7a8e
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325341"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor 에이전트 개요

가상 컴퓨터 및 기타 계산 리소스에는 모니터링 데이터를 수집 하 여 게스트 운영 체제 및 워크 로드의 성능 및 가용성을 측정 하는 에이전트가 필요 합니다. 이 문서에서는 Azure Monitor에서 사용 하는 에이전트를 설명 하 고 특정 환경에 대 한 요구 사항을 충족 하는 데 필요한 사항을 결정 하는 데 도움을 줍니다.

> [!NOTE]
> 현재 Azure Monitor 및 Log Analytics의 최근 통합으로 인해 Azure Monitor에는 여러 개의 에이전트가 있습니다. 기능이 겹칠 수 있지만 각각에는 고유한 기능이 있습니다. 요구 사항에 따라 가상 컴퓨터에서 하나 이상의 에이전트가 필요할 수 있습니다. 

특정 가상 컴퓨터에 대해 단일 에이전트로 완전히 충족 될 수 없는 특정 요구 사항 집합이 있을 수 있습니다. 예를 들어 Azure 진단 확장이 필요 하지만 Log Analytics 에이전트 및 종속성 에이전트가 필요한 VM용 Azure Monitor 기능을 활용 하려는 메트릭 경고를 사용할 수 있습니다. 이와 같은 경우 여러 에이전트를 사용할 수 있으며,이는 각각의 기능을 필요로 하는 고객을 위한 일반적인 시나리오입니다.

## <a name="summary-of-agents"></a>에이전트 요약

다음 표에서는 Windows 및 Linux에 대 한 Azure Monitor 에이전트의 빠른 비교를 제공 합니다. 각에 대 한 자세한 내용은 아래 섹션에 나와 있습니다. 

### <a name="windows-agents"></a>Windows 에이전트

| | 진단<br>확장 (WAD) | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|
| **지원 되는 환경** | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | 
| **에이전트 요구 사항**  | 없음 | 없음 | Log Analytics 에이전트가 필요 합니다. |
| **수집되는 데이터** | 이벤트 로그<br>ETW 이벤트<br>성능<br>파일 기반 로그<br>IIS 로그<br>.NET 앱 로그<br>크래시 덤프<br>에이전트 진단 로그 | 이벤트 로그<br>성능<IIS logs><br>파일 기반 로그<br>인사이트 및 솔루션<br>기타 서비스 | 프로세스 세부 정보 및 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Storage<br>Azure Monitor 메트릭<br>이벤트 허브 | Azure Monitor 로그 | Azure Monitor 로그 |


### <a name="linux-agents"></a>Linux 에이전트

| | 진단<br>확장 (꼬마) | Telegraf<br>에이전트 | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|
| **지원 되는 환경** | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 |
| **에이전트 요구 사항**  | 없음 | 없음 | 없음 | Log Analytics 에이전트가 필요 합니다. |
| **수집되는 데이터** | syslog<br>성능 | 성능 | syslog<br>성능| 프로세스 세부 정보 및 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Storage<br>이벤트 허브 | Azure Monitor 메트릭 | Azure Monitor 로그 | Azure Monitor 로그 |

## <a name="log-analytics-agent"></a>Log Analytics 에이전트

[Log Analytics 에이전트](log-analytics-agent.md) 는 게스트 운영 체제의 모니터링 데이터와 Azure, 다른 클라우드 공급자 및 온-프레미스의 가상 컴퓨터 작업을 수집 합니다. Log Analytics 작업 영역으로 데이터를 수집 합니다. Log Analytics 에이전트는 System Center Operations Manager에서 사용 하는 것과 동일한 에이전트 이며, 에이전트 컴퓨터를 관리 그룹과 통신 하 고 동시에 Azure Monitor 멀티홈 수 있습니다. 이 에이전트는 Azure Monitor의 특정 정보 및 솔루션에도 필요 합니다.


> [!NOTE]
> Windows 용 Log Analytics 에이전트를 종종 Microsoft Monitoring Agent (MMA) 라고 합니다. Linux 용 Log Analytics 에이전트를 OMS 에이전트 라고 합니다.



다음 작업을 수행 해야 하는 경우 Log Analytics 에이전트를 사용 합니다.

* Azure 외부의 가상 또는 물리적 컴퓨터에서 로그 및 성능 데이터를 수집 합니다. 
* Log Analytics 작업 영역으로 데이터를 전송 하 여 [로그 쿼리와](../log-query/log-query-overview.md)같은 [Azure Monitor 로그](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 에서 지원 되는 기능을 활용 합니다.
* 규모에 따라 가상 컴퓨터를 모니터링 하 고 다른 리소스 및 외부 프로세스에 대 한 프로세스 및 종속성을 모니터링 하는 데 사용할 수 있는 [VM용 Azure Monitor](../insights/vminsights-overview.md) 을 사용 합니다.  
* [Azure Security Center](../../security-center/security-center-intro.md) 또는 [Azure 센티널](../../sentinel/overview.md)을 사용 하 여 가상 컴퓨터의 보안을 관리 합니다.
* [Azure Automation 업데이트 관리](../../automation/automation-update-management.md), [Azure Automation 상태 구성](../../automation/automation-dsc-overview.md)또는 [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking.md) 를 사용 하 여 Azure vm의 포괄적인 관리 제공
* 다른 [솔루션](../monitor-reference.md#insights-and-core-solutions) 을 사용 하 여 특정 서비스 또는 응용 프로그램을 모니터링할 수 있습니다.

Log Analytics 에이전트의 제한 사항은 다음과 같습니다.

- Azure Monitor 메트릭, Azure Storage 또는 Azure Event Hubs에 데이터를 보낼 수 없습니다.

## <a name="azure-diagnostics-extension"></a>Azure 진단 확장

[Azure 진단 확장](diagnostics-extension-overview.md) 은 게스트 운영 체제에서 모니터링 데이터를 수집 하 고 Azure virtual machines 및 기타 계산 리소스의 작업을 수집 합니다. 주로 데이터를 Azure Storage 수집 하지만 데이터 싱크를 정의 하 여 Azure Monitor 메트릭과 Azure Event Hubs 같은 다른 대상으로 데이터를 보낼 수도 있습니다.

다음 작업을 수행 해야 하는 경우 Azure 진단 확장을 사용 합니다.

- 보관을 위해 Azure Storage 데이터를 보내거나 [Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같은 도구를 사용 하 여 분석 합니다.
- [메트릭 탐색기](metrics-getting-started.md) 를 사용 하 여 분석 하 고, 거의 실시간 [메트릭 경고](./alerts-metric-overview.md) 및 [자동 크기 조정](autoscale-overview.md) (Windows에만 해당)과 같은 기능을 활용 하기 위해 데이터를 [Azure Monitor 메트릭에](data-platform-metrics.md) 보냅니다.
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)를 사용 하 여 타사 도구로 데이터를 보냅니다.
- [부팅 진단을](../../virtual-machines/troubleshooting/boot-diagnostics.md) 수집 하 여 VM 부팅 문제를 조사 합니다.

Azure 진단 확장의 제한 사항은 다음과 같습니다.

- Azure 리소스에만 사용할 수 있습니다.
- Azure Monitor 로그에 데이터를 전송 하는 기능이 제한 됩니다.

## <a name="telegraf-agent"></a>Telegraf 에이전트

[InfluxData Telegraf 에이전트](collect-custom-metrics-linux-telegraf.md) 는 Linux 컴퓨터에서 Azure Monitor 메트릭에 대 한 성능 데이터를 수집 하는 데 사용 됩니다.

다음 작업을 수행 해야 하는 경우 Telegraf 에이전트를 사용 합니다.

* [메트릭 탐색기](metrics-getting-started.md) 를 사용 하 여 분석 하 고, 거의 실시간 [메트릭 경고](./alerts-metric-overview.md) 및 [자동 크기 조정](autoscale-overview.md) (Linux만 해당)과 같은 기능을 활용 하기 위해 데이터를 [Azure Monitor 메트릭에](data-platform-metrics.md) 보냅니다. 



## <a name="dependency-agent"></a>종속성 에이전트

종속성 에이전트는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 합니다. 

다음 작업을 수행 해야 하는 경우 종속성 에이전트를 사용 합니다.

* 지도 기능 [VM용 Azure Monitor](../insights/vminsights-overview.md) 또는 [서비스 맵](../insights/service-map.md) 솔루션을 사용 합니다.

종속성 에이전트를 사용 하는 경우 다음 사항을 고려 하세요.

- 종속성 에이전트를 사용 하려면 Log Analytics 에이전트가 동일한 가상 컴퓨터에 설치 되어 있어야 합니다.
- Linux Vm에서는 Azure 진단 확장 전에 Log Analytics 에이전트를 설치 해야 합니다.

## <a name="extensions-compared-to-agents"></a>에이전트와 비교 되는 확장

[Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md) 용 Log Analytics 확장은 Azure 가상 컴퓨터에 Log Analytics 에이전트를 설치 합니다. [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 용 Azure Monitor 종속성 확장은 Azure virtual machines에 종속성 에이전트를 설치 합니다. 이러한 에이전트는 위에서 설명한 것과 동일한 에이전트 이지만 [가상 머신 확장](../../virtual-machines/extensions/overview.md)을 통해 관리할 수 있습니다. 가능 하면 언제 든 지 확장을 사용 하 여 에이전트를 설치 하 고 관리 해야 합니다.


## <a name="next-steps"></a>다음 단계

각 에이전트에 대 한 자세한 내용은 다음을 참조 하세요.

- [Log Analytics 에이전트 개요](log-analytics-agent.md)
- [Azure Diagnostics 확장 개요](diagnostics-extension-overview.md)
- [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](collect-custom-metrics-linux-telegraf.md)

