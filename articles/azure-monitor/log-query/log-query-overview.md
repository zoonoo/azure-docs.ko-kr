---
title: Azure Monitor의 로그 쿼리
description: Azure Monitor에서 사용되는 Kusto 쿼리 언어에 대한 참조 정보입니다. Azure Monitor와 관련된 추가 요소와 Azure Monitor 로그 쿼리에 지원되지 않는 요소가 포함됩니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/09/2020
ms.openlocfilehash: 6174bcbe5a014cff8dbd8dff242880d7f0ef7aa0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491395"
---
# <a name="log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리
Azure Monitor 로그는 Azure 데이터 탐색기를 기반으로 하며 로그 쿼리는 동일한 Kusto 쿼리 언어 (KQL)를 사용 하 여 작성 됩니다. 이는 쉽게 읽고 제작할 수 있도록 설계 된 풍부한 언어 이므로 몇 가지 기본 지침을 사용 하 여 쿼리 작성을 시작할 수 있어야 합니다.

쿼리를 사용 하는 Azure Monitor 영역은 다음과 같습니다.

- [Log Analytics](../log-query/log-analytics-overview.md). 로그 쿼리를 편집 하 고 대화형으로 결과를 분석 하기 위한 Azure Portal의 기본 도구입니다. Azure Monitor의 다른 곳에서 로그 쿼리를 사용 하려는 경우에도 일반적으로 Log Analytics를 최종 위치로 복사 하기 전에이를 작성 하 고 테스트 합니다.
- [로그 경고 규칙](../platform/alerts-overview.md)입니다. 작업 영역에서 데이터의 문제를 사전에 식별 합니다.  각 경고 규칙은 일정 한 간격으로 자동 실행 되는 로그 쿼리를 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- [통합 문서](../platform/workbooks-overview.md). Azure Portal의 대화형 시각적 보고서에 다양 한 시각화를 사용 하 여 로그 쿼리의 결과를 포함 합니다.
- [Azure 대시보드](../learn/tutorial-logs-dashboards.md). 모든 쿼리 결과를 Azure 대시보드에 고정 하 여 로그 및 메트릭 데이터를 함께 시각화 하 고 필요에 따라 다른 Azure 사용자와 공유할 수 있습니다.
- [Logic Apps](../platform/logicapp-flow-connector.md).  Logic Apps를 사용 하 여 자동화 된 워크플로에서 로그 쿼리 결과를 사용 합니다.
- [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult). 명령줄의 PowerShell 스크립트 또는 AzOperationalInsightsSearchResults를 사용 하는 Azure Automation runbook의 로그 쿼리 결과를 사용 합니다.
- [Azure Monitor LOGS API](https://dev.loganalytics.io). REST API 클라이언트에서 작업 영역에서 로그 데이터를 검색 합니다.  API 요청에는 검색할 데이터를 확인하기 위해 Azure Monitor에 대해 실행되는 쿼리가 포함됩니다.

## <a name="getting-started"></a>시작
KQL를 사용 하 여 로그 쿼리를 작성 하는 방법을 배우는 가장 좋은 방법은 사용 가능한 자습서와 샘플을 활용 하는 것입니다.

- [Log Analytics 자습서](log-analytics-tutorial.md) -Azure Portal에서 쿼리를 편집 및 실행 하는 데 사용 하는 도구인 Log Analytics 기능 사용에 대 한 자습서입니다. 또한 쿼리 언어를 직접 사용 하지 않고도 간단한 쿼리를 작성할 수 있습니다. 이전에 Log Analytics를 사용 하지 않은 경우 여기에서 시작 하 여 다른 자습서 및 샘플에서 사용할 도구를 이해할 수 있습니다.
- [KQL 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor) -기본 KQL 개념 및 일반 연산자를 통해 안내 합니다. 이는 언어 자체와 로그 쿼리 구조를 최대한 활용 하기 시작 하는 가장 좋은 장소입니다. 
- [예제 쿼리](example-queries.md) -Log Analytics에서 사용할 수 있는 쿼리 예제에 대 한 설명입니다. 쿼리를 수정 하지 않고 사용 하거나 샘플로 사용 하 여 KQL를 배울 수 있습니다.
- [쿼리 샘플](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor) -다양 한 개념을 보여 주는 샘플 쿼리입니다.



## <a name="reference-documentation"></a>참조 설명서
모든 명령 및 연산자에 대 한 참조를 포함 한 [KQL 설명서](/azure/data-explorer/kusto/query/) 는 Azure 데이터 탐색기 설명서에서 확인할 수 있습니다. KQL를 사용 하 여 능숙 하 게 사용 하는 경우에도 정기적으로 참조를 사용 하 여 이전에 사용 하지 않은 새 명령 및 시나리오를 조사 합니다.


## <a name="language-differences"></a>언어 차이
Azure Monitor Azure 데이터 탐색기와 동일한 KQL를 사용 하지만 몇 가지 차이점이 있습니다. KQL 설명서는 Azure Monitor에서 지원 되지 않거나 다른 기능을 포함 하는 연산자를 지정 합니다. Azure Monitor 관련 된 연산자는 Azure Monitor 내용에 설명 되어 있습니다. 다음 섹션에서는 빠른 참조를 위해 언어 버전 간의 차이점을 나열 합니다.

### <a name="statements-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 명령문

* [별칭](/azure/kusto/query/aliasstatement)
* [쿼리 매개 변수](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 함수

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [데이터베이스 ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 연산자

* [클러스터 간 조인](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Azure Monitor에서 지원되지 않는 플러그 인

* [Python 플러그 인](/azure/kusto/query/pythonplugin)
* [sql_request 플러그 인](/azure/kusto/query/sqlrequestplugin)


### <a name="additional-operators-in-azure-monitor"></a>Azure Monitor의 추가 연산자
다음 연산자는 특정 Azure Monitor 기능을 지원하며 Azure Monitor 외에는 사용할 수 없습니다.

* [앱 ()](app-expression.md)
* [리소스 ()](resource-expression.md)
* [작업 영역 ()](workspace-expression.md)

## <a name="next-steps"></a>다음 단계
- [쿼리 작성에 대 한 자습서](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)를 안내 합니다.
- [Kusto 쿼리 언어에 대한 전체 참조 문서](/azure/kusto/query/)에 액세스합니다.

