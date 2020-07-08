---
title: 워크 로드 모니터링-Azure Portal
description: Azure Portal를 사용 하 여 Synapse SQL 모니터링
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 01a22aa5d2ec7ed54be62f0975b0fefbafd84cd8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211564"
---
# <a name="monitor-workload---azure-portal"></a>워크 로드 모니터링-Azure Portal

이 문서에서는 Azure Portal를 사용 하 여 워크 로드를 모니터링 하는 방법을 설명 합니다. 여기에는 [SYNAPSE SQL](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)에 대 한 log analytics를 사용 하 여 쿼리 실행 및 워크 로드 추세를 조사 하는 Azure Monitor 로그 설정이 포함 됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- SQL 풀: SQL 풀에 대 한 로그를 수집 합니다. 프로 비전 된 SQL 풀이 없는 경우 [sql 풀 만들기](load-data-from-azure-blob-storage-using-polybase.md)의 지침을 참조 하세요.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log Analytics 작업 영역에 대 한 찾아보기 블레이드로 이동 하 고 작업 영역을 만듭니다.

![Log Analytics 작업 영역](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![분석 작업 영역 추가](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![분석 작업 영역 추가](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

작업 영역에 대 한 자세한 내용은 다음 [설명서](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)를 참조 하세요.

## <a name="turn-on-resource-logs"></a>리소스 로그 설정

SQL 풀에서 로그를 내보내도록 진단 설정을 구성 합니다. 로그는 가장 일반적으로 사용 되는 성능 문제 해결 Dmv와 동일한 원격 분석 뷰로 구성 되어 있습니다. 현재 지원 되는 뷰는 다음과 같습니다.

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![리소스 로그 사용](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

Azure Storage, Stream Analytics 또는 Log Analytics 로그를 내보낼 수 있습니다. 이 자습서에서는 Log Analytics를 선택 합니다.

![로그 지정](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Log Analytics에 대해 쿼리 실행

다음을 수행할 수 있는 Log Analytics 작업 영역으로 이동 합니다.

- 로그 쿼리를 사용 하 여 로그 분석 및 다시 사용할 쿼리 저장
- 다시 사용할 쿼리 저장
- 로그 경고 만들기
- 대시보드에 쿼리 결과 고정

로그 쿼리 기능에 대 한 자세한 내용은 다음 [설명서](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)를 참조 하세요.

![Log Analytics 작업 영역 편집기](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![작업 영역 쿼리 Log Analytics](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

## <a name="sample-log-queries"></a>샘플 로그 쿼리

```Kusto
//List all queries
AzureDiagnostics
| where Category contains "ExecRequests"
| project TimeGenerated, StartTime_t, EndTime_t, Status_s, Command_s, ResourceClass_s, duration=datetime_diff('millisecond',EndTime_t, StartTime_t)
```

```Kusto
//Chart the most active resource classes
AzureDiagnostics
| where Category contains "ExecRequests"
| where Status_s == "Completed"
| summarize totalQueries = dcount(RequestId_s) by ResourceClass_s
| render barchart
```

```Kusto
//Count of all queued queries
AzureDiagnostics
| where Category contains "waits"
| where Type_s == "UserConcurrencyResourceType"
| summarize totalQueuedQueries = dcount(RequestId_s)
```

## <a name="next-steps"></a>다음 단계

이제 Azure monitor 로그를 설정 하 고 구성 했으므로 팀 전체에서 공유 하도록 [azure 대시보드를 사용자 지정](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 합니다.
