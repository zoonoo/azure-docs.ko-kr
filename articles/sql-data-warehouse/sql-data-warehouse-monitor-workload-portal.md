---
title: 작업 모니터링-Azure portal | Microsoft Docs
description: Azure portal을 사용하여 Azure SQL Data Warehouse를 모니터링합니다.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 6c8ce090039e3d5cc85c86d920710294de2165f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748910"
---
# <a name="monitor-workload---azure-portal"></a>작업 모니터링-Azure portal

이 문서에서는 워크로드를 모니터링하기 위해 Azure portal을 사용하는 방법을 설명합니다. 여기에는 [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)용 log analytics를 사용하여 쿼리 실행 및 작업 추세를 조사하기 위한 Azure Monitor 로그 설정이 포함됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure SQL Data Warehouse: SQL data warehouse에 대한 로그가 수집됩니다. 프로비전된 SQL data warehouse가 없는 경우,[SQL Data Warehouse 만들기](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)지침을 참조하세요.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log Analytics 작업 영역에 대한 찾아보기 블레이드로 이동하고 작업 영역을 만듭니다. 

![Log Analytics 작업 영역](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![Analytics 작업 영역 추가](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![Analytics 작업 영역 추가](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

작업 영역에 대한 자세한 내용은 다음 [설명서](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#create-a-workspace)를 참조하세요.

## <a name="turn-on-diagnostic-logs"></a>진단 로그 켜기 

SQL data warehouse에서 로그를 내보내기 위한 진단 설정을 구성합니다. 로그는 SQL Data Warehouse에 대한 가장 일반적으로 사용되는 성능 문제 해결 DMV에 해당 하는 데이터 웨어하우스의 원격 분석 뷰로 구성됩니다. 현재 다음과 같은 뷰가 지원됩니다.

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![진단 로그를 사용하도록 설정](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Azure Storage, Stream Analytics 또는 Log Analytics로 로그를 내보낼 수 있습니다. 이 자습서에서는 Log Analytics를 선택 합니다.

![로그를 지정 합니다.](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Log Analytics에 대해 쿼리 실행

Log Analytics 작업 영역으로 이동하여 다음을 수행할 수 있습니다.

- 로그 쿼리를 사용하여 로그를 분석하고 다시 사용할 수 있도록 쿼리를 저장합니다.
- 재사용을 위해 쿼리를 저장 합니다.
- 로그 경고 만들기
- 쿼리 결과 대시보드에 고정

로그 쿼리 기능에 대한 내용은 다음 [설명서](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)를 방문합니다.

![Log Analytics 작업 영역 편집기](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![로그 분석 작업 영역 쿼리](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

Azure monitor 로그를 설정하고 구성했으므로 [Azure 대시보드를 사용자 지정](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)하여 팀 전체에 공유할 수 있습니다.
