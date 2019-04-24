---
title: Azure Monitor 로그 쿼리에서 조인 | Microsoft Docs
description: 이 문서는 Azure Monitor 쿼리 언어에서 조인을 사용하는 방법에 대한 단원을 포함합니다.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 2ea5b4e3af6591e6e25a863998baa7cecb3e29e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60520104"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리에서 조인

> [!NOTE]
> 완료 해야 [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md) 하 고 [Azure Monitor 로그 쿼리](get-started-queries.md) 이 단원을 완료 하기 전에 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

조인을 사용하면 동일한 쿼리에서 여러 테이블의 데이터를 분석할 수 있습니다. 지정된 열의 값을 일치시켜 두 데이터 집합의 행을 병합합니다.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

이 예제에서는 모든 로그인 이벤트에 대해 첫 번째 데이터 세트가 필터링됩니다. 그런 후 모든 로그아웃 이벤트를 필터링하는 두 번째 데이터 세트와 조인됩니다. 에상되는 열은 _Computer_, _Account_, _TargetLogonId_ 및 _TimeGenerated_입니다. 데이터 세트는 공유 열 _TargetLogonId_를 통해 상호 연관됩니다. 출력은 상관 관계마다 하나의 레코드로 표시되며, 로그인 및 로그아웃 시간이 모두 포함됩니다.

두 데이터 세트에 동일한 이름의 열이 있는 경우 오른쪽 데이터 세트의 열에 인덱스 번호가 지정됩니다. 따라서 이 예제의 결과는 왼쪽 테이블의 값을 포함하는 _TargetLogonId_와 오른쪽 테이블의 값을 포함하는  _TargetLogonId1_으로 표시됩니다. 이 경우 두 번째 _TargetLogonId1_ 열은 `project-away` 연산자를 사용하여 제거되었습니다.

> [!NOTE]
> 성능 향상을 위해 `project` 연산자를 사용하여 조인된 데이터 집합의 관련 열만 유지합니다.


다음 구문을 사용하여 두 데이터 세트를 조인합니다. 그러면 조인된 키는 두 테이블 간에 다른 이름을 갖습니다.
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>조회 테이블
조인의 일반적인 용도는 결과를 좀 더 이해하기 쉬운 상태로 변환하는 데 도움이 되는 `datatable`을 사용하여 값의 정적 매핑을 수행하는 것입니다. 예를 들어, 각 이벤트 ID에 대한 이벤트 이름으로 보안 이벤트 데이터를 유용하게 만들 수 있습니다.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![datatable을 사용하여 조인](media/joins/dim-table.png)

## <a name="join-kinds"></a>조인 유형
_kind_ 인수를 사용하여 조인 유형을 지정합니다. 다음 표에 설명된 대로 각 유형은 지정된 테이블의 레코드 간에 다른 일치를 수행합니다.

| 조인 유형 | 설명 |
|:---|:---|
| innerunique | 기본 조인 모드입니다. 먼저 왼쪽 테이블에서 일치하는 열의 값이 검색되고 중복 값이 제거됩니다.  그런 후 고유 값 집합이 오른쪽 테이블과 일치하는지 확인합니다. |
| inner | 두 테이블에서 일치하는 레코드만 결과에 포함됩니다. |
| leftouter | 왼쪽 테이블의 모든 레코드와 오른쪽 테이블의 일치하는 레코드가 결과에 포함됩니다. 일치하지 않는 출력 속성에는 null이 포함됩니다.  |
| leftanti | 오른쪽과 일치하지 않는 왼쪽의 레코드가 결과에 포함됩니다. 결과 테이블에는 왼쪽 테이블의 열만 포함됩니다. |
| leftsemi | 오른쪽과 일치하는 왼쪽의 레코드가 결과에 포함됩니다. 결과 테이블에는 왼쪽 테이블의 열만 포함됩니다. |


## <a name="best-practices"></a>모범 사례

최적의 성능을 위해서는 다음 사항을 고려하세요.

- 각 테이블에 시간 필터를 사용하여 조인이 평가되어야 하는 레코드를 줄입니다.
- 조인에 앞서 `where` 및 `project`를 사용하여 입력 테이블의 행 및 열 수를 줄입니다.
- 한 테이블이 언제나 다른 테이블보다 더 작으면 해당 테이블을 조인의 좌변으로 사용합니다.


## <a name="next-steps"></a>다음 단계
Azure Monitor 로그 쿼리 사용에 대한 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [고급 쿼리 작성](advanced-query-writing.md)
- [차트](charts.md)