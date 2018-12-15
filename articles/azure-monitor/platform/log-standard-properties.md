---
title: Azure Monitor Log Analytics 보고서의 표준 속성 | Microsoft Docs
description: Azure Monitor Log Analytics에서 여러 데이터 형식에 공통적인 속성에 대해 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/27/2018
ms.author: bwren
ms.openlocfilehash: d2db9d426da58b3783b07210165a55cc6ec27658
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185956"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics 레코드의 표준 속성
[Log Analytics](../log-query/log-query-overview.md)의 데이터는 각각 고유한 속성 집합이 있는 특정 데이터 형식의 레코드 집합으로 저장됩니다. 많은 데이터 형식에는 여러 형식에 공통적인 표준 속성이 있습니다. 이 문서에서는 이러한 속성에 대해 설명하고 쿼리에 속성을 사용하는 방법의 예를 제공합니다.

이러한 속성 중 일부는 여전히 구현 중이므로 일부 데이터 형식에서는 표시되지만 다른 데이터 형식에는 표시되지 않을 수 있습니다.

## <a name="timegenerated"></a>TimeGenerated
**TimeGenerated** 속성은 레코드가 생성된 시간과 날짜를 포함합니다. 시간별로 필터링 또는 요약하는 데 사용할 수 있는 공용 속성을 제공합니다. Azure Portal에서 보기 또는 대시보드의 시간 범위를 선택하면 보기 또는 대시보드가 TimeGenerated를 사용하여 결과를 필터링합니다.

### <a name="examples"></a>예

다음 쿼리는 이전 주의 각 날짜에 생성된 오류 이벤트 수를 반환합니다.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>type
**Type** 속성은 레코드가 검색된 테이블 이름을 보유하며 테이블 이름을 레코드 유형이라고 생각해도 됩니다. 이 속성은 `search` 연산자를 사용하여 여러 유형의 레코드를 구분하는 쿼리처럼 여러 테이블의 레코드를 결합하는 쿼리에 유용합니다. 경우에 따라 **Type** 대신 **$table**을 사용할 수도 있습니다.

### <a name="examples"></a>예
다음 쿼리는 지난 시간 동안 수집된 레코드 수를 유형별로 반환합니다.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
**\_ResourceId** 속성은 레코드가 연결된 리소스의 고유 ID를 포함합니다. 쿼리 범위를 특정 리소스의 레코드로만 제한하거나 여러 테이블의 관련 데이터를 조인하는 데 사용할 표준 속성을 제공합니다.

Azure 리소스의 경우 **_ResourceId** 값은 [Azure 리소스 ID URL](../../azure-resource-manager/resource-group-template-functions-resource.md)입니다. 이 속성은 현재 Azure 리소스로 제한되지만 온-프레미스 컴퓨터와 같은 Azure 외부 리소스로 확장될 예정입니다.

> [!NOTE]
> 일부 데이터 형식은 Azure 리소스 ID 또는 적어도 그 일부(예: 구독 ID)를 포함하는 필드를 이미 갖고 있습니다. 이러한 필드는 이전 버전과의 호환성을 위해 유지되지만, 보다 일관적인 _ResourceId를 사용하여 교차 상관 관계를 수행하는 것이 좋습니다.

### <a name="examples"></a>예
다음 쿼리는 각 컴퓨터에 대한 성능 및 이벤트 데이터를 조인합니다. 여기서는 ID가 _101_이고 프로세서 활용률이 50%를 초과하는 이벤트가 모두 표시됩니다.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

다음 쿼리는 _AzureActivity_ 레코드를 _SecurityEvent_ 레코드와 조인합니다. 이 컴퓨터에 로그인한 사용자가 포함된 활동 작업이 모두 표시됩니다.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>다음 단계

- [Log Analytics 데이터의 저장](../log-query/log-query-overview.md) 방법을 자세히 알아보세요.
- [Log Analytics에서 쿼리 작성](../../azure-monitor/log-query/get-started-queries.md) 단원을 계속 진행하세요.
- [Log Analytics에서 테이블 조인](../../azure-monitor/log-query/joins.md) 단원을 계속 진행하세요.
