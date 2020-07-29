---
title: Azure Monitor 로그 쿼리의 resource () 식 | Microsoft Docs
description: 리소스 식은 여러 리소스에서 데이터를 검색 하기 위해 리소스 중심 Azure Monitor 로그 쿼리에서 사용 됩니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77665702"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor 로그 쿼리의 resource () 식

`resource`식은 다른 리소스에서 데이터를 검색 하기 위해 [리소스로 범위가](scope.md#query-scope) 지정 된 Azure Monitor 쿼리에 사용 됩니다. 


## <a name="syntax"></a>Syntax

`resource(`*한정자*`)`

## <a name="arguments"></a>인수

- *식별자*: 리소스의 리소스 ID입니다.

| ID | Description | 예제
|:---|:---|:---|
| 리소스 | 리소스에 대 한 데이터를 포함 합니다. | 리소스 ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| 리소스 그룹 또는 구독 | 리소스 및 리소스에 포함 된 모든 리소스에 대 한 데이터를 포함 합니다.  | 리소스 ("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>참고

* 리소스에 대 한 읽기 권한이 있어야 합니다.


## <a name="examples"></a>예

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>다음 단계

- 쿼리 범위에 대 한 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](scope.md) 를 참조 하세요.
- [ 쿼리 언어](/azure/kusto/query/)에 대한 전체 문서에 액세스합니다.
