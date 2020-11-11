---
title: Azure Monitor 로그
description: 모니터링 데이터에 대 한 고급 분석에 사용 되는 Azure Monitor 로그에 대해 설명 합니다.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/22/2020
ms.author: bwren
ms.openlocfilehash: 462242b001da5a5a6d2eba8e4bd06315c0b263a6
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491858"
---
# <a name="azure-monitor-logs-overview"></a>Azure Monitor 로그 개요
Azure Monitor 로그는 모니터링 되는 [리소스](../monitor-reference.md)에서 로그 및 성능 데이터를 수집 하 고 구성 하는 Azure Monitor의 기능입니다. Azure 서비스의 [플랫폼 로그](platform-logs-overview.md) , [virtual machines 에이전트](agents-overview.md)의 로그 및 성능 데이터, [응용 프로그램](../app/app-insights-overview.md) 의 사용 현황 및 성능 데이터 등 다양 한 원본에서 데이터를 단일 작업 영역으로 통합 하 여 수백만 개의 레코드를 신속 하 게 분석할 수 있는 정교한 쿼리 언어를 사용 하 여 함께 분석할 수 있습니다. 특정 레코드 집합을 검색 하거나 정교한 데이터 분석을 수행 하 여 모니터링 데이터에서 중요 한 패턴을 식별 하는 간단한 쿼리를 수행할 수 있습니다. Log Analytics를 사용 하 여 로그 쿼리 및 해당 결과를 대화형으로 작업 하 고, 경고 규칙에서 사용 하 여 사전에 문제에 대 한 알림을 수행 하거나, 통합 문서 또는 대시보드에서 결과를 시각화할 수 있습니다.

> [!NOTE]
> Azure Monitor 로그는 Azure Monitor를 지 원하는 데이터 플랫폼 중 절반입니다. 다른는 시계열 데이터베이스에 숫자 데이터를 저장 하는 [Azure Monitor 메트릭입니다](data-platform-metrics.md) . 이렇게 하면이 데이터를 Azure Monitor 로그의 데이터 보다 더 간단 하 게 만들고, 거의 실시간 시나리오를 지원 하 여 문제를 경고 하 고 신속 하 게 검색 하는 데 유용 하 게 사용할 수 있습니다. 그러나 메트릭은 특정 구조에 숫자 데이터만 저장할 수 있는 반면, 로그는 고유 구조를 사용 하 여 다양 한 데이터 형식을 저장할 수 있습니다. 또한 메트릭 데이터의 분석에 사용할 수 없는 로그 쿼리를 사용 하 여 로그 데이터에 대 한 복잡 한 분석을 수행할 수 있습니다.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Azure Monitor 로그로 무엇을 할 수 있나요?
다음 표에서는 Azure Monitor에서 로그를 사용할 수 있는 여러 가지 방법에 대해 설명 합니다.

|  |  |
|:---|:---|
| **분석** | Azure Portal에서 [Log Analytics](../log-query/get-started-portal.md) 를 사용 하 여 [로그 쿼리](../log-query/log-query-overview.md) 를 작성 하 고 강력한 분석 엔진을 사용 하 여 로그 데이터를 대화형으로 분석 합니다. |
| **경고** | 쿼리의 결과가 특정 결과와 일치할 때 알림을 보내거나 [자동화된 작업](action-groups.md)을 수행하는 [로그 경고 규칙](alerts-log.md)을 구성합니다. |
| **시각화** | 테이블 또는 차트로 렌더링된 쿼리 결과를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정합니다.<br>[통합 문서](../app/usage-workbooks.md)를 만들어 대화형 보고서의 여러 데이터 집합을 결합합니다. <br>쿼리의 결과를 [Power BI](powerbi.md)로 내보내 서로 다른 시각화를 사용하고 Azure 외부의 사용자와 공유합니다.<br>쿼리의 결과를 [Grafana](grafana-plugin.md)로 내보내 대시보드를 활용하고 다른 데이터 원본과 결합합니다.|
| **Insights** | 특정 응용 프로그램 및 서비스에 대 한 사용자 지정 모니터링 환경을 제공 하는 [통찰력](../monitor-reference.md#insights-and-core-solutions) 을 지원 합니다.  |
| **장치** | [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)를 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.operationalinsights)을 사용하여 명령줄에서 로그 쿼리 결과에 액세스합니다.<br>[REST API](https://dev.loganalytics.io/)를 사용하여 사용자 지정 애플리케이션에서 로그 쿼리 결과에 액세스합니다. |
| **내보내기** | Azure storage 계정 또는 Azure Event Hubs에 대 한 [로그 데이터의 자동화 된 내보내기를](logs-data-export.md) 구성 합니다.<br>워크플로를 빌드하여 로그 데이터를 검색하고 [Logic Apps](logicapp-flow-connector.md)를 사용하여 이를 외부 위치에 복사합니다. |

![로그 개요](media/data-platform-logs/logs-overview.png)


## <a name="data-collection"></a>데이터 수집
Log Analytics 작업 영역을 만든 후에는 다른 원본을 구성 하 여 데이터를 전송 해야 합니다. 데이터는 자동으로 수집 되지 않습니다. 이 구성은 데이터 원본에 따라 달라 집니다. 예를 들어 Azure 리소스에서 작업 영역으로 리소스 로그를 전송 하는 [진단 설정을 만듭니다](diagnostic-settings.md) . [VM용 Azure Monitor 사용 하도록 설정](../insights/vminsights-enable-overview.md) 하 여 가상 컴퓨터에서 데이터를 수집 합니다. [작업 영역에서 데이터 원본을](data-sources.md) 구성 하 여 추가 이벤트 및 성능 데이터를 수집 합니다.

- Azure Monitor 로그에 데이터를 보내도록 구성할 수 있는 데이터 원본의 전체 목록은 [Azure Monitor에서 모니터링 되는 내용](../monitor-reference.md) 을 참조 하세요.


## <a name="log-analytics-workspaces"></a>Log Analytics 작업 영역
Azure Monitor 로그에 의해 수집 된 데이터는 하나 이상의 [Log Analytics 작업 영역](./design-logs-deployment.md)에 저장 됩니다. 작업 영역은 데이터의 지리적 위치, 데이터에 액세스할 수 있는 사용자를 정의 하는 액세스 권한, 가격 책정 계층 및 데이터 보존 등의 구성 설정을 정의 합니다.  

Azure Monitor 로그를 사용 하려면 작업 영역을 하나 이상 만들어야 합니다. 단일 작업 영역이 모든 모니터링 데이터에 대해 충분 하거나 요구 사항에 따라 여러 작업 영역을 만들도록 선택할 수 있습니다. 예를 들어 프로덕션 데이터에 대해 하나의 작업 영역이 있고 테스트에 다른 작업 영역이 있을 수 있습니다. 

- 새 작업 영역을 만들려면 [Azure Portal에서 Log Analytics 작업 영역 만들기](../learn/quick-create-workspace.md) 를 참조 하세요.
- 여러 작업 영역을 만들기 위한 고려 사항은 [Azure Monitor 로그 배포 디자인](design-logs-deployment.md) 을 참조 하세요.

## <a name="data-structure"></a>데이터 구조
로그 쿼리는 Log Analytics 작업 영역에서 데이터를 검색 합니다. 각 작업 영역에는 여러 행의 데이터를 포함 하는 별도의 열로 구성 된 여러 테이블이 포함 되어 있습니다. 각 테이블은 데이터 원본에서 제공 하는 데이터의 행에서 공유 하는 고유한 열 집합으로 정의 됩니다. 

[![Azure Monitor 로그 구조](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Application Insights의 로그 데이터는 Azure Monitor 로그에도 저장 되지만 응용 프로그램이 구성 된 방식에 따라 다르게 저장 됩니다. 작업 영역 기반 응용 프로그램의 경우 데이터는 응용 프로그램 요청, 예외 및 페이지 보기와 같은 데이터를 저장 하는 표준 테이블 집합의 Log Analytics 작업 영역에 저장 됩니다. 여러 응용 프로그램에서 동일한 작업 영역을 사용할 수 있습니다. 클래식 응용 프로그램의 경우 데이터는 Log Analytics 작업 영역에 저장 되지 않습니다. 동일한 쿼리 언어를 사용 하 고 Azure Portal에서 동일한 Log Analytics 도구를 사용 하 여 쿼리를 만들고 실행 합니다. 그러나 클래식 응용 프로그램의 데이터는 서로 별도로 저장 됩니다. 테이블 이름과 열 이름이 서로 다르지만 일반적인 구조는 작업 영역 기반 응용 프로그램과 동일 합니다. 작업 영역 기반 및 클래식 응용 프로그램의 스키마에 대 한 자세한 내용은 [작업 영역 기반 리소스 변경 내용](../app/apm-tables.md) 을 참조 하세요.


> [!NOTE]
> Application Insights 환경 내에서 Application Insights 클래식 리소스 쿼리, 통합 문서 및 로그 기반 경고에 대해 계속 이전 버전과 완전히 호환됩니다. [새 작업 영역 기반 테이블 구조/스키마](../app/apm-tables.md)를 쿼리/확인하려면 먼저 Log Analytics 작업 영역으로 이동해야 합니다. 미리 보기 중에 Application Insights 창 내에서 **로그** 를 선택하면 클래식 Application Insights 쿼리 환경에 액세스할 수 있습니다. 자세한 내용은 [쿼리 범위](../log-query/scope.md) 를 참조 하세요.


[![Application Insights에 대 한 Azure Monitor 로그 구조](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)


## <a name="log-queries"></a>로그 쿼리
데이터를 처리 하 고 결과를 반환 하는 읽기 전용 요청인 로그 쿼리를 사용 하 여 Log Analytics 작업 영역에서 데이터를 검색 합니다. 로그 쿼리는 Azure 데이터 탐색기에서 사용 하는 것과 동일한 쿼리 언어인 [KQL (Kusto Query language)](/azure/data-explorer/kusto/query/)로 작성 됩니다. Log Analytics에서 로그 쿼리를 작성 하 여 결과를 대화형으로 분석 하 고, 경고 규칙에서 사용 하 여 문제를 사전에 통보 하거나, 해당 결과를 통합 문서 또는 대시보드에 포함할 수 있습니다. Insights에는 해당 보기와 통합 문서를 지 원하는 미리 작성 한 쿼리가 포함 되어 있습니다.

- 로그 쿼리를 사용 하는 목록과 자습서 및 기타 설명서에 대 한 참조를 시작 하려면 [Azure Monitor의 로그 쿼리](log-query/../../log-query/log-query-overview.md) 를 참조 하세요.

![Log Analytics](media/data-platform-logs/log-analytics.png)

## <a name="log-analytics"></a>Log Analytics
Azure Portal의 도구인 Log Analytics를 사용 하 여 로그 쿼리를 편집 및 실행 하 고 결과를 대화형으로 분석 합니다. 그런 다음 사용자가 만든 쿼리를 사용 하 여 쿼리 경고 및 통합 문서 로그와 같은 Azure Monitor의 다른 기능을 지원할 수 있습니다. Azure Monitor 메뉴의 **로그** 옵션 또는 Azure Portal의 대부분의 다른 서비스에서 Log Analytics에 액세스할 수 있습니다.

- Log Analytics에 대 한 설명은 [Azure Monitor의 Log Analytics 개요](/log-query/log-analytics-overview.md) 를 참조 하세요. 
- [Log Analytics 자습서](/log-query/log-analytics-tutorial.md) 를 참조 하 여 간단한 로그 쿼리를 만들고 해당 결과를 분석 하는 Log Analytics 기능을 사용 하는 과정을 안내 합니다.



## <a name="relationship-to-azure-data-explorer"></a>Azure 데이터 탐색기에 대 한 관계
Azure Monitor 로그는 Azure 데이터 탐색기를 기반으로 합니다. Log Analytics 작업 영역은 대략적으로 Azure 데이터 탐색기의 데이터베이스와 동일 하며, 테이블은 동일 하 게 구성 되며 둘 다 동일한 Kusto 쿼리 언어 (KQL)를 사용 합니다. Log Analytics를 사용 하 여 Azure Portal의 Azure Monitor 쿼리로 작업 하는 환경은 Azure 데이터 탐색기 웹 UI를 사용 하는 환경과 비슷합니다. [Log Analytics 작업 영역의 데이터를 Azure 데이터 탐색기 쿼리에 포함할](/azure/data-explorer/query-monitor-data)수도 있습니다. 


## <a name="next-steps"></a>다음 단계

- Log Analytics 작업 영역에서 데이터를 검색 하 고 분석 하는 [로그 쿼리에](../log-query/log-query-overview.md) 대해 알아봅니다.
- [Azure Monitor의 메트릭](data-platform-metrics.md)을 알아봅니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
