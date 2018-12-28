---
title: Azure Log Analytics 쿼리의 app() 식 | Microsoft Docs
description: app 식은 동일한 리소스 그룹, 다른 리소스 그룹 또는 다른 구독의 특정 Application Insights 앱에서 데이터를 검색하기 위해 Log Analytics 쿼리에서 사용됩니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: e83ba321a98e40f07ff82e68c7961c2a6a49076d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191838"
---
# <a name="app-expression-in-log-analytics-query"></a>Log Analytics 쿼리의 app() 식

`app` 식은 동일한 리소스 그룹, 다른 리소스 그룹 또는 다른 구독의 특정 Application Insights 앱에서 데이터를 검색하기 위해 Log Analytics 쿼리에서 사용됩니다. 이 식은 Log Analytics 쿼리에 응용 프로그램 데이터를 포함하고 Application Insights 쿼리를 통해 여러 응용 프로그램의 데이터를 쿼리하는 데 유용합니다.



## <a name="syntax"></a>구문

`app(`*Identifier*`)`


## <a name="arguments"></a>인수

- *식별자*: 아래 표에 있는 형식 중 하나를 사용하여 앱을 식별합니다.

| 식별자 | 설명 | 예
|:---|:---|:---|
| 리소스 이름 | 사용자가 읽을 수 있는 앱의 이름(즉, “구성 요소 이름”) | app(“fabrikamapp”) |
| 정규화된 이름 | “subscriptionName/resourceGroup/componentName” 형식으로 된 앱의 전체 이름 | app(‘AI-Prototype/Fabrikam/fabrikamapp’) |
| ID | 앱의 GUID | app(“988ba129-363e-4415-8fe7-8cbab5447518”) |
| Azure 리소스 ID | Azure 리소스의 식별자 |app(“/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp”) |


## <a name="notes"></a>메모

* 응용 프로그램에 대한 읽기 권한이 있어야 합니다.
* 응용 프로그램을 이름으로 식별하면 액세스 가능한 모든 구독에서 고유한 것으로 가정합니다. 지정된 이름의 응용 프로그램이 여러 개 있으면 모호성으로 인해 쿼리가 실패합니다. 이런 경우 다른 식별자 중 하나를 사용해야 합니다.
* 관련된 식 [작업 영역](workspace-expression.md)을 사용하여 Log Analytics 작업 영역 전체를 쿼리합니다.

## <a name="examples"></a>예

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
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

- Log Analytics 작업 영역을 가리키는 [작업 영역 식](workspace-expression.md)을 참조하세요.
- [Log Analytics 데이터](../../azure-monitor/log-query/log-query-overview.md)의 저장 방법을 알아보세요.