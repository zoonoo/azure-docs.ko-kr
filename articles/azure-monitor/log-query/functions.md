---
title: 함수 Azure Log Analytics | Microsoft Docs
description: 이 문서에서는 함수를 사용하여 Log Analytics의 한 쿼리에서 다른 쿼리를 호출하는 방법을 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 8f2855ed56d298ec4c6abee02dd59ce9471f0d2e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885328"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics에서 함수 사용

> [!NOTE]
> 이 단원을 완료하기 전에 [Analytics 포털 시작](get-started-portal.md) 및 [쿼리 시작](get-started-queries.md)을 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Log Analytics 쿼리를 다른 쿼리와 함께 사용하여 함수로 저장할 수 있습니다. 이렇게 하면 복잡한 쿼리를 여러 부분으로 나누어 간소화하고, 공통 코드를 여러 쿼리에서 다시 사용할 수 있습니다.

## <a name="create-a-function"></a>함수 만들기

**저장**을 클릭한 후 다음 표의 정보를 지정하여 Azure Portal에서 함수를 만듭니다.

| 설정 | 설명 |
|:---|:---|
| 이름           | **쿼리 탐색기**에 나타나는 쿼리의 표시 이름입니다. |
| 다른 이름으로 저장        | 함수 |
| 함수 별칭 | 다른 쿼리에서 함수를 사용하기 위한 약식 이름입니다. 공백을 포함할 수 없으며 고유해야 합니다. |
| Category       | **쿼리 탐색기**에서 저장된 쿼리 및 함수를 구성하는 범주입니다. |

> [!NOTE]
> Log Analytics의 함수는 다른 함수를 포함할 수 없습니다.

> [!NOTE]
> Log Analytics 쿼리에는 함수를 저장할 수 있지만, Application Insights 쿼리의 경우는 현재 이러한 기능이 지원되지 않습니다.



## <a name="use-a-function"></a>함수 사용
다른 쿼리에 해당 별칭을 포함하여 함수를 사용합니다. 다른 테이블처럼 사용할 수 있습니다.

## <a name="example"></a>예
다음 샘플 쿼리는 마지막 날에 보고된 모든 누락된 보안 업데이트를 반환합니다. 별칭 _security_updates_last_day_를 사용하여 이 쿼리를 함수로 저장합니다. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

다른 쿼리를 만들고 _security_updates_last_day_ 함수를 참조하여 SQL 관련 필수 보안 업데이트를 검색합니다.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>다음 단계
Log Analytics 쿼리 언어를 사용에 대해서는 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [조인](joins.md)
- [차트](charts.md)
