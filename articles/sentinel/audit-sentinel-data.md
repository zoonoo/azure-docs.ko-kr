---
title: Azure 센티널 쿼리 및 활동 감사 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 수행 되는 쿼리 및 활동을 감사 하는 방법을 설명 합니다.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a8ea32d84da521c8a1af926c6cb5e26bc2738de2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102054756"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Azure Sentinel 쿼리 및 활동 감사

이 문서에서는 보안 작업 (SOC) 작업 영역에서 내부 및 외부 규정 준수 요구 사항 등 Azure 센티널 작업 영역에서 수행 되는 쿼리 실행 및 작업에 대 한 감사 데이터를 보는 방법을 설명 합니다.

Azure 센티널은 다음에 대 한 액세스를 제공 합니다.

- **Azureactivity** 테이블-경고 규칙 편집과 같이 Azure 센티널에서 수행 되는 모든 작업에 대 한 세부 정보를 제공 합니다. **Azureactivity** 테이블은 특정 쿼리 데이터를 기록 하지 않습니다. 자세한 내용은 [Azure 활동 로그를 사용 하 여 감사](#auditing-with-azure-activity-logs)를 참조 하세요.

- **Laquerylogs** 테이블은 Azure 센티널에서 실행 되는 쿼리를 포함 하 여 Log Analytics에서 실행 되는 쿼리에 대 한 세부 정보를 제공 합니다. 자세한 내용은 [LAQueryLogs로 감사](#auditing-with-laquerylogs)를 참조 하세요.

> [!TIP]
> 이 문서에서 설명 하는 수동 쿼리 외에도 Azure 센티널은 SOC 환경의 활동을 감사 하는 데 도움이 되는 기본 제공 통합 문서를 제공 합니다.
>
> Azure 센티널 **통합** 문서 영역에서 **작업 영역 감사** 통합 문서를 검색 합니다.



## <a name="auditing-with-azure-activity-logs"></a>Azure 활동 로그를 사용 하 여 감사

Azure 센티널의 감사 로그는 azure [활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 유지 관리 됩니다. 여기서 **azureactivity** 테이블에는 azure 센티널 작업 영역에서 수행 된 모든 작업이 포함 됩니다.

Azure 센티널을 사용 하 여 SOC 환경에서 활동을 감사 하는 경우 **Azureactivity** 테이블을 사용할 수 있습니다.

**AzureActivity 테이블을 쿼리하려면**:

1. [Azure 활동](connect-azure-activity.md) 데이터 원본을 연결 하 여 Azureactivity 라는 **로그** 화면에서 감사 이벤트를 새 테이블로 스트리밍 시작 합니다.

1. 그런 다음 다른 테이블과 같이 KQL를 사용 하 여 데이터를 쿼리 합니다.

    **Azureactivity** 테이블에는 Azure 센티널을 비롯 한 여러 서비스의 데이터가 포함 됩니다. Azure 센티널의 데이터만 필터링 하려면 다음 코드를 사용 하 여 쿼리를 시작 합니다.

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    예를 들어 특정 분석 규칙을 편집 하는 마지막 사용자를 확인 하려면 다음 쿼리를 사용 합니다 (를 확인 하려는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 규칙의 규칙 ID로 대체).

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

보고 해야 하는 항목에 따라 더 많은 매개 변수를 쿼리에 추가 하 여 **Azureactivities** 테이블을 탐색 합니다. 다음 섹션에서는 **Azureactivity** 테이블 데이터로 감사 하는 데 사용할 다른 샘플 쿼리를 제공 합니다. 

자세한 내용은 azure [활동 로그에 포함 된 Azure 센티널 데이터](#azure-sentinel-data-included-in-azure-activity-logs)를 참조 하세요.

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>지난 24 시간 동안 특정 사용자가 수행한 모든 작업 찾기

다음 **Azureactivity** 테이블 쿼리는 지난 24 시간 동안 특정 Azure AD 사용자가 수행한 모든 작업을 나열 합니다.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>모든 삭제 작업 찾기

다음 **Azureactivity** 테이블 쿼리는 Azure 센티널 작업 영역에서 수행 된 모든 삭제 작업을 나열 합니다.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Azure 활동 로그에 포함 된 azure 센티널 데이터
 
Azure 센티널의 감사 로그는 [Azure 활동 로그](../azure-monitor/essentials/platform-logs-overview.md)에서 유지 관리 되며 다음과 같은 유형의 정보를 포함 합니다.

|작업  |정보 유형  |
|---------|---------|
|**만든 날짜**     |경고 규칙 <br> 사례 설명 <br>인시던트 설명 <br>저장된 검색<br>Watchlists    <br>통합 문서     |
|**삭제됨**     |경고 규칙 <br>책갈피 <br>데이터 커넥터 <br>인시던트 <br>저장된 검색 <br>설정 <br>위협 인텔리전스 보고서 <br>Watchlists      <br>통합 문서 <br>워크플로  |
|**업데이트됨**     |  경고 규칙<br>책갈피 <br> 사례 <br> 데이터 커넥터 <br>인시던트 <br>인시던트 설명 <br>위협 인텔리전스 보고서 <br> 통합 문서 <br>워크플로       |
|     |         |

Azure 활동 로그를 사용 하 여 사용자 권한 부여 및 라이선스를 확인할 수도 있습니다. 

예를 들어 다음 표에는 Azure 활동 로그에 있는 선택한 작업과 로그 데이터를 끌어오는 특정 리소스가 나열 되어 있습니다.

|작업 이름|    리소스 종류|
|----|----|
|통합 문서 만들기 또는 업데이트  |Microsoft Insights/통합 문서|
|통합 문서 삭제    |Microsoft Insights/통합 문서|
|워크플로 설정   |Microsoft.Logic/workflows|
|워크플로 삭제    |Microsoft.Logic/workflows|
|저장된 검색 만들기    |OperationalInsights/작업 영역/savedSearches|
|저장 된 검색 삭제    |OperationalInsights/작업 영역/savedSearches|
|경고 규칙 업데이트 |Microsoft SecurityInsights/alertRules|
|경고 규칙 삭제 |Microsoft SecurityInsights/alertRules|
|경고 규칙 응답 작업 업데이트 |Microsoft SecurityInsights/alertRules/작업|
|경고 규칙 응답 작업 삭제 |Microsoft SecurityInsights/alertRules/작업|
|책갈피 업데이트   |Microsoft SecurityInsights/책갈피|
|책갈피 삭제   |Microsoft SecurityInsights/책갈피|
|업데이트 사례   |Microsoft SecurityInsights/사례|
|업데이트 사례 조사  |Microsoft SecurityInsights/사례/조사|
|사례 설명 만들기   |Microsoft SecurityInsights/사례/설명|
|데이터 커넥터 업데이트 |Microsoft SecurityInsights/dataConnectors|
|데이터 커넥터 삭제 |Microsoft SecurityInsights/dataConnectors|
|업데이트 설정    |Microsoft SecurityInsights/설정|
| | |

자세한 내용은 [Azure 활동 로그 이벤트 스키마](/azure/azure-monitor/essentials/activity-log-schema)를 참조 하세요.


## <a name="auditing-with-laquerylogs"></a>LAQueryLogs로 감사

**Laquerylogs** 테이블은 Log Analytics에서 실행 되는 로그 쿼리에 대 한 세부 정보를 제공 합니다. Log Analytics는 Azure 센티널의 기본 데이터 저장소로 사용 되므로 Azure 센티널 작업 영역에서 LAQueryLogs 데이터를 수집 하도록 시스템을 구성할 수 있습니다.

LAQueryLogs 데이터에는 다음과 같은 정보가 포함 됩니다.

- 쿼리가 실행 된 경우
- Log Analytics에서 쿼리를 실행 한 사람
- Log Analytics에서 쿼리를 실행 하는 데 사용 되는 도구 (예: Azure 센티널)
- 쿼리 텍스트 자체
- 각 쿼리 실행에 대 한 성능 데이터

> [!NOTE]
> - **Laquerylogs** 테이블에는 Azure 센티널의 로그 블레이드에서 실행 된 쿼리만 포함 됩니다. 또한 **조사 그래프나** Azure 센티널 **구하기** 페이지를 사용 하 여 예약 된 분석 규칙에 의해 실행 되는 쿼리는 포함 되지 않습니다.
> - 쿼리가 실행 되는 시간 사이에 약간의 지연이 있을 수 있으며 데이터가 **Laquerylogs** 테이블에 채워집니다. 감사 데이터에 대 한 **Laquerylogs** 테이블을 쿼리 하는 데 5 분 정도 대기 하는 것이 좋습니다.
>


**LAQueryLogs 테이블을 쿼리하려면**:

1. Log Analytics 작업 영역에서 **Laquerylogs** 테이블은 기본적으로 사용 하도록 설정 되어 있지 않습니다. Azure 센티널에서 감사할 때 **laquerylogs** 데이터를 사용 하려면 먼저 Log Analytics 작업 영역의 **진단 설정** 영역에서 **laquerylogs** 를 사용 하도록 설정 합니다.

    자세한 내용은 [Azure Monitor 로그의 쿼리 감사](/azure/azure-monitor/logs/query-audit)를 참조 하세요.


1. 그런 다음 다른 테이블과 같이 KQL를 사용 하 여 데이터를 쿼리 합니다.

    예를 들어 다음 쿼리는 지난 주에 실행 된 쿼리 수를 날짜별로 보여 줍니다.

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

다음 섹션에서는 Azure 센티널를 사용 하 여 SOC 환경에서 작업을 감사할 때 **Laquerylogs** 테이블에서 실행할 샘플 쿼리를 보여 줍니다.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>응답이 "정상"이 아닌 경우 실행 되는 쿼리 수

다음 **Laquerylogs** 테이블 쿼리는 **200 OK** 의 HTTP 응답 이외의 다른 항목이 수신 된 쿼리 실행 수를 보여 줍니다. 예를 들어이 수에는 실행에 실패 한 쿼리가 포함 됩니다.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>CPU를 많이 사용 하는 쿼리를 위한 사용자 표시

다음 **Laquerylogs** 테이블 쿼리는 cpu를 많이 사용 하는 쿼리를 실행 한 사용자를 cpu 사용 및 쿼리 시간의 길이에 따라 나열 합니다.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>지난 주에 가장 많은 쿼리를 실행 한 사용자 표시

다음 **Laquerylogs** 테이블 쿼리는 지난 주에 가장 많은 쿼리를 실행 한 사용자를 나열 합니다.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Azure 센티널 활동에 대 한 경고 구성

Azure 센티널 감사 리소스를 사용 하 여 사전 경고를 만들 수 있습니다.

예를 들어 Azure 센티널 작업 영역에 중요 한 테이블이 있는 경우 다음 쿼리를 사용 하 여 해당 테이블이 쿼리 될 때마다 알림을 받을 수 있습니다.

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>다음 단계

Azure 센티널에서 **작업 영역 감사** 통합 문서를 사용 하 여 SOC 환경에서 작업을 감사 합니다.

자세한 내용은 [자습서: 데이터 시각화 및 모니터링](tutorial-monitor-your-data.md)을 참조 하세요.
