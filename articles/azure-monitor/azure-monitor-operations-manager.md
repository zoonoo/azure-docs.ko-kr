---
title: 기존 Operations Manager 고객에 대 한 Azure Monitor
description: Operations Manager의 기존 사용자에 대 한 지침을 통해 클라우드로 전환 하는 동안 특정 작업에 대 한 모니터링을 Azure Monitor로 전환할 수 있습니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: c213a38286de05df5c3be8e3498bcca4ab6e1fbf
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736148"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>기존 Operations Manager 고객에 대 한 Azure Monitor
이 문서는 현재 [System Center Operations Manager](/system-center/scom/welcome) 를 사용 하 고 비즈니스 응용 프로그램 및 기타 리소스를 Azure로 마이그레이션할 때 [Azure Monitor](overview.md) 전환을 계획 하는 고객을 위한 지침을 제공 합니다. 이 문서에서는 비즈니스 및 IT 운영 요구 사항을 손상 시 키 지 않고 최대한 많은 Operations Manager 기능을 Azure Monitor 최대한 활용 하 여 클라우드로 완전히 전환 하는 것으로 가정 합니다. 

이 문서에서 만든 권장 사항은 Azure Monitor 변경 되 고 기능 Operations Manager 추가 됩니다. 하지만 기본 전략은 일관 되 게 유지 됩니다.

> [!IMPORTANT]
> 여기에 설명 된 여러 Azure Monitor 기능을 구현 하는 데 비용이 듭니다. 따라서 전체 환경에서 배포 하기 전에 해당 값을 평가 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 문서에서는 이미 [Operations Manager](/system-center/scom) 를 사용 하 고 있으며 최소한 [Azure Monitor](overview.md)에 대 한 기본적인 지식이 있다고 가정 합니다. 둘 간의 전체 비교는 [클라우드 모니터링 가이드: 플랫폼 모니터링 개요](/azure/cloud-adoption-framework/manage/monitor/platform-overview)를 참조 하세요. 이 문서에서는 여기에 설명 된 몇 가지 권장 사항을 이해 하는 데 도움이 되는 두 가지 간의 특정 기능 차이점에 대해 자세히 설명 합니다. 


## <a name="general-strategy"></a>일반 전략
플랫폼은 근본적으로 다르기 때문에 자산을 Operations Manager에서 Azure Monitor으로 변환 하는 마이그레이션 도구가 없습니다. 대신 마이그레이션을 사용 하 여 Operations Manager를 계속 사용 하면서 [표준 Azure Monitor 구현을](deploy.md) 구성 합니다. 다른 응용 프로그램 및 구성 요소에 대 한 요구 사항을 충족 하도록 Azure Monitor를 사용자 지정 하 고 더 많은 기능을 사용할 수 있으므로 Operations Manager에서 다른 관리 팩과 에이전트를 사용 중지할 수 있습니다.

이 문서에서 권장 하는 일반적인 전략은 클라우드 [모니터링 가이드](/azure/cloud-adoption-framework/manage/monitor/)와 동일 합니다. 클라우드 모니터링 전략은 클라우드로 점진적 전환을 수행할 수 있도록 하는 [하이브리드 클라우드 모니터링](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) 전략을 권장 합니다. 일부 기능이 중복 될 수 있지만,이 전략을 사용 하면 새 플랫폼에 보다 친숙 하 게 기존 비즈니스 프로세스를 유지 관리할 수 있습니다. Azure Monitor으로 바꿀 수 있으므로 Operations Manager 기능에서 벗어나 이동 합니다. 여러 모니터링 도구를 사용 하면 복잡성이 증가 하지만, 온-프레미스 또는 다른 클라우드에 있을 수 있는 서버 소프트웨어 및 인프라 구성 요소를 모니터링 하 Operations Manager 기능을 유지 하면서 차세대 클라우드 워크 로드를 모니터링 하는 Azure Monitor 기능을 활용할 수 있습니다. 


## <a name="components-to-monitor"></a>모니터링할 구성 요소
각각에 대해 고유한 모니터링 전략을 결정 하기 위해 모니터링 해야 하는 다양 한 유형의 작업을 분류 하는 데 도움이 됩니다. [클라우드 모니터링 가이드: 모니터링 전략](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) 작성은 레거시 엔터프라이즈 응용 프로그램에서 클라우드의 최신 응용 프로그램으로 진행 하면서 모니터링이 필요한 환경에서 다양 한 계층에 대 한 자세한 분석을 제공 합니다.

클라우드 전에는 Operations Manager를 사용 하 여 모든 계층을 모니터링 했습니다. IaaS (Infrastructure as a Service)를 사용 하 여 전환을 시작 하는 경우 가상 머신에 대 한 Operations Manager를 계속 사용 하지만 클라우드 리소스에 대 한 Azure Monitor 사용을 시작 합니다. PaaS (Platform as a Service)를 사용 하 여 최신 응용 프로그램으로 전환 하는 경우 Azure Monitor에 더 집중 하 여 Operations Manager 기능 사용을 중지할 수 있습니다.


![클라우드 모델](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

이러한 계층은이 문서의 나머지 부분에서 자세히 설명 하는 다음과 같은 범주로 단순화할 수 있습니다. 사용자 환경의 모든 모니터링 워크 로드가 이러한 범주 중 하나에 적합 하지 않을 수 있지만, 일반적으로 적용 되는 일반적인 권장 사항에 따라 특정 범주에 충분 해야 합니다.

**비즈니스 응용 프로그램.** 비즈니스와 관련 된 기능을 제공 하는 응용 프로그램입니다. 내부 또는 외부에 있을 수 있으며 사용자 지정 코드를 사용 하 여 내부적으로 개발 되는 경우가 많습니다. 레거시 응용 프로그램은 일반적으로 Windows 또는 Linux를 실행 하는 가상 또는 물리적 컴퓨터에서 호스트 되 고, 최신 응용 프로그램은 azure의 응용 프로그램 서비스 (예: Azure Web Apps 및 Azure Functions)를 기반으로 합니다.

**Azure 서비스.** 클라우드로 마이그레이션된 비즈니스 응용 프로그램을 지 원하는 Azure의 리소스입니다. 여기에는 Azure Storage, Azure SQL 및 Azure IoT와 같은 서비스가 포함 됩니다. 여기에는 다른 Azure 서비스와 마찬가지로 모니터링 되기 때문에 Azure virtual machines도 포함 되지만 이러한 가상 컴퓨터의 게스트 운영 체제에서 실행 되는 응용 프로그램 및 소프트웨어에는 호스트 이외의 모니터링이 추가로 필요 합니다.

**서버 소프트웨어.** 비즈니스 응용 프로그램 또는 패키지 응용 프로그램을 지 원하는 가상 컴퓨터와 물리적 컴퓨터에서 실행 되는 소프트웨어로, 비즈니스에 일반 기능을 제공 합니다. 예로는 IIS (Internet Information Server), SQL Server, Exchange 및 SharePoint가 있습니다. 여기에는 가상 컴퓨터와 물리적 컴퓨터의 Windows 또는 Linux 운영 체제도 포함 됩니다.

**로컬 인프라.** 모니터링이 필요한 온-프레미스 환경에만 적용 되는 구성 요소입니다. 여기에는 물리적 서버, 저장소 및 네트워크 구성 요소와 같은 리소스가 포함 됩니다. 이러한 구성 요소는 클라우드로 이동할 때 가상화 됩니다.

## <a name="sample-walkthrough"></a>샘플 연습
다음은 Operations Manager에서 Azure Monitor로의 마이그레이션에 대 한 가상의 연습입니다. 이는 실제 마이그레이션의 전체 복잡성을 나타내는 것이 아니며, 최소한 기본 단계와 시퀀스를 제공 합니다. 아래 섹션에서는 이러한 각 단계에 대해 자세히 설명 합니다.

구성 요소를 Azure로 이동 하기 전의 환경은 온-프레미스 또는 관리 서비스 공급자가 있는 가상 컴퓨터와 물리적 컴퓨터를 기반으로 합니다. 사용자 환경에서 물리적 서버 및 네트워크와 같은 비즈니스 응용 프로그램, 서버 소프트웨어 및 기타 인프라 구성 요소를 모니터링 하는 Operations Manager에 의존 합니다. IIS, SQL Server 및 다양 한 공급 업체 소프트웨어와 같은 서버 소프트웨어용 표준 관리 팩을 사용 하 고 특정 요구 사항에 맞게 해당 관리 팩을 조정 합니다. 비즈니스 응용 프로그램 및 기존 관리 팩을 사용 하 여 모니터링할 수 없는 기타 구성 요소에 대 한 사용자 지정 관리 팩을 만들고 비즈니스 프로세스를 지원 하도록 Operations Manager를 구성 합니다.

Azure로의 마이그레이션은 IaaS에서 시작 하 여 비즈니스 응용 프로그램을 지 원하는 가상 머신을 Azure로 이동 합니다. 이러한 응용 프로그램 및 이러한 응용 프로그램에서 사용 하는 서버 소프트웨어에 대 한 모니터링 요구 사항은 변경 되지 않으며 기존 관리 팩을 사용 하 여 이러한 서버에서 Operations Manager 계속 사용 합니다. 

Azure 구독을 만드는 즉시 Azure 서비스에 대해 Azure Monitor를 사용할 수 있습니다. 플랫폼 메트릭과 활동 로그를 자동으로 수집 하 고, 로그 쿼리를 사용 하 여 사용 가능한 모든 원격 분석을 대화형으로 분석할 수 있도록 리소스 로그를 수집할 수 있도록 구성 합니다. 가상 컴퓨터에서 VM용 Azure Monitor를 사용 하 여 전체 환경에서 모니터링 데이터를 분석 하 고 컴퓨터와 프로세스 간의 관계를 검색할 수 있습니다. Azure Arc 사용 서버를 사용 하도록 설정 하 여 온-프레미스 물리적 및 가상 머신으로 Azure Monitor 사용을 확장 합니다. 

각 비즈니스 응용 프로그램에 대해 Application Insights를 사용 하도록 설정 합니다. 각 응용 프로그램의 다양 한 구성 요소를 식별 하 고, 사용 현황 및 성능 데이터를 수집 하기 시작 하 고, 코드에서 발생 하는 모든 오류를 식별 합니다. 외부 응용 프로그램을 사전에 테스트 하 고 성능 또는 가용성 문제에 대해 경고 하는 가용성 테스트를 만듭니다. Application Insights은 Operations Manager 없는 강력한 기능을 제공 하지만, 아직 Azure Monitor에 포함 되지 않은 모니터링 시나리오를 포함 하므로 비즈니스 응용 프로그램에 대해 개발한 사용자 지정 관리 팩을 계속 사용 합니다. 

Azure Monitor에 대해 잘 알고 있으면 일부 관리 팩 기능을 대체 하 고 새 모니터링 플랫폼을 사용 하기 위해 비즈니스 프로세스를 발전 시킬 수 있는 경고 규칙을 만들기 시작 합니다. 이렇게 하면 Operations Manager 관리 그룹에서 컴퓨터 및 관리 팩 제거를 시작할 수 있습니다. 중요 한 서버 소프트웨어 및 온-프레미스 인프라에 대 한 관리 팩을 계속 사용 하지만 추가 기능을 사용 중지 하는 데 사용할 수 있는 Azure Monitor의 새로운 기능을 계속 감시 합니다.

## <a name="monitor-azure-services"></a>Azure 서비스 모니터링
Azure 서비스는 실제로 원격 분석을 수집 하는 Azure Monitor 필요 하며, Azure 구독을 만들 때 사용 하도록 설정 됩니다. [활동 로그](platform/activity-log.md) 는 구독에 대해 자동으로 수집 되 고 [플랫폼 메트릭은](platform/data-platform-metrics.md) 사용자가 만든 모든 Azure 리소스에서 자동으로 수집 됩니다. 운영 콘솔의 성능 보기와 유사 하 게 [메트릭 탐색기](platform/metrics-getting-started.md)를 사용 하 여 바로 시작할 수 있지만 데이터의 대화형 분석과 [고급 집계](platform/metrics-charts.md) 를 제공 합니다. 값이 임계값을 초과 하는 경우 알림을 받을 [메트릭 경고를 만들거나](platform/alerts-metric.md) 표시를 위해 [Azure 대시보드에 차트를 추가](platform/metrics-charts.md#pinning-to-dashboards) 합니다.

[![메트릭 탐색기](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

각 리소스의 내부 작업에 대 한 세부 정보를 제공 하는 메트릭 및 [리소스 로그](platform/resource-logs.md)를 Log Analytics 작업 영역에 보내도록 각 Azure 리소스에 대 한 [진단 설정을 만듭니다](platform/diagnostic-settings.md) . 그러면 리소스에 대해 사용 가능한 모든 원격 분석이 제공 되며 [Log Analytics](log-query/log-analytics-overview.md) 를 사용 하 여 Operations Manager에 해당 하는 기능이 없는 고급 쿼리 언어를 사용 하 여 로그 및 성능 데이터를 대화형으로 분석할 수 있습니다. 또한 복잡 한 논리를 사용 하 여 경고 조건을 확인 하 고 여러 리소스 간에 데이터를 상호 연결 하는 [로그 쿼리 경고](platform/alerts-log-query.md)를 만들 수 있습니다.

[![로그 분석](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Azure Monitor의 [정보](monitor-reference.md) 는 특정 Azure 서비스에 대 한 고유한 모니터링을 제공 한다는 점에서 관리 팩과 비슷합니다. 현재는 네트워킹, 저장소 및 컨테이너를 포함 한 여러 서비스에 대 한 정보를 사용할 수 있으며 다른 서비스는 계속 추가 됩니다.

[![통찰력 예](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


통찰력은 메트릭 및 로그 쿼리를 풍부한 대화형 보고서에 결합 하는 Azure Monitor [통합 문서](platform/workbooks-overview.md) 를 기반으로 합니다. 운영 콘솔에서 사용자 지정 보기 및 보고서를 만드는 방법과 비슷한 방식으로 여러 서비스의 데이터를 결합 하는 통합 문서를 만듭니다.

### <a name="azure-management-pack"></a>Azure 관리 팩
[Azure 관리 팩](https://www.microsoft.com/download/details.aspx?id=50013) 을 사용 하 여 azure 리소스를 검색 하 고 특정 모니터링 시나리오 집합을 기반으로 상태를 모니터링할 Operations Manager 있습니다. 이 관리 팩을 통해 Azure의 각 리소스에 대 한 추가 구성을 수행 해야 하지만, Azure Monitor에 집중할 수 있도록 비즈니스 프로세스를 개선할 때까지 운영 콘솔에서 Azure 리소스를 어느 정도 볼 수 있도록 하는 것이 도움이 될 수 있습니다.

[![Azure 관리 팩](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 운영 콘솔에서 특정 Azure 리소스에 대 한 표시 유형을 원하는 경우 Azure 관리 팩을 사용 하 고 일부 기본 경고를 기존 프로세스와 통합 하도록 선택할 수 있습니다. 실제로 Azure Monitor에 의해 수집 된 데이터를 사용 합니다. Azure 리소스에 대 한 장기적인 전체 모니터링에 대 한 Azure Monitor를 확인 해야 합니다. 


## <a name="monitor-server-software-and-local-infrastructure"></a>서버 소프트웨어 및 로컬 인프라 모니터링
컴퓨터를 클라우드로 이동 하는 경우 해당 소프트웨어에 대 한 모니터링 요구 사항은 변경 되지 않습니다. 가상화 된 후에는 더 이상 물리적 구성 요소를 모니터링할 필요가 없지만 게스트 운영 체제와 해당 워크 로드는 환경에 관계 없이 동일한 요구 사항을 갖습니다.

[VM용 Azure Monitor](insights/vminsights-overview.md) 는 가상 컴퓨터 및 해당 게스트 운영 체제 및 워크 로드를 모니터링 하는 Azure Monitor의 기본 기능입니다. Operations Manager와 마찬가지로 VM용 Azure Monitor는 에이전트를 사용 하 여 가상 컴퓨터의 게스트 운영 체제에서 데이터를 수집 합니다. 이는 일반적으로 분석 및 경고를 위해 관리 팩에서 사용 하는 것과 동일한 성능 및 이벤트 데이터입니다. 그러나 이러한 컴퓨터에서 실행 되는 비즈니스 응용 프로그램 및 서버 소프트웨어에 대 한 문제를 식별 하 고 경고 하는 기존 규칙은 없습니다. 검색 된 문제를 사전에 알리도록 사용자 고유의 경고 규칙을 만들어야 합니다.

[![VM용 Azure Monitor 성능](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

또한 Azure Monitor는 가상 머신에서 실행 되는 다양 한 응용 프로그램 및 서비스의 상태를 측정 하지 않습니다. 메트릭 경고는 값이 Azure Monitor 임계값 아래로 떨어지면 현재 시스템에서 실행 되는 응용 프로그램 및 서비스에 대 한 상태 조건을 정의 하는 기능이 없는 경우에도 자동으로 해결할 수 있습니다. 또한 상태 롤업을 제공 하 여 관련 구성 요소의 상태를 그룹화 할 수 없습니다.

> [!NOTE]
> [VM용 Azure Monitor에 대 한 새로운 게스트 상태 기능은](insights/vminsights-health-overview.md) 현재 공개 미리 보기로 제공 되며 성능 메트릭 집합의 상태에 따라 경고를 생성 합니다. 이는 처음에는 게스트 운영 체제와 관련 된 특정 성능 카운터 집합으로 제한 되지만 응용 프로그램이 나 가상 머신에서 실행 되는 다른 워크 로드는 그렇지 않습니다.
> 
> [![VM용 Azure Monitor 게스트 상태](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

하이브리드 환경에서 컴퓨터의 소프트웨어를 모니터링 하는 것은 일반적으로 각 컴퓨터의 요구 사항에 따라 VM용 Azure Monitor와 Operations Manager의 조합을 사용 하 고 Azure Monitor에 대 한 운영 프로세스를 개발 하는 데 사용 됩니다. Microsoft Management Agent (Azure Monitor의 Log Analytics 에이전트 라고 함)는 두 플랫폼에서 모두 사용 되어 단일 컴퓨터를 동시에 모니터링할 수 있습니다.

> [!NOTE]
> 나중에 VM용 Azure Monitor은 현재 공개 미리 보기로 제공 되는 [Azure Monitor 에이전트로](platform/azure-monitor-agent-overview.md)전환 됩니다. 동일한 가상 컴퓨터를 두 플랫폼에서 계속 모니터링할 수 있도록 Microsoft Monitoring Agent와 호환 됩니다.

Azure Monitor에서 아직 제공할 수 없는 기능에 대 한 Operations Manager 계속 사용 합니다. 여기에는 IIS, SQL Server 또는 Exchange와 같은 중요 한 서버 소프트웨어에 대 한 관리 팩이 포함 됩니다. Azure Monitor를 사용 하 여 연결할 수 없는 온-프레미스 인프라에 대 한 사용자 지정 관리 팩을 개발할 수도 있습니다. 또한 Azure Monitor 및 기타 Azure 서비스를 보강 하거나 교체할 수 있는 서비스 작업을 현대화 전환할 수 있을 때까지 작업 프로세스에 긴밀 하 게 통합 된 경우 Operations Manager 계속 사용 합니다. 

Azure Monitor Vm을 사용 하 여 Operations Manager를 즉시 대체 하지 않는 경우에도 현재 모니터링을 향상 시킵니다. Azure Monitor에 고유한 기능의 예는 다음과 같습니다.

- 가상 컴퓨터와 해당 외부 종속성 간의 관계를 검색 하 고 모니터링 합니다.
- 대화형 차트와 통합 문서에 있는 여러 가상 컴퓨터에서 집계 된 성능 데이터를 봅니다.
- [로그 쿼리](log-query/log-query-overview.md) 를 사용 하 여 다른 Azure 리소스의 데이터를 사용 하 여 가상 머신에서 원격 분석을 대화형으로 분석할 수 있습니다.
- 여러 가상 컴퓨터에 걸친 복잡 한 논리를 기반으로 [로그 경고 규칙](platform/alerts-log-query.md) 을 만듭니다.

[![VM용 Azure Monitor 맵](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Azure virtual machines 외에도 [Azure Arc 사용 서버](../azure-arc/servers/overview.md)를 사용 하 여 온-프레미스 및 다른 클라우드에서 컴퓨터를 모니터링할 수 VM용 Azure Monitor. Arc 사용 서버를 사용 하면 Azure 외부, 회사 네트워크 또는 다른 클라우드 공급자에서 호스트 되는 Windows 및 Linux 컴퓨터를 관리할 수 있습니다.



## <a name="monitor-business-applications"></a>비즈니스 응용 프로그램 모니터링
일반적으로 각 가상 컴퓨터에 설치 된 에이전트를 활용 하 여 Operations Manager를 사용 하 여 비즈니스 응용 프로그램을 모니터링 하려면 사용자 지정 관리 팩이 필요 합니다. Azure Monitor의 Application Insights는 Azure, 다른 클라우드 또는 온-프레미스에 있든 관계 없이 웹 기반 응용 프로그램을 모니터링 하므로 Azure로 마이그레이션 되었는지 여부에 관계 없이 모든 응용 프로그램에 사용할 수 있습니다.

비즈니스 응용 프로그램에 대 한 모니터링이 Operations Manager의 [.net 앱 성능 템플릿에서]() 제공 하는 기능으로 제한 되는 경우 기능 손실 없이 Application Insights로 마이그레이션할 가능성이 높습니다. 실제로 Application Insights에는 다음을 포함 하 여 많은 수의 추가 기능이 포함 됩니다.

- 응용 프로그램 구성 요소를 자동으로 검색 하 고 모니터링 합니다.
- 응답 시간, 실패율 및 요청 속도와 같은 자세한 응용 프로그램 사용 현황 및 성능 데이터를 수집 합니다.
- 페이지 보기, 로드 성능 등의 브라우저 데이터를 수집 합니다.
- 예외를 검색 하 고 스택 추적 및 관련 요청을 자세히 살펴봅니다.
- [분산 추적](app/distributed-tracing.md) 및 [스마트 감지](app/proactive-diagnostics.md)와 같은 기능을 사용 하 여 고급 분석을 수행 합니다.
- [메트릭 탐색기](platform/metrics-getting-started.md) 를 사용 하 여 성능 데이터를 대화형으로 분석할 수 있습니다.
- [로그 쿼리](log-query/log-query-overview.md) 를 사용 하 여 Azure 서비스 및 VM용 Azure Monitor에 대해 수집 된 데이터와 함께 수집 된 원격 분석을 대화형으로 분석 합니다.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

필요한 기능을 얻을 수 있을 때까지 Application Insights 외에도 Operations Manager을 계속 사용 해야 하는 경우도 있습니다. Operations Manager를 계속 해야 할 수 있는 예는 다음과 같습니다.

-  응용 프로그램의 가용성 및 응답성을 모니터링 하 고 경고 하는 데 사용할 수 있는 [가용성 테스트](app/monitor-web-app-availability.md)는 웹 테스트 에이전트의 IP 주소에서 들어오는 요청이 필요 합니다. 정책에서 이러한 액세스를 허용 하지 않는 경우 Operations Manager에서 [웹 응용 프로그램 가용성 모니터](/system-center/scom/web-application-availability-monitoring-template) 를 계속 사용 해야 할 수 있습니다.
- Operations Manager 가용성 테스트에 대 한 폴링 간격을 설정할 수 있습니다. 많은 고객이 60-120 초 마다 확인 합니다. Application Insights은 5 분의 최소 폴링 간격으로, 일부 고객에 게는 너무 길 수 있습니다.
- Operations Manager에서 상당한 모니터링은 응용 프로그램에서 생성 된 이벤트를 수집 하 고 로컬 에이전트에서 스크립트를 실행 하 여 수행 됩니다. 이러한 옵션은 Application Insights의 표준 옵션이 아니므로 비즈니스 요구 사항을 충족 하기 위해 사용자 지정 작업이 필요할 수 있습니다. 여기에는 Log Analytics 작업 영역에 저장 된 이벤트 데이터를 사용 하는 사용자 지정 경고 규칙과 [하이브리드 runbook worker](../automation/automation-hybrid-runbook-worker.md)를 사용 하 여 가상 컴퓨터 게스트에서 시작 된 스크립트가 포함 될 수 있습니다.
- 응용 프로그램을 작성 하는 언어에 따라 [Application Insights에서 사용할 수 있는 계측](app/platforms.md)기능이 제한 될 수 있습니다.

이 가이드의 다른 섹션에 있는 기본 전략에 따라 비즈니스 응용 프로그램에 대 한 Operations Manager를 계속 사용 하지만 Application Insights에서 제공 하는 추가 기능을 활용할 수 있습니다. Azure Monitor 중요 한 기능을 대체할 수 있으므로 사용자 지정 관리 팩을 사용 중지 하기 시작할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- 하이브리드 모니터링 환경을 디자인 하 고 구현 하는 방법에 대 한 자세한 내용 및 Azure Monitor와 System Center Operations Manager에 대 한 자세한 내용은 [클라우드 모니터링 가이드](/azure/cloud-adoption-framework/manage/monitor/) 를 참조 하세요.
- [Azure 리소스 모니터링](insights/monitor-azure-resource.md)에 대 한 자세한 내용은 Azure Monitor를 참조 하세요.
- [Azure virtual machines 모니터링](insights/monitor-vm-azure.md)에 대 한 자세한 내용은 Azure Monitor를 참조 하세요.
- 자세한 내용은 [VM용 Azure Monitor](insights/vminsights-overview.md)를 참조 하세요.
- 자세한 내용은 [Application Insights](app/app-insights-overview.md)를 참조 하세요.