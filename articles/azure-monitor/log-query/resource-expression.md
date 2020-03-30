---
title: Azure 모니터 로그 쿼리의 리소스() 식 | 마이크로 소프트 문서
description: 리소스 식은 리소스 중심 Azure Monitor 로그 쿼리에서 여러 리소스에서 데이터를 검색하는 데 사용됩니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 2a729caefe698b13833098ba48df9d4bfbd97356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665702"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor 로그 쿼리의 리소스() 식

식은 `resource` 다른 리소스에서 데이터를 [검색하기 위해 리소스로 범위가 설정된](scope.md#query-scope) Azure Monitor 쿼리에서 사용됩니다. 


## <a name="syntax"></a>구문

`resource(`*식별자*`)`

## <a name="arguments"></a>인수

- *식별자*: 리소스의 리소스 ID입니다.

| ID | Description | 예제
|:---|:---|:---|
| 리소스 | 리소스에 대한 데이터를 포함합니다. | 리소스("/구독/xxxxxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/microsoft.compute/virtualmachine/myvm") |
| 리소스 그룹 또는 구독 | 리소스에 대한 데이터와 리소스에 포함된 모든 리소스를 포함합니다.  | 리소스("구독/xxxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/리소스 그룹/myresourcegroup) |


## <a name="notes"></a>메모

* 리소스에 대한 읽기 액세스 권한이 있어야 합니다.


## <a name="examples"></a>예

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>다음 단계

- 쿼리 범위에 대한 자세한 내용은 [Azure 모니터 로그 분석의 로그 쿼리 범위 및 시간 범위를](scope.md) 참조하십시오.
- [ 쿼리 언어](/azure/kusto/query/)에 대한 전체 문서에 액세스합니다.
