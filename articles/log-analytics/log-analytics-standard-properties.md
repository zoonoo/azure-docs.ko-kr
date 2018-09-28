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
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955870"
---
# <a name="standard-properties-in-log-analytics-records"></a>Log Analytics 레코드의 표준 속성
[Log Analytics](../log-analytics/log-analytics-queries.md)의 데이터는 각각 고유한 속성 집합이 있는 특정 데이터 형식의 레코드 집합으로 저장됩니다. 많은 데이터 형식에는 여러 형식에 공통적인 표준 속성이 있습니다. 이 문서에서는 이러한 속성에 대해 설명하고 쿼리에 속성을 사용하는 방법의 예를 제공합니다.

이러한 속성 중 일부는 여전히 구현 중이므로 일부 데이터 형식에서는 표시되지만 다른 데이터 형식에는 표시되지 않을 수 있습니다.


## <a name="resourceid"></a>_ResourceId
**_ResourceId** 속성은 레코드가 연결된 리소스의 고유 ID를 포함합니다. 쿼리 범위를 특정 리소스의 레코드로만 제한하거나 여러 테이블의 관련 데이터를 조인하는 데 사용할 표준 속성을 제공합니다.

Azure 리소스의 경우 **_ResourceId** 값은 [Azure 리소스 ID URL](../azure-resource-manager/resource-group-template-functions-resource.md)입니다. 이 속성은 현재 Azure 리소스로 제한되지만 온-프레미스 컴퓨터와 같은 Azure 외부 리소스로 확장될 예정입니다. 

### <a name="examples"></a>예
다음 예제는 각 컴퓨터에 대한 성능 및 이벤트 데이터를 결합하는 쿼리를 보여 줍니다. 여기서는 ID가 _101_이고 프로세서 활용률이 50%를 초과하는 이벤트가 모두 표시됩니다.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

다음 예제는 _AzureActivity_ 레코드와 _SecurityEvent_ 레코드를 결합하는 쿼리를 보여 줍니다. 이 컴퓨터에 로그인한 사용자가 포함된 활동 작업이 모두 표시됩니다.

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

- [Log Analytics 데이터의 저장](../log-analytics/log-analytics-queries.md) 방법을 자세히 알아보세요.
- [Log Analytics에서 쿼리 작성](../log-analytics/query-language/get-started-queries.md) 단원을 계속 진행하세요.
- [Log Analytics에서 테이블 조인](../log-analytics/query-language/joins.md) 단원을 계속 진행하세요.