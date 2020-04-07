---
title: 워크로드 모니터링 - Azure 포털
description: Azure 포털을 사용하여 시냅스 SQL 모니터링
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 0658a775e40c1fc433c7c2e1d853493544e74ee4
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743209"
---
# <a name="monitor-workload---azure-portal"></a>워크로드 모니터링 - Azure 포털

이 문서에서는 Azure 포털을 사용하여 워크로드를 모니터링하는 방법을 설명합니다. 여기에는 [Synapse SQL에](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)대한 로그 분석을 사용하여 쿼리 실행 및 워크로드 추세를 조사하기 위해 Azure Monitor Log를 설정하는 것이 포함됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/)을 만듭니다.
- SQL 풀: SQL 풀에 대한 로그를 수집합니다. 프로비전된 SQL 풀이 없는 경우 [SQL 풀 만들기의](load-data-from-azure-blob-storage-using-polybase.md)지침을 참조하십시오.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log Analytics 작업 영역의 찾아보기 블레이드로 이동하여 작업 영역 만들기

![Log Analytics 작업 영역](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspaces.png)

![분석 작업 영역 추가](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace.png)

![분석 작업 영역 추가](./media/sql-data-warehouse-monitor-workload-portal/add_analytics_workspace_2.png)

작업 영역에 대한 자세한 내용은 다음 [설명서를](../../azure-monitor/learn/quick-create-workspace.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsond#create-a-workspace)참조하십시오.

## <a name="turn-on-diagnostic-logs"></a>진단 로그 켜기

SQL 풀에서 로그를 내보도록 진단 설정을 구성합니다. 로그는 가장 일반적으로 사용되는 성능 문제 해결 DMV와 동등한 원격 분석 보기로 구성됩니다. 현재 다음 보기가 지원됩니다.

- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

![진단 로그를 사용하도록 설정](./media/sql-data-warehouse-monitor-workload-portal/enable_diagnostic_logs.png)

로그는 Azure 저장소, 스트림 분석 또는 로그 분석으로 내보내질 수 있습니다. 이 자습서에서는 로그 분석을 선택합니다.

![로그 지정](./media/sql-data-warehouse-monitor-workload-portal/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>로그 분석에 대한 쿼리 실행

로그 분석 작업 영역으로 이동하여 다음을 수행할 수 있습니다.

- 로그 쿼리를 사용하여 로그를 분석하고 재사용을 위해 쿼리 저장
- 재사용을 위한 쿼리 저장
- 로그 경고 만들기
- 쿼리 결과를 대시보드에 고정

로그 쿼리 기능에 대한 자세한 내용은 다음 [설명서를](../../azure-monitor/log-query/query-language.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)참조하십시오.

![로그 분석 작업 영역 편집기](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_editor.png)

![로그 분석 작업 영역 쿼리](./media/sql-data-warehouse-monitor-workload-portal/log_analytics_workspace_queries.png)

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

Azure 모니터 로그를 설정하고 구성한 이제 [Azure 대시보드를 사용자 지정하여](../../azure-portal/azure-portal-dashboards.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 팀 간에 공유합니다.
