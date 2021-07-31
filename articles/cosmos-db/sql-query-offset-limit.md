---
title: Azure Cosmos DB의 OFFSET LIMIT 절
description: Azure Cosmos DB에서 쿼리할 때 OFFSET LIMIT 절을 사용하여 몇 개의 특정 값을 건너뛰고 사용하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338300"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 OFFSET LIMIT 절
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

OFFSET LIMIT 절은 쿼리에서 몇 개의 값을 건너뛰고 사용하기 위한 선택적 절입니다. OFFSET LIMIT 절에는 OFFSET 개수와 LIMIT 개수가 필요합니다.

ORDER BY 절과 함께 OFFSET LIMIT를 사용하면 정렬된 값을 건너뛰고 사용하여 결과 집합이 생성됩니다. ORDER BY 절을 사용하지 않으면 결정적 값 순서로 생성됩니다.

## <a name="syntax"></a>구문
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>인수

- `<offset_amount>`

   쿼리 결과에서 건너뛰어야 하는 항목 수를 정수로 지정합니다.

- `<limit_amount>`
  
   쿼리 결과에 포함되어야 하는 항목 수를 정수로 지정합니다.

## <a name="remarks"></a>설명
  
  `OFFSET LIMIT` 절에는 `OFFSET` 개수와 `LIMIT` 개수가 둘 다 필요합니다. 선택적 `ORDER BY` 절을 사용하면 정렬된 값을 건너뛰어 결과 집합이 생성됩니다. 그러지 않으면 쿼리가 고정된 값 순서대로 반환합니다.

  오프셋되는 용어 수가 증가함에 따라 `OFFSET LIMIT`를 사용하는 쿼리의 RU 요금도 증가합니다. [여러 페이지의 결과](sql-query-pagination.md)가 있는 쿼리의 경우 일반적으로 [연속 토큰](sql-query-pagination.md#continuation-tokens)을 사용하는 것이 좋습니다. 연속 토큰은 나중에 쿼리가 다시 시작될 수 있는 위치의 “책갈피”입니다. `OFFSET LIMIT`를 사용하는 경우에는 “책갈피”가 없습니다. 쿼리의 다음 페이지를 반환하려면 처음부터 시작해야 합니다.
  
  항목을 완전히 건너뛰고 클라이언트 리소스를 저장하려는 경우 `OFFSET LIMIT`를 사용해야 합니다. 예를 들어 1부터 999까지의 결과를 볼 필요가 없고 1000번째 쿼리 결과로 건너뛰려는 경우 `OFFSET LIMIT`를 사용해야 합니다. 백 엔드에서 `OFFSET LIMIT`는 건너뛴 항목을 포함하여 각 항목을 계속 로드합니다. 필요하지 않은 항목 처리를 방지하여 클라이언트 리소스를 절약할 수 있으므로 성능이 향상됩니다.

## <a name="examples"></a>예

예를 들어 다음은 첫 번째 값을 건너뛰고 두 번째 값을 거주 도시의 이름 순으로 반환하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음은 첫 번째 값을 건너뛰고 두 번째 값을 순서 없이 반환하는 쿼리입니다.

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [ORDER BY 절](sql-query-order-by.md)
