---
title: Azure 모니터로 Azure 리소스 모니터링 | 마이크로 소프트 문서
description: Azure 모니터를 사용하여 Azure의 리소스에서 모니터링 데이터를 수집하고 분석하는 방법을 설명합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249270"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure 모니터를 통해 Azure 리소스 모니터링
Azure 리소스에 의존하는 중요한 응용 프로그램 및 비즈니스 프로세스가 있는 경우 해당 리소스의 가용성, 성능 및 운영에 대해 모니터링해야 합니다. 이 문서에서는 Azure 리소스에서 생성된 모니터링 데이터와 Azure Monitor의 기능을 사용하여 이 데이터를 분석하고 경고하는 방법에 대해 설명합니다.

> [!IMPORTANT]
> 이 문서는 Azure 모니터를 사용하는 Azure의 모든 서비스에 적용됩니다. VM 및 앱 서비스를 포함한 컴퓨팅 리소스는 여기에 설명된 것과 동일한 모니터링 데이터를 생성하지만 로그 및 메트릭을 생성할 수도 있는 게스트 운영 체제도 있습니다. 이 데이터를 수집하고 분석하는 방법에 대한 자세한 내용은 이러한 서비스에 대한 모니터링 설명서를 참조하십시오.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Monitor는 Azure의 전체 스택 모니터링 서비스로, 다른 클라우드 및 온-프레미스의 리소스 외에도 Azure 리소스를 모니터링하는 전체 기능 집합을 제공합니다. [Azure Monitor 데이터 플랫폼은](../platform/data-platform.md) 다음 섹션에 설명된 전체 모니터링 도구 집합을 사용하여 데이터를 [로그](../platform/data-platform-logs.md) 및 [메트릭으로](../platform/data-platform-metrics.md) 수집하여 함께 분석할 수 있습니다.

- [Azure 모니터 메트릭으로 무엇을 할 수 있습니까?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Azure 모니터 로그로 무엇을 할 수 있습니까?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Azure 리소스를 만드는 즉시 Azure Monitor가 활성화되고 [Azure 포털에서 보고 분석할](#monitoring-in-the-azure-portal)수 있는 메트릭 및 활동 로그 수집을 시작합니다. 일부 구성에서는 추가 모니터링 데이터를 수집하고 추가 기능을 활성화할 수 있습니다. 구성 요구 사항에 대한 자세한 내용은 아래 [모니터링 데이터를](#monitoring-data) 참조하십시오.


## <a name="costs-associated-with-monitoring"></a>모니터링과 관련된 비용
기본적으로 수집되는 모니터링 데이터를 분석하는 데는 비용이 들지 않습니다. 여기에는 다음이 포함됩니다.

- 플랫폼 메트릭을 수집하고 메트릭 탐색기를 통해 분석합니다.
- Azure 포털에서 활동 로그를 수집하고 분석합니다.
- 활동 로그 경고 규칙 만들기.

로그 및 메트릭을 수집하고 내보내는 데 는 Azure Monitor 비용이 없지만 대상과 관련된 관련 비용이 있을 수 있습니다.

- 로그 분석 작업 영역에서 로그 및 메트릭을 수집할 때 데이터 수집 및 보존과 관련된 비용입니다. [로그 분석에 대한 Azure 모니터 가격을](https://azure.microsoft.com/pricing/details/monitor/)참조하십시오.
- Azure 저장소 계정에 대한 로그 및 메트릭을 수집할 때 데이터 저장소와 관련된 비용입니다. [Blob 저장소에 대한 Azure 저장소 가격을](https://azure.microsoft.com/pricing/details/storage/blobs/)참조하십시오.
- Azure 이벤트 허브로 로그 및 메트릭을 전달할 때 이벤트 허브 스트리밍과 관련된 비용입니다. [Azure 이벤트 허브 가격 을](https://azure.microsoft.com/pricing/details/event-hubs/)참조하십시오.

다음과 관련된 Azure Monitor 비용이 있을 수 있습니다. [Azure 모니터 가격](https://azure.microsoft.com/pricing/details/monitor/)참조 :

- 로그 쿼리 실행.
- 메트릭 또는 로그 쿼리 경고 규칙을 만듭니다.
- 경고 규칙에서 알림을 보냅니다.
- API를 통해 메트릭에 액세스합니다.

## <a name="monitoring-data"></a>데이터 모니터링
Azure의 리소스는 다음 다이어그램에 표시된 [로그](../platform/data-platform-logs.md) 및 [메트릭을 생성합니다.](../platform/data-platform-metrics.md) 생성되는 특정 데이터와 해당 서비스가 제공하는 추가 솔루션 또는 통찰력에 대한 각 Azure 서비스에 대한 설명서를 참조하십시오.

![개요](media/monitor-azure-resource/logs-metrics.png)



- [플랫폼 메트릭](../platform/data-platform-metrics.md) - 일정한 간격으로 자동으로 수집되고 특정 시간에 리소스의 일부 측면을 설명하는 수치 값입니다. 
- [리소스 로그](../platform/platform-logs-overview.md) - 키 볼트에서 비밀을 얻거나 데이터베이스에 대한 요청을 하는 등 Azure 리소스(데이터 평면) 내에서 수행된 작업에 대한 통찰력을 제공합니다. 리소스 로그의 내용과 구조는 Azure 서비스 및 리소스 유형에 따라 다릅니다.
- [활동 로그](../platform/platform-logs-overview.md) - 새 리소스를 만들거나 가상 컴퓨터를 시작하는 등 외부(관리 평면)에서 구독의 각 Azure 리소스에 대한 작업에 대한 통찰력을 제공합니다. 이 정보는 구독의 리소스에서 수행된 쓰기 작업(PUT, POST, DELETE)에 대해 누가, 누가, 언제 사용할 수 있는지에 대한 정보입니다.


## <a name="configuration-requirements"></a>구성 요구 사항

### <a name="configure-monitoring"></a>모니터링 구성
일부 모니터링 데이터는 자동으로 수집되지만 요구 사항에 따라 일부 구성을 수행해야 할 수 있습니다. 모니터링 데이터의 각 유형에 대한 특정 정보는 아래 정보를 참조하십시오.

- [플랫폼 메트릭](../platform/data-platform-metrics.md) - 구성없이 플랫폼 메트릭이 [Azure Monitor 메트릭으로](../platform/data-platform-metrics.md) 자동으로 수집됩니다. Azure 모니터 로그에 항목을 보내거나 Azure 외부에서 전달할 진단 설정을 만듭니다.
- [리소스 로그](../platform/platform-logs-overview.md) - 리소스 로그는 Azure 리소스에 의해 자동으로 생성되지만 진단 설정 없이는 수집되지 않습니다.  Azure 모니터 로그에 항목을 보내거나 Azure 외부에서 전달할 진단 설정을 만듭니다.
- [활동 로그](../platform/platform-logs-overview.md) - 활동 로그는 구성없이 자동으로 수집되며 Azure 포털에서 볼 수 있습니다. 진단 설정을 만들어 Azure 모니터 로그에 복사하거나 Azure 외부로 전달합니다.

### <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Azure 모니터 로그에 데이터를 수집하려면 로그 분석 작업 영역이 필요합니다. 새 작업 영역을 만들어 서비스를 신속하게 모니터링할 수 있지만 다른 서비스에서 데이터를 수집하는 작업 영역을 사용할 때 가치가 있을 수 있습니다. 요구 사항에 가장 적합한 작업 영역 디자인을 결정하는 데 도움이 되는 작업 영역 만들기 및 [Azure Monitor Logs 배포 디자인에](../platform/design-logs-deployment.md) 대한 자세한 내용은 [Azure 포털에서 로그 분석 작업 영역 만들기를](../learn/quick-create-workspace.md) 참조하십시오. 조직의 기존 작업 영역을 사용하는 경우 [Azure Monitor의 로그 데이터 및 작업 영역에 대한 액세스 관리에](../platform/manage-access.md)설명된 대로 적절한 권한이 필요합니다. 





## <a name="diagnostic-settings"></a>진단 설정
진단 설정은 특정 리소스에 대한 리소스 로그 및 메트릭을 보낼 위치를 정의합니다. 가능한 목적지는 다음과 같습니다.

- [강력한 로그](../platform/resource-logs-collect-workspace.md) 쿼리를 사용하여 Azure Monitor에서 수집한 다른 모니터링 데이터로 데이터를 분석하고 로그 경고 및 시각화와 같은 다른 Azure Monitor 기능을 활용할 수 있는 로그 분석 작업 영역입니다. 
- [타사](../platform/resource-logs-stream-event-hubs.md) SIEM 및 기타 로그 분석 솔루션과 같은 외부 시스템으로 데이터를 스트리밍하는 이벤트 허브입니다. 
- 감사, 정적 분석 또는 백업에 유용한 [Azure 저장소 계정입니다.](../platform/resource-logs-collect-storage.md)

진단 만들기 설정의 절차에 따라 [Azure에서 플랫폼 로그 및 메트릭을 수집하여 Azure](../platform/diagnostic-settings.md) 포털을 통해 진단 설정을 만들고 관리합니다. [리소스 관리자 템플릿을 사용하여 Azure에서 진단 설정 만들기를](../platform/diagnostic-settings-template.md) 참조하여 템플릿에서 정의하고 리소스가 생성될 때 리소스에 대한 전체 모니터링을 사용하도록 설정합니다.


## <a name="monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링
 Azure 포털의 리소스 메뉴에서 대부분의 Azure 리소스에 대한 모니터링 데이터에 액세스할 수 있습니다. 이렇게 하면 표준 Azure Monitor 도구를 사용하여 단일 리소스의 데이터에 액세스할 수 있습니다. 일부 Azure 서비스는 서로 다른 옵션을 제공하므로 추가 정보는 해당 서비스에 대한 설명서를 참조해야 합니다. Azure **모니터** 메뉴를 사용하여 모니터링되는 모든 리소스의 데이터를 분석합니다. 

### <a name="overview"></a>개요
많은 서비스가 작업을 한눈에 파악할 수 있도록 **개요** 페이지에 모니터링 데이터를 포함할 것입니다. 개요는 일반적으로 Azure Monitor 메트릭에 저장된 플랫폼 메트릭의 하위 세트를 기반으로 합니다. 다른 모니터링 옵션은 일반적으로 서비스의 **모니터링** 섹션에서 사용할 수 있습니다. 있습니다.

![개요 페이지](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>인사이트 및 솔루션 
일부 서비스는 Azure Monitor의 표준 기능을 벗어난 도구를 제공합니다. [Insights는](../insights/insights-overview.md) Azure Monitor 데이터 플랫폼 및 표준 기능을 기반으로 구축된 사용자 지정된 모니터링 환경을 제공합니다. [솔루션은](../insights/solutions.md) Azure Monitor Logs에 구축된 미리 정의된 모니터링 논리를 제공합니다. 

서비스에 Azure Monitor 인사이트가 있는 경우 각 리소스의 메뉴에서 **모니터링에서** 액세스할 수 있습니다. Azure 모니터 메뉴의 모든 인사이트 및 솔루션에 **액세스합니다.**

![자세한 정보](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>메트릭
대부분의 서비스에 대한 메트릭 메뉴 항목에서 사용할 수 있는 [메트릭 탐색기를](../platform/metrics-getting-started.md) 사용하여 Azure 포털의 **메트릭을 분석합니다.** 이 도구를 사용하면 개별 메트릭으로 작업하거나 여러 메트릭을 결합하여 상관 관계 및 추세를 식별할 수 있습니다. 

- 메트릭 탐색기 사용의 기본 사항에 대한 [Azure 메트릭 탐색기 시작을](../platform/metrics-getting-started.md) 참조하십시오.
- 여러 메트릭을 사용하고 필터 및 분할을 적용하는 등 메트릭 탐색기의 고급 기능에 대한 [Azure 메트릭 탐색기의 고급 기능을](../platform/metrics-charts.md) 참조하십시오.

![메트릭](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>활동 로그 
현재 리소스로 설정된 초기 필터를 사용하여 Azure Portal의 활동 로그에서 항목을 봅니다. 활동 로그를 Log Analytics 작업 영역에 복사하여 로그 쿼리 및 통합 문서에서 사용할 수 있습니다. 

- 다양한 방법을 사용하여 활동 로그를 보고 항목을 검색하는 방법에 대한 자세한 내용은 [Azure Activity 로그 이벤트 보기](../platform/activity-log-view.md) 및 검색을 참조하세요.
- 기록되는 특정 이벤트에 대한 Azure 서비스에 대한 설명서를 참조하십시오.

![활동 로그](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 로그
Azure Monitor Logs는 강력한 쿼리 도구를 사용하여 분석을 위해 여러 서비스 및 기타 데이터 원본의 로그 및 메트릭을 통합합니다. 위에서 설명한 대로 Azure Monitor의 Log Analytics 작업 영역에 플랫폼 메트릭, 활동 로그 및 리소스 로그를 수집하는 진단 설정을 만듭니다.

[Log Analytics를](../log-query/get-started-portal.md) 사용하면 Azure Monitor의 강력한 기능인 [로그 쿼리를](../log-query/log-query-overview.md)사용하여 작업할 수 있으며, 이를 통해 완전한 기능을 갖춘 쿼리 언어를 사용하여 로그 데이터의 고급 분석을 수행할 수 있습니다. Azure 리소스에 대한 **모니터링** 메뉴의 **로그에서 로그** 분석을 열어 리소스를 [쿼리 범위로](../log-query/scope.md#query-scope)사용하여 로그 쿼리로 작업합니다. 이렇게 하면 해당 리소스에 대해 여러 테이블에서 데이터를 분석할 수 있습니다. Azure Monitor 메뉴의 **로그를** 사용하여 모든 리소스에 대한 로그에 액세스합니다. 

- 로그 쿼리를 작성하는 데 사용되는 쿼리 언어를 사용하는 방법에 대한 자습서는 [Azure Monitor의 로그 쿼리로 시작하기를](../log-query/get-started-queries.md) 참조하십시오.
- Azure 모니터 로그에서 리소스 로그가 수집되는 방법에 대한 정보와 쿼리에서 리소스 로그에 액세스하는 방법에 대한 자세한 내용은 [Azure 모니터의 로그 분석 작업 영역에서 Azure 리소스 로그 수집을](../platform/resource-logs-collect-workspace.md) 참조하십시오.
- Azure 모니터 로그에서 리소스 로그 데이터가 구조화되는 방법에 대한 설명은 [수집 모드를](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) 참조하십시오.
- Azure 모니터 로그의 테이블에 대한 자세한 내용은 각 Azure 서비스에 대한 설명서를 참조하십시오.

![로그](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>명령줄에서 모니터링
명령줄에서 리소스에서 수집된 모니터링 데이터에 액세스하거나 Azure [PowerShell](/powershell/azure/) 또는 [Azure 명령줄 인터페이스를](/cli/azure/)사용하여 스크립트에 포함할 수 있습니다. 

- CLI에서 메트릭 데이터에 액세스하기 위한 [CLI 메트릭 참조를](/cli/azure/monitor/metrics) 참조하십시오.
- CLI의 로그 쿼리를 사용하여 Azure 모니터 로그 데이터에 액세스하기 위한 CLI [로그 분석 참조를](/cli/azure/ext/log-analytics/monitor/log-analytics) 참조하십시오.
- Azure PowerShell에서 메트릭 데이터에 액세스하기 위한 [Azure PowerShell 메트릭 참조를](/powershell/module/azurerm.insights/get-azurermmetric) 참조하십시오.
- [Azure PowerShell의 로그 쿼리를](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) 사용하여 Azure 모니터 로그 데이터에 액세스하기 위한 Azure PowerShell 로그 쿼리 참조를 참조하십시오.

## <a name="monitoring-from-rest-api"></a>REST API에서 모니터링
REST API를 사용하여 사용자 지정 응용 프로그램에 리소스에서 수집된 모니터링 데이터를 포함합니다.

- Azure 모니터 REST API에서 메트릭에 액세스하는 방법에 대한 자세한 내용은 [Azure 모니터링 REST API 연습을](../platform/rest-api-walkthrough.md) 참조하십시오.
- Azure PowerShell의 로그 쿼리를 사용하여 Azure 모니터 로그 데이터에 액세스하는 방법에 대한 자세한 내용은 Azure [Log Analytics REST API를](https://dev.loganalytics.io/) 참조하십시오.

## <a name="alerts"></a>경고
[경고는](../platform/alerts-overview.md) 모니터링 데이터에 중요한 조건이 발견되면 사전에 사용자에게 알리고 조치를 취할 수 있습니다. 경고대상, 경고를 만들지 여부에 대한 조건 및 응답에서 수행할 작업을 정의하는 경고 규칙을 만듭니다.

모니터링 데이터의 다른 종류는 경고 규칙의 다른 종류에 사용 됩니다.

- [활동 로그 경고](../platform/alerts-activity-log.md) - 특정 조건과 일치하는 활동 로그에서 항목이 생성될 때 경고를 만듭니다. 예를 들어 특정 유형의 리소스가 만들어지거나 구성 변경이 실패할 때 알림을 받을 수 있습니다.
- [메트릭 경고](../platform/alerts-metric.md) - 메트릭 값이 특정 임계값을 초과할 때 경고를 만듭니다. 메트릭 경고는 다른 경고보다 응답성이 높으며 문제가 해결되면 자동으로 해결할 수 있습니다.
- [로그 쿼리 경고](../platform/alerts-log.md) - 정기적으로 로그 쿼리를 실행하고 특정 조건이 발견되면 경고를 만듭니다. 이렇게 하면 여러 데이터 집합 및 에서 복잡한 분석을 수행할 수 있습니다.

리소스 메뉴의 **경고를** 사용하여 경고를 보고 해당 리소스에 대한 경고 규칙을 관리합니다. 활동 로그 경고 및 메트릭 경고만 개별 Azure 리소스를 대상으로 사용합니다. 로그 쿼리 경고는 Log Analytics 작업 영역을 대상으로 사용하며 해당 작업 영역에 저장된 모든 로그에 액세스할 수 있는 쿼리를 기반으로 합니다. Azure Monitor 메뉴를 사용하여 모든 리소스및 로그 쿼리 경고 규칙에 대한 경고를 보고 관리합니다.

- 경고 규칙 만들기에 대한 자세한 내용은 위의 다양한 종류의 경고에 대한 문서를 참조하십시오.
- 경고에 대한 응답을 관리할 수 있는 작업 그룹을 만드는 방법에 대한 자세한 내용은 [Azure 포털에서 작업 그룹 만들기 및 관리를](../platform/action-groups.md) 참조하세요.



## <a name="next-steps"></a>다음 단계

* 다른 Azure 서비스에 대한 리소스 로그에 대한 자세한 내용은 [지원되는 서비스, 스키마 및 Azure 리소스 로그에 대한 범주를](../platform/diagnostic-logs-schema.md) 참조하십시오.  
