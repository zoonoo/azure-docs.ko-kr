---
title: Azure 코스모스 DB의 오프셋 제한 절
description: Azure Cosmos DB에서 쿼리할 때 OFFSET LIMIT 절을 사용하여 특정 값을 건너뛰고 사용하는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771580"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Azure 코스모스 DB의 오프셋 제한 절

OFFSET LIMIT 절은 건너뛰고 쿼리에서 몇 개의 값을 가져가는 선택적 절입니다. 오프셋 개수와 제한 수는 오프셋 제한 절에 필요합니다.

오프셋 제한은 ORDER BY 절과 함께 사용되며 결과 집합은 건너뛰고 정렬된 값을 수행하여 생성됩니다. ORDER BY 절을 사용하지 않으면 결정적 값 순서가 생성됩니다.

## <a name="syntax"></a>구문
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>인수

- `<offset_amount>`

   쿼리 결과가 건너뛰어야 하는 항목의 정수 수를 지정합니다.

- `<limit_amount>`
  
   쿼리 결과에 포함해야 하는 항목의 정수 수를 지정합니다.

## <a name="remarks"></a>설명
  
  절에는 `OFFSET` 개수와 `LIMIT` 개수가 모두 필요합니다. `OFFSET LIMIT` 선택적 `ORDER BY` 절을 사용하는 경우 정렬된 값을 건너뛰는 작업을 수행하여 결과 집합이 생성됩니다. 그렇지 않으면 쿼리가 고정된 값 순서를 반환합니다.

  오프셋되는 용어 수가 `OFFSET LIMIT` 증가함에 따라 쿼리의 RU 요금이 증가합니다. 결과의 여러 페이지가 있는 쿼리의 경우 일반적으로 연속 토큰을 사용하는 것이 좋습니다. 연속 토큰은 쿼리가 나중에 다시 시작할 수 있는 위치에 대한 "책갈피"입니다. 을 사용하는 `OFFSET LIMIT`경우 "책갈피"가 없습니다. 쿼리의 다음 페이지를 반환하려면 처음부터 시작해야 합니다.
  
  문서를 완전히 `OFFSET LIMIT` 건너뛰고 클라이언트 리소스를 저장하려는 경우 를 사용해야 합니다. 예를 들어 1000번째 쿼리 결과로 건너뛰고 결과 1부터 999까지 볼 필요가 없는 경우에 사용해야 `OFFSET LIMIT` 합니다. 백 엔드에서 `OFFSET LIMIT` 건너뛴 문서를 포함하여 각 문서를 로드합니다. 성능 이점은 필요하지 않은 문서를 처리하지 않아 클라이언트 리소스를 절약할 수 있다는 것입니다.

## <a name="examples"></a>예

예를 들어 첫 번째 값을 건너뛰고 두 번째 값을 반환하는 쿼리는 다음과 같습니다(상주 도시 이름 순서순).

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

다음은 첫 번째 값을 건너뛰고 두 번째 값을 반환하는 쿼리입니다(순서 없이).

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
- [select 절](sql-query-select.md)
- [주문 별 절](sql-query-order-by.md)
