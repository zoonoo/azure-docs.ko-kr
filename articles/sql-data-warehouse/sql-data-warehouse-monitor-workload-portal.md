---
title: 워크 로드 모니터링-Azure Portal
description: Azure Portal를 사용 하 여 Azure SQL Data Warehouse 모니터링
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 73d837c34dd5a480cae08a41c89939414899052a
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645627"
---
# <a name="monitor-workload---azure-portal"></a>워크 로드 모니터링-Azure Portal

이 문서에서는 Azure Portal를 사용 하 여 워크 로드를 모니터링 하는 방법을 설명 합니다. 여기에는 [Azure SQL Data Warehouse](https://azure.microsoft.com/blog/workload-insights-with-sql-data-warehouse-delivered-through-azure-monitor-diagnostic-logs-pass/)에 대 한 log analytics를 사용 하 여 쿼리 실행 및 워크 로드 추세를 조사 하기 위한 Azure Monitor 로그 설정이 포함 됩니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- Azure SQL Data Warehouse: SQL Data Warehouse에 대 한 로그를 수집 합니다. 프로 비전 된 SQL Data Warehouse 없는 경우 [SQL Data Warehouse 만들기](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-tutorial)의 지침을 참조 하세요.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log Analytics 작업 영역에 대 한 찾아보기 블레이드로 이동 하 고 작업 영역을 만듭니다. 

![Log Analytics 작업 영역](media/sql-data-warehouse-monitor/log_analytics_workspaces.png)

![분석 작업 영역 추가](media/sql-data-warehouse-monitor/add_analytics_workspace.png)

![분석 작업 영역 추가](media/sql-data-warehouse-monitor/add_analytics_workspace_2.png)

작업 영역에 대 한 자세한 내용은 다음 [설명서](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace#create-a-workspace)를 참조 하세요.

## <a name="turn-on-diagnostic-logs"></a>진단 로그 켜기 

SQL Data Warehouse에서 로그를 내보내도록 진단 설정을 구성 합니다. 로그는 SQL Data Warehouse에 대해 가장 일반적으로 사용 되는 성능 문제 해결 Dmv와 동일한 데이터 웨어하우스의 원격 분석 뷰로 구성 됩니다. 현재 지원 되는 뷰는 다음과 같습니다.

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=aps-pdw-2016-au7)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=aps-pdw-2016-au7)


![진단 로그를 사용하도록 설정](media/sql-data-warehouse-monitor/enable_diagnostic_logs.png)

Azure Storage, Stream Analytics 또는 Log Analytics 로그를 내보낼 수 있습니다. 이 자습서에서는 Log Analytics를 선택 합니다.

![로그 지정](media/sql-data-warehouse-monitor/specify_logs.png)

## <a name="run-queries-against-log-analytics"></a>Log Analytics에 대해 쿼리 실행

다음을 수행할 수 있는 Log Analytics 작업 영역으로 이동 합니다.

- 로그 쿼리를 사용 하 여 로그 분석 및 다시 사용할 쿼리 저장
- 다시 사용할 쿼리 저장
- 로그 경고 만들기
- 대시보드에 쿼리 결과 고정

로그 쿼리 기능에 대 한 자세한 내용은 다음 [설명서](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language)를 참조 하세요.

![Log Analytics 작업 영역 편집기](media/sql-data-warehouse-monitor/log_analytics_workspace_editor.png)



![작업 영역 쿼리 Log Analytics](media/sql-data-warehouse-monitor/log_analytics_workspace_queries.png)

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

이제 Azure monitor 로그를 설정 하 고 구성 했으므로 팀 전체에서 공유 하도록 [azure 대시보드를 사용자 지정](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) 합니다.
