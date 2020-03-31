---
title: Azure 모니터의 로그 쿼리 개요 | 마이크로 소프트 문서
description: 로그 쿼리와 관련된 일반적인 질문에 답하고 쿼리를 사용하기 시작했습니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670120"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Azure 모니터의 로그 쿼리 개요
로그 쿼리를 사용하면 [Azure Monitor 로그](../platform/data-platform-logs.md)에서 수집된 데이터의 값을 완전히 활용할 수 있습니다. 강력한 쿼리 언어를 사용하면 여러 테이블의 데이터를 조인하고, 큰 데이터 집합을 집계하고, 최소한의 코드로 복잡한 작업을 수행할 수 있습니다. 지원 데이터가 수집되고 올바른 쿼리를 구성하는 방법을 이해하는 한 거의 모든 질문에 대한 답변과 분석을 수행할 수 있습니다.

Azure Monitor의 일부 기능(예: [인사이트](../insights/insights-overview.md) 및 [솔루션)은](../insights/solutions-inventory.md) 기본 쿼리에 노출되지 않고 로그 데이터를 처리합니다. Azure Monitor의 다른 기능을 완전히 활용하려면 쿼리가 생성되는 방법과 쿼리를 사용하여 Azure Monitor Log의 데이터를 대화형으로 분석하는 방법을 이해해야 합니다.

이 문서를 Azure Monitor의 로그 쿼리에 대해 학습하기 위한 시작점으로 사용합니다. 일반적인 질문에 답하고 자세한 내용과 공과를 제공하는 다른 문서에 대한 링크를 제공합니다.

## <a name="how-can-i-learn-how-to-write-queries"></a>쿼리를 작성하는 방법을 어떻게 배울 수 있습니까?
당신이 바로 사물로 이동하려는 경우, 당신은 다음 자습서로 시작할 수 있습니다 :

- [Azure 모니터에서 로그 분석을 시작하십시오.](get-started-portal.md)
- [Azure 모니터에서 로그 쿼리를 시작합니다.](get-started-queries.md)

기본 사항을 숙지하고 나면 데모 환경의 자체 데이터 또는 데이터를 사용하여 여러 레슨을 진행합니다. 

- [Azure Monitor 로그 쿼리에서 문자열 작업](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>로그 쿼리는 어떤 언어를 사용합니까?
Azure 모니터 로그는 [Azure 데이터 탐색기를](/azure/data-explorer)기반으로 하며 로그 쿼리는 동일한 Kusto 쿼리 언어(KQL)를 사용하여 작성됩니다. 이 언어는 읽기 쉽고 작성하기 쉽도록 설계된 풍부한 언어이며 최소한의 지침으로 사용할 수 있어야 합니다.

KQL에 대한 전체 설명서및 사용 가능한 다양한 기능에 대한 참조는 [Azure 데이터 탐색기 KQL 설명서를](/azure/kusto/query) 참조하십시오.<br>
Azure 모니터 로그의 데이터를 사용하여 언어를 빠르게 연습할 수 있는 Azure [모니터의 로그 쿼리시작을](get-started-queries.md) 참조하십시오.
Azure Monitor에서 사용하는 KQL 버전의 사소한 차이점은 [Azure Monitor 로그 쿼리 언어 차이를](data-explorer-difference.md) 참조하십시오.

## <a name="what-data-is-available-to-log-queries"></a>쿼리를 로그하는 데 사용할 수 있는 데이터는 무엇입니까?
Azure 모니터 로그에서 수집된 모든 데이터를 로그 쿼리에서 검색하고 분석할 수 있습니다. 데이터 원본마다 데이터를 서로 다른 테이블에 쓰지만 단일 쿼리에 여러 테이블을 포함하여 여러 원본의 데이터를 분석할 수 있습니다. 쿼리를 빌드할 때 먼저 찾고 있는 데이터가 있는 테이블을 결정하므로 Azure Monitor Logs의 데이터가 구조화되는 방식을 최소한 기본적인 이해가 있어야 합니다.

Azure 모니터 로그를 채우는 다양한 데이터 원본 목록은 [Azure 모니터 로그 의 소스를](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)참조하십시오.<br>
데이터의 구조에 대한 설명은 [Azure 모니터 로그 구조를](logs-structure.md) 참조하십시오.

## <a name="what-does-a-log-query-look-like"></a>로그 쿼리는 어떤 모습일까요?
쿼리는 해당 테이블에서 모든 레코드를 검색하기 위한 단일 테이블 이름만큼 간단할 수 있습니다.

```Kusto
Syslog
```

또는 특정 레코드를 필터링하고 요약한 다음 결과를 차트에서 시각화할 수 있습니다.

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

보다 복잡한 분석을 위해 조인을 사용하여 여러 테이블에서 데이터를 검색하여 결과를 함께 분석할 수 있습니다.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
KQL에 익숙하지 않더라도 이러한 쿼리에서 사용되는 기본 논리를 적어도 파악할 수 있어야 합니다. 테이블 이름으로 시작한 다음 여러 명령을 추가하여 해당 데이터를 필터링하고 처리합니다. 쿼리는 원하는 수의 명령을 사용할 수 있으며 사용 가능한 다른 KQL 명령에 익숙해지면 더 복잡한 쿼리를 작성할 수 있습니다.

언어 및 공통 함수를 소개하는 로그 쿼리에 대한 자습서는 [Azure Monitor의 로그 쿼리로 시작하기를](get-started-queries.md) 참조하십시오.<br>


## <a name="what-is-log-analytics"></a>Log Analytics란?
로그 애널리틱스는 Azure 포털의 기본 도구로 로그 쿼리를 작성하고 결과를 대화형으로 분석합니다. 로그 쿼리는 Azure Monitor의 다른 위치에서도 사용되지만, 일반적으로 Log Analytics를 사용하여 쿼리를 작성하고 테스트합니다.

Azure 포털의 여러 위치에서 로그 분석을 시작할 수 있습니다. Log Analytics에서 사용할 수 있는 데이터의 범위는 시작 방법에 따라 결정됩니다. 자세한 내용은 [쿼리 범위를](scope.md) 참조하십시오.

- **Azure 모니터** 메뉴 또는 로그 분석 작업 영역 메뉴에서 **로그를** 선택합니다. **Log Analytics workspaces**
- 애플리케이션 인사이트 응용 프로그램의 **개요** 페이지에서 **분석을** 선택합니다.
- Azure 리소스의 메뉴에서 **로그를 선택합니다.**

![Log Analytics](media/log-query-overview/log-analytics.png)

[Azure 모니터의 로그 분석 시작하기를](get-started-portal.md) 참조하여 여러 기능을 소개하는 Log Analytics의 자습서 를 참조하세요.

## <a name="where-else-are-log-queries-used"></a>로그 쿼리가 사용되는 다른 곳은 어디입니까?
로그 쿼리 및 로그 분석에서 그 결과를 대화식으로 작업하는 것 외에도 쿼리를 사용할 Azure Monitor의 영역은 다음과 같습니다.

- **경고 규칙.** [경고 규칙](../platform/alerts-overview.md)은 작업 영역에서 데이터의 문제를 사전에 식별합니다.  각 경고 규칙은 일정한 간격으로 자동으로 실행되는 로그 검색을 기반으로 합니다.  경고를 만들어야 하는지 여부를 결정하도록 결과를 검사합니다.
- **대시보드** 쿼리 결과를 [Azure 대시보드](../learn/tutorial-logs-dashboards.md)에 고정하여 로그 및 메트릭 데이터를 함께 시각화하고 다른 Azure 사용자와 선택적으로 공유할 수 있습니다.
- **레이아웃.**  [뷰 디자이너](../platform/view-designer.md)를 통해 사용자 대시보드에 포함될 데이터의 시각화를 만들 수 있습니다.  로그 쿼리는 각 뷰의 [타일](../platform/view-designer-tiles.md) 및 [시각화 파트](../platform/view-designer-parts.md)에서 사용되는 데이터를 제공합니다.  
- **내보내기.**  Azure Monitor에서 Excel 또는 [Power BI](../platform/powerbi.md)로 데이터를 가져오는 경우 로그 쿼리를 만들어 내보낼 데이터를 정의합니다.
- **Powershell.** 명령줄에서 PowerShell 스크립트를 실행하거나 [Get-Az운영인사이트검색결과를](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) 사용하여 Azure 모니터에서 로그 데이터를 검색하는 Azure 자동화 실행책을 실행할 수 있습니다.  이 cmdlet에는 검색할 데이터를 결정하는 쿼리가 필요합니다.
- **Azure Monitor Logs API.**  REST API 클라이언트는 [Azure Monitor Logs API](https://dev.loganalytics.io)를 통해 작업 영역에서 로그 데이터를 검색할 수 있습니다.  API 요청에는 검색할 데이터를 확인하기 위해 Azure Monitor에 대해 실행되는 쿼리가 포함됩니다.


## <a name="next-steps"></a>다음 단계
- [Azure 포털에서 로그 분석을 사용하는 방법에 대한 자습서를](get-started-portal.md)살펴보세요.
- 쿼리 를 [작성하는 방법에 대한 자습서를](get-started-queries.md)통해 걸어 .
