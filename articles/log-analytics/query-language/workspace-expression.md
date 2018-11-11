---
title: Azure Log Analytics 쿼리의 workspace() 식 | Microsoft Docs
description: workspace 식은 동일한 리소스 그룹, 다른 리소스 그룹 또는 다른 구독의 특정 작업 영역에서 데이터를 검색하기 위해 Log Analytics 쿼리에서 사용됩니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 8b83650acfee638ae2a667cd0925fd91d44b80b2
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282699"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Log Analytics 쿼리의 workspace() 식

`workspace` 식은 동일한 리소스 그룹, 다른 리소스 그룹 또는 다른 구독의 특정 작업 영역에서 데이터를 검색하기 위해 Log Analytics 쿼리에서 사용됩니다. Application Insights 쿼리에 로그 데이터를 포함하고 로그 쿼리를 통해 여러 작업 영역의 데이터를 쿼리하는 데 유용합니다.


## <a name="syntax"></a>구문

`workspace(`*Identifier*`)`

## <a name="arguments"></a>인수

- *Identifier*: 아래 표에 있는 형식 중 하나를 사용하여 작업 영역을 식별합니다.

| 식별자 | 설명 | 예
|:---|:---|:---|
| 리소스 이름 | 사용자가 읽을 수 있는 작업 영역의 이름(즉, “구성 요소 이름”) | workspace(“contosoretail”) |
| 정규화된 이름 | “subscriptionName/resourceGroup/componentName” 형식으로 된 작업 영역의 전체 이름 | workspace(‘Contoso/ContosoResource/ContosoWorkspace’) |
| ID | 작업 영역의 GUID | workspace(“b438b3f6-912a-46d5-9db1-b42069242ab4”) |
| Azure 리소스 ID | Azure 리소스의 식별자 | workspace(“/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail”) |


## <a name="notes"></a>메모

* 작업 영역에 대한 읽기 권한이 있어야 합니다.
* 관련 식은 Application Insights 응용 프로그램 전체에서 쿼리할 수 있는 `app`입니다.

## <a name="examples"></a>예

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>다음 단계

- Application Insights 앱을 가리키는 [앱 식](workspace-expression.md)을 참조하세요.
- [Log Analytics 데이터](../../log-analytics/log-analytics-queries.md)의 저장 방법을 알아보세요.