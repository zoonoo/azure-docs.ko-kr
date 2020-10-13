---
title: Azure Monitor의 로그 쿼리 개요 | Microsoft Docs
description: 로그 쿼리와 관련 된 일반적인 질문에 답변 하 고이를 사용 하 여 시작 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 55463f6af47ef8eda712b1787a89a710c08c1fe6
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91995208"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure Monitor의 로그 쿼리 개요
로그 쿼리를 통해 [Azure Monitor 로그](../platform/data-platform-logs.md)에 수집 된 데이터의 값을 완벽 하 게 활용할 수 있습니다. 강력한 쿼리 언어를 사용 하면 여러 테이블의 데이터를 조인 하 고, 큰 데이터 집합을 집계 하 고, 최소한의 코드로 복잡 한 작업을 수행할 수 있습니다. 실제로 모든 질문에 대 한 답변 및 분석은 지원 데이터가 수집 되는 동안 수행 되며, 올바른 쿼리를 구성 하는 방법을 이해할 수 있습니다.

[정보](../insights/insights-overview.md) 및 [솔루션과](../monitor-reference.md) 같은 Azure Monitor의 일부 기능은 기본 쿼리에 노출 하지 않고 로그 데이터를 처리 합니다. Azure Monitor의 다른 기능을 완벽 하 게 활용 하려면 쿼리를 생성 하는 방법과이를 사용 하 여 Azure Monitor 로그의 데이터를 대화형으로 분석 하는 방법을 이해 해야 합니다.

이 문서를 참조 하 여 Azure Monitor의 로그 쿼리에 대해 학습을 시작 합니다. 일반적인 질문에 답변 하 고 추가 세부 정보 및 단원을 제공 하는 다른 설명서에 대 한 링크를 제공 합니다.

## <a name="how-can-i-learn-how-to-write-queries"></a>쿼리를 작성 하는 방법을 어떻게 알 수 있나요?
바로 이동 하려면 다음 자습서를 시작할 수 있습니다.

- [Azure Monitor에서 Log Analytics를 시작](get-started-portal.md)합니다.
- [Azure Monitor에서 로그 쿼리를 시작](get-started-queries.md)합니다.

기본 사항을 종료 한 후에는에서 시작 하는 데모 환경의 데이터 나 사용자 고유의 데이터를 사용 하 여 여러 단원을 안내 합니다. 

- [Azure Monitor 로그 쿼리에서 문자열 작업](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>로그 쿼리는 어떤 언어를 사용 하나요?
Azure Monitor 로그는 [Azure 데이터 탐색기](/azure/data-explorer)를 기반으로 하며 로그 쿼리는 동일한 Kusto 쿼리 언어 (KQL)를 사용 하 여 작성 됩니다. 이는 쉽게 읽고 제작할 수 있도록 설계 된 풍부한 언어 이며 최소한의 지침으로 사용을 시작할 수 있어야 합니다.

KQL에 대 한 전체 설명서 및 사용 가능한 다양 한 기능에 대 한 참조는 [Azure 데이터 탐색기 KQL 설명서](/azure/kusto/query) 를 참조 하세요.<br>
Azure Monitor 로그의 데이터를 사용 하는 언어에 대 한 간략 한 연습은 [Azure Monitor에서 로그 쿼리 시작](get-started-queries.md) 을 참조 하세요.
Azure Monitor에서 사용 하는 KQL 버전의 사소한 차이점은 [Azure Monitor 로그 쿼리 언어 차이점](data-explorer-difference.md) 을 참조 하세요.

## <a name="what-data-is-available-to-log-queries"></a>로그 쿼리에 사용할 수 있는 데이터는 무엇 인가요?
Azure Monitor 로그에 수집 된 모든 데이터는 로그 쿼리를 검색 하 고 분석 하는 데 사용할 수 있습니다. 데이터 원본이 서로 다른 테이블에 데이터를 기록 하지만 여러 원본에서 데이터를 분석 하는 여러 테이블을 단일 쿼리에 포함할 수 있습니다. 쿼리를 작성하는 경우 원하는 데이터가 있는 테이블이 무엇인지 확인하는 것으로 시작합니다. 데이터의 구성 방법에 대 한 설명은 [Azure Monitor 로그의 구조](../platform/data-platform-logs.md) 를 참조 하세요.

## <a name="what-does-a-log-query-look-like"></a>로그 쿼리의 모양은 무엇입니까?
쿼리는 해당 테이블의 모든 레코드를 검색 하기 위한 단일 테이블 이름 처럼 간단할 수 있습니다.

```Kusto
Syslog
```

또는 특정 레코드를 필터링 하 고, 요약 하 고, 차트에서 결과를 시각화할 수 있습니다.

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

더 복잡 한 분석을 위해 조인을 사용 하 여 여러 테이블에서 데이터를 검색 하 여 결과를 함께 분석할 수 있습니다.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
KQL에 익숙하지 않은 경우에도 이러한 쿼리에서 사용 되는 기본 논리를 최소한으로 파악할 수 있어야 합니다. 테이블 이름으로 시작 하 여 해당 데이터를 필터링 및 처리 하는 여러 명령을 추가 합니다. 쿼리는 원하는 수의 명령을 사용할 수 있으며, 사용 가능한 다양 한 KQL 명령을 익히는 것 처럼 더 복잡 한 쿼리를 작성할 수 있습니다.

언어 및 일반 함수를 소개 하는 로그 쿼리에 대 한 자습서는 [Azure Monitor에서 로그 쿼리 시작](get-started-queries.md) 을 참조 하세요.<br>


## <a name="what-is-log-analytics"></a>Log Analytics란?
Log Analytics는 로그 쿼리를 작성하고 대화형으로 결과를 분석하기 위한 Azure Portal의 기본 도구입니다. 로그 쿼리는 Azure Monitor의 다른 위치에서도 사용되지만, 일반적으로 Log Analytics를 사용하여 쿼리를 작성하고 테스트합니다.

Azure Portal의 여러 위치에서 Log Analytics를 시작할 수 있습니다. Log Analytics에서 사용할 수 있는 데이터의 범위는 시작 방법에 따라 결정 됩니다. 자세한 내용은 [쿼리 범위](scope.md) 를 참조 하세요.

- **Azure Monitor** 메뉴 또는 **Log Analytics 작업 영역** 메뉴에서 **로그** 를 선택 합니다.
- Application Insights 응용 프로그램의 **개요** 페이지에서 **로그** 를 선택 합니다.
- Azure 리소스의 메뉴에서 **로그** 를 선택 합니다.

![Log Analytics](media/log-query-overview/log-analytics.png)

몇 가지 기능을 소개 하는 Log Analytics에 대 한 자습서 연습은 [Azure Monitor에서 Log Analytics 시작](get-started-portal.md) 을 참조 하세요.

## <a name="where-else-are-log-queries-used"></a>로그 쿼리를 사용 하는 다른 위치
Log Analytics에서 로그 쿼리 및 해당 결과를 대화형으로 작업 하는 것 외에도 쿼리를 사용 하는 Azure Monitor 영역에는 다음이 포함 됩니다.

- **경고 규칙.** [경고 규칙](../platform/alerts-overview.md)은 작업 영역에서 데이터의 문제를 사전에 식별합니다.  각 경고 규칙은 일정한 간격으로 자동으로 실행되는 로그 검색을 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- **대시보드** 쿼리 결과를 [Azure 대시보드](../learn/tutorial-logs-dashboards.md)에 고정하여 로그 및 메트릭 데이터를 함께 시각화하고 다른 Azure 사용자와 선택적으로 공유할 수 있습니다.
- **레이아웃.**  [뷰 디자이너](../platform/view-designer.md)를 통해 사용자 대시보드에 포함될 데이터의 시각화를 만들 수 있습니다.  로그 쿼리는 각 뷰의 [타일](../platform/view-designer-tiles.md) 및 [시각화 파트](../platform/view-designer-parts.md)에서 사용되는 데이터를 제공합니다.  
- **내보내기가.**  Azure Monitor에서 Excel 또는 [Power BI](../platform/powerbi.md)로 데이터를 가져오는 경우 로그 쿼리를 만들어 내보낼 데이터를 정의합니다.
- **슬래시.** [AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) 를 사용 하 여 Azure Monitor에서 로그 데이터를 검색 하는 Azure Automation runbook 또는 명령줄에서 PowerShell 스크립트를 실행할 수 있습니다.  이 cmdlet에는 검색할 데이터를 결정하는 쿼리가 필요합니다.
- **Azure Monitor Logs API.**  REST API 클라이언트는 [Azure Monitor Logs API](https://dev.loganalytics.io)를 통해 작업 영역에서 로그 데이터를 검색할 수 있습니다.  API 요청에는 검색할 데이터를 확인하기 위해 Azure Monitor에 대해 실행되는 쿼리가 포함됩니다.


## <a name="next-steps"></a>다음 단계
- [Azure Portal에서 Log Analytics를 사용 하는 방법에 대 한 자습서](get-started-portal.md)를 안내 합니다.
- [쿼리 작성에 대 한 자습서](get-started-queries.md)를 안내 합니다.