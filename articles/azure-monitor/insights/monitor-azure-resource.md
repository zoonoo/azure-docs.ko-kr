---
title: Azure Monitor를 사용 하 여 Azure 리소스 모니터링 | Microsoft Docs
description: Azure Monitor를 사용 하 여 Azure에서 리소스의 모니터링 데이터를 수집 하 고 분석 하는 방법을 설명 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 1ec241e261a7710b7a5b92d88f147ce8d148602b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554033"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure 리소스 모니터링
Azure 리소스를 사용 하는 중요 한 응용 프로그램 및 비즈니스 프로세스를 사용 하는 경우 해당 리소스의 가용성, 성능 및 작업을 모니터링 하려고 합니다. 이 문서에서는 Azure 리소스에서 생성 되는 모니터링 데이터와 Azure Monitor 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법에 대해 설명 합니다.

> [!IMPORTANT]
> 이 문서는 Azure Monitor를 사용 하는 Azure의 모든 서비스에 적용 됩니다. Vm 및 App Service을 포함 한 계산 리소스는 여기에 설명 된 것과 동일한 모니터링 데이터를 생성 하지만 로그 및 메트릭을 생성할 수 있는 게스트 운영 체제도 있습니다. 이 데이터를 수집 하 고 분석 하는 방법에 대 한 자세한 내용은 이러한 서비스에 대 한 모니터링 설명서를 참조 하세요.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Monitor은 azure에서 다른 클라우드 및 온-프레미스의 리소스 외에도 Azure 리소스를 모니터링 하는 완전 한 기능 집합을 제공 하는 전체 stack 모니터링 서비스입니다. [Azure Monitor 데이터 플랫폼](../platform/data-platform.md) 은 다음 섹션에 설명 된 대로 전체 모니터링 도구 집합을 사용 하 여 함께 분석할 수 있는 [로그](../platform/data-platform-logs.md) 및 [메트릭](../platform/data-platform-metrics.md) 으로 데이터를 수집 합니다.

- [Azure Monitor 메트릭을 사용 하 여 무엇을 할 수 있나요?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Azure Monitor 로그로 무엇을 할 수 있나요?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Azure 리소스를 만드는 즉시 Azure Monitor 사용 하도록 설정 되 고 [Azure Portal에서 보고 분석할](#monitoring-in-the-azure-portal)수 있는 메트릭 및 활동 로그를 수집 하기 시작 합니다. 일부 구성에서는 추가 모니터링 데이터를 수집 하 고 추가 기능을 사용 하도록 설정할 수 있습니다. 구성 요구 사항에 대 한 자세한 내용은 아래의 [모니터링 데이터](#monitoring-data) 를 참조 하세요.


## <a name="costs-associated-with-monitoring"></a>모니터링과 관련 된 비용
기본적으로 수집 되는 모니터링 데이터의 분석 비용은 없습니다. 이 제품에는 다음이 포함됩니다.

- 메트릭 탐색기를 사용 하 여 플랫폼 메트릭을 수집 하 고 분석 합니다.
- 활동 로그를 수집 하 고 Azure Portal 분석 합니다.
- 활동 로그 경고 규칙을 만드는 중입니다.

로그 및 메트릭을 수집 하 고 내보내는 데 Azure Monitor 비용은 없지만 대상과 관련 된 비용이 있을 수 있습니다.

- Log Analytics 작업 영역에서 로그 및 메트릭을 수집할 때 데이터 수집 및 보존과 관련 된 비용입니다. [Log Analytics에 대 한 Azure Monitor 가격 책정을](https://azure.microsoft.com/pricing/details/monitor/)참조 하세요.
- Azure 저장소 계정에 로그 및 메트릭을 수집 하는 경우 데이터 저장소와 관련 된 비용입니다. [Blob 저장소에 대 한 Azure Storage 가격 책정을](https://azure.microsoft.com/pricing/details/storage/blobs/)참조 하세요.
- Azure Event Hubs로 로그 및 메트릭을 전달할 때 이벤트 허브 스트림과 관련 된 비용입니다. [Azure Event Hubs 가격 책정](https://azure.microsoft.com/pricing/details/event-hubs/)을 참조 하세요.

다음과 관련 된 Azure Monitor 비용이 있을 수 있습니다. [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/)을 참조 하세요.

- 로그 쿼리를 실행 하 고 있습니다.
- 메트릭 또는 로그 쿼리 경고 규칙을 만드는 중입니다.
- 경고 규칙에서 알림 보내기
- API를 통해 메트릭 액세스

## <a name="monitoring-data"></a>데이터 모니터링
Azure의 리소스는 다음 다이어그램에 표시 된 [로그](../platform/data-platform-logs.md) 및 [메트릭을](../platform/data-platform-metrics.md) 생성 합니다. 생성 하는 특정 데이터와 사용자가 제공 하는 추가 솔루션 또는 정보는 각 Azure 서비스에 대 한 설명서를 참조 하세요.

![개요](media/monitor-azure-resource/logs-metrics.png)



- [플랫폼 메트릭](../platform/data-platform-metrics.md) -정기적으로 자동으로 수집 되는 숫자 값으로, 특정 시간에 리소스의 일부 측면을 설명 합니다. 
- [리소스 로그](../platform/resource-logs-overview.md) -Azure 리소스 (데이터 평면) 내에서 수행 된 작업에 대 한 정보를 제공 합니다. 예를 들어 Key Vault에서 비밀을 가져오거나 데이터베이스를 요청 하는 등의 작업을 수행할 수 있습니다. 리소스 로그의 내용과 구조는 Azure 서비스 및 리소스 유형에 따라 달라 집니다.
- [활동 로그](../platform/activity-logs-overview.md) -외부 (관리 평면)에서 구독의 각 Azure 리소스에 대 한 작업에 대 한 정보를 제공 합니다. 예를 들어 새 리소스를 만들거나 가상 머신을 시작 합니다. 구독에서 리소스에 대해 수행 되는 모든 쓰기 작업 (PUT, POST, DELETE)에 대 한 정보입니다.


## <a name="configuration-requirements"></a>구성 요구 사항
일부 모니터링 데이터는 자동으로 수집 되지만 요구 사항에 따라 일부 구성을 수행 해야 할 수도 있습니다. 각 유형의 모니터링 데이터에 대 한 구체적인 정보는 아래 정보를 참조 하세요.

- [플랫폼 메트릭](../platform/data-platform-metrics.md) -플랫폼 메트릭은 구성이 필요 없는 [Azure Monitor 메트릭에](../platform/data-platform-metrics.md) 자동으로 수집 됩니다. Azure Monitor 로그에 항목을 보내거나 Azure 외부에서 전달 하는 진단 설정을 만듭니다.
- [리소스 로그](../platform/resource-logs-overview.md) -리소스 로그는 Azure 리소스에 의해 자동으로 생성 되지만 진단 설정 없이는 수집 되지 않습니다.  Azure Monitor 로그에 항목을 보내거나 Azure 외부에서 전달 하는 진단 설정을 만듭니다.
- [활동 로그](../platform/activity-logs-overview.md) -작업 로그는 구성이 필요 없는 자동으로 수집 되며 Azure Portal에서 볼 수 있습니다. 진단 설정을 만들어 Azure Monitor 로그에 복사 하거나 Azure 외부에서 전달 합니다.


## <a name="diagnostic-settings"></a>진단 설정
진단 설정은 특정 리소스에 대 한 리소스 로그 및 메트릭을 보내야 하는 위치를 정의 합니다. 가능한 대상은 다음과 같습니다.

- [Log Analytics 작업 영역](../platform/resource-logs-collect-workspace.md) 을 사용 하면 강력한 로그 쿼리를 사용 하 여 Azure Monitor 수집 된 다른 모니터링 데이터를 사용 하 여 데이터를 분석 하 고 로그 경고 및 시각화와 같은 기타 Azure Monitor 기능을 활용할 수 있습니다. 
- 타사 SIEMs 및 기타 log analytics 솔루션과 같은 외부 시스템으로 데이터를 스트리밍하는 [Event hubs](../platform/resource-logs-stream-event-hubs.md) 
- 감사, 정적 분석 또는 백업에 유용 하 게 사용할 수 있는 [Azure storage 계정](../platform/resource-logs-collect-storage.md) 입니다.

진단 설정 만들기의 절차에 따라 [Azure에서 플랫폼 로그 및 메트릭을 수집 하](../platform/diagnostic-settings.md) 여 Azure Portal를 통해 진단 설정을 만들고 관리 합니다. 템플릿에서 정의 하려면 [리소스 관리자 템플릿을 사용 하 여 Azure에서 진단 설정 만들기](../platform/diagnostic-settings-template.md) 를 참조 하 고, 리소스를 만들 때 리소스에 대 한 완전 한 모니터링을 사용 하도록 설정 합니다.


## <a name="monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링
 Azure Portal의 리소스 메뉴에서 대부분의 Azure 리소스에 대 한 모니터링 데이터에 액세스할 수 있습니다. 이를 통해 표준 Azure Monitor 도구를 사용 하 여 단일 리소스의 데이터에 액세스할 수 있습니다. 일부 Azure 서비스는 다른 옵션을 제공 하므로 추가 정보는 해당 서비스에 대 한 설명서를 참조 해야 합니다. **Azure Monitor** 메뉴를 사용 하 여 모니터링 되는 모든 리소스의 데이터를 분석할 수 있습니다. 

대부분의 서비스에는 **개요** 페이지에 대 한 모니터링 데이터가 포함 되어 해당 작업에 대 한 간략 한 보기를 제공 합니다. 이는 일반적으로 Azure Monitor 메트릭에 저장 된 플랫폼 메트릭의 하위 집합을 기반으로 합니다. 다른 모니터링 옵션은 일반적으로 서비스의 **모니터링** 섹션에서 사용할 수 있습니다. 메뉴가.

![개요 페이지](media/monitor-azure-resource/overview-page.png)

## <a name="analyzing-metrics"></a>메트릭 분석
[메트릭 탐색기](../platform/metrics-getting-started.md)를 사용 하 여 개별 메트릭을 분석 하 고 여러 메트릭을 상호 연결 하 여 상관 관계 및 추세를 식별 합니다. 일부 서비스는 리소스 메뉴에서 **메트릭을** 열 때 해당 메트릭에 대 한 작업을 수행 하기 위한 사용자 지정 된 환경을 제공 합니다.

- 메트릭 탐색기 사용에 대 한 기본 사항은 [Azure 메트릭 탐색기 시작](../platform/metrics-getting-started.md) 을 참조 하세요.
- 여러 메트릭 사용, 필터 및 분할 적용과 같은 메트릭 탐색기의 고급 기능에 대 한 [Azure 메트릭 탐색기의 고급 기능](../platform/metrics-charts.md) 을 참조 하세요.

![메트릭](media/monitor-azure-resource/metrics.png)


## <a name="analyzing-logs"></a>로그 분석

### <a name="activity-log"></a>활동 로그 
현재 리소스로 설정 된 초기 필터를 사용 하 여 Azure Portal의 활동 로그에서 항목을 봅니다. 활동 로그를 Log Analytics 작업 영역에 복사 하 여 로그 쿼리 및 통합 문서에서 사용할 수 있도록 합니다. 

- 활동 로그를 보고 다양 한 방법을 사용 하 여 항목을 검색 하는 방법에 대 한 자세한 내용은 [Azure 활동 로그 이벤트 보기 및 검색](../platform/activity-log-view.md) 을 참조 하세요.
- 기록 되는 특정 이벤트는 Azure 서비스에 대 한 설명서를 참조 하세요.

![활동 로그](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor 로그
Azure Monitor 로그는 강력한 쿼리 도구를 사용 하 여 분석을 위해 여러 서비스 및 기타 데이터 원본의 로그와 메트릭을 통합 합니다. 위에서 설명한 것 처럼 Azure Monitor의 Log Analytics 작업 영역으로 플랫폼 메트릭, 활동 로그 및 리소스 로그를 수집 하는 진단 설정을 만듭니다.

[Log Analytics](../log-query/get-started-portal.md) 를 사용 하면 완전 한 기능을 갖춘 쿼리 언어를 사용 하 여 로그 데이터에 대 한 고급 분석을 수행할 수 있는 강력한 Azure Monitor 기능인 [로그 쿼리](../log-query/log-query-overview.md)를 사용할 수 있습니다. 리소스를 [쿼리 범위로](../log-query/scope.md#query-scope)사용 하 여 로그 쿼리로 작업 하려면 Azure 리소스에 대 한 **모니터링** 메뉴의 **로그** 에서 Log Analytics를 엽니다. 이를 통해 해당 리소스에 대 한 여러 테이블의 데이터를 분석할 수 있습니다. Azure Monitor 메뉴의 **로그** 를 사용 하 여 모든 리소스에 대 한 로그에 액세스할 수 있습니다. 

- Log Analytics를 사용 하 여 쿼리를 작성 하 고 결과에 대 한 작업을 수행 하는 연습은 [Azure Monitor에서 Log Analytics 시작](../log-query/get-started-portal.md) 을 참조 하세요.
- 로그 쿼리를 작성 하는 데 사용 되는 쿼리 언어를 사용 하는 방법에 대 한 자습서는 [Azure Monitor에서 로그 쿼리 시작](../log-query/get-started-queries.md) 을 참조 하세요.
- Azure Monitor 로그에서 리소스 로그를 수집 하는 방법에 대 한 자세한 내용과 쿼리에서 액세스 하는 방법에 대 한 자세한 내용은 [Azure Monitor Log Analytics 작업 영역에서 Azure 리소스 로그 수집](../platform/resource-logs-collect-workspace.md) 을 참조 하세요.
- Azure Monitor 로그에서 리소스 로그 데이터를 구성 하는 방법에 대 한 설명은 [수집 모드](../platform/resource-logs-collect-workspace.md#collection-mode) 를 참조 하세요.
- Azure Monitor 로그의 해당 테이블에 대 한 자세한 내용은 각 Azure 서비스에 대 한 설명서를 참조 하세요.

![로그](media/monitor-azure-resource/logs.png)


## <a name="alerts"></a>경고
[경고](../platform/alerts-overview.md) 는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알리고 잠재적으로 조치를 취합니다. 경고의 대상, 경고를 만들지 여부에 대 한 조건 및 응답으로 수행할 작업을 정의 하는 경고 규칙을 만듭니다.

다양 한 종류의 모니터링 데이터가 다양 한 종류의 경고 규칙에 사용 됩니다.

- [활동 로그 경고](../platform/alerts-activity-log.md) -활동 로그에서 특정 조건과 일치 하는 항목이 생성 되 면 경고를 만듭니다. 이렇게 하면 특정 형식의 리소스가 만들어지거나 구성 변경이 실패 한 경우 예를 알릴 수 있습니다.
- [메트릭 경고](../platform/alerts-metric.md) -메트릭 값이 특정 임계값을 초과 하는 경우 경고를 만듭니다. 메트릭 경고는 다른 경고 보다 응답성이 높고 문제가 해결 될 때 자동으로 해결 될 수 있습니다.
- [로그 쿼리 경고](../platform/alerts-log.md) -정기적으로 로그 쿼리를 실행 하 고 특정 조건이 발견 되 면 경고를 만듭니다. 이를 통해 여러 데이터 집합 및에서 복잡 한 분석을 수행할 수 있습니다.

리소스의 메뉴에서 경고를 사용 **하 여 경고** 를 확인 하 고 해당 리소스에 대 한 경고 규칙을 관리 합니다. 활동 로그 경고 및 메트릭 경고만 개별 Azure 리소스를 대상으로 사용 합니다. 로그 쿼리 경고는 Log Analytics 작업 영역을 대상으로 사용 하 고 해당 작업 영역에 저장 된 모든 로그에 액세스할 수 있는 쿼리를 기반으로 합니다. Azure Monitor 메뉴를 사용 하 여 모든 리소스에 대 한 경고를 보고 관리 하 고, log query 경고 규칙을 관리 합니다.

- 경고 규칙을 만드는 방법에 대 한 자세한 내용은 위의 여러 종류의 경고에 대 한 문서를 참조 하세요.
- 경고에 대 한 응답을 관리할 수 있는 작업 그룹을 만드는 방법에 대 한 자세한 내용은 [Azure Portal에서 작업 그룹 만들기 및 관리](../platform/action-groups.md) 를 참조 하세요.

## <a name="insights-and-solutions"></a>통찰력 및 솔루션 
일부 서비스는 Azure Monitor 표준 기능 외의 도구를 제공 합니다. [솔루션](../insights/solutions.md) 은 표준 Azure Monitor 기능을 기반으로 하는 미리 정의 된 모니터링 논리를 제공 합니다. [Insights](../insights/insights-overview.md) 는 Azure Monitor 데이터 플랫폼과 표준 기능을 기반으로 하는 사용자 지정 모니터링 환경을 제공 합니다.

리소스에서 정보를 사용할 수 있는 경우 리소스 메뉴의 **정보** 에서 액세스할 수 있습니다. Azure Monitor 메뉴에서 모든 정보 및 솔루션에 액세스 합니다.

- 사용 가능한 정보 또는 솔루션이 있는지 확인 하려면 각 서비스에 대 한 모니터링 설명서를 참조 하세요.

![인사이트](media/monitor-azure-resource/insights.png)

## <a name="next-steps"></a>다음 단계

* 서로 다른 Azure 서비스에 대 한 리소스 로그에 대 한 자세한 내용은 [Azure 진단 로그에 대해 지원 되는 서비스, 스키마 및 범주](../platform/diagnostic-logs-schema.md) 를 참조 하세요.  
