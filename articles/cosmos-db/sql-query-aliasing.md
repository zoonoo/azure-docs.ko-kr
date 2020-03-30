---
title: Azure 코스모스 DB의 별칭
description: Azure Cosmos DB SQL 쿼리에서 별칭을 사용하여 이름이 같은 두 속성을 구분하는 방법을 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873474"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure 코스모스 DB의 별칭

쿼리에서 값을 명시적으로 별칭할 수 있습니다. 쿼리에 이름이 같은 두 개의 속성이 있는 경우 별칭을 사용하여 투영된 결과에서 모호해지도록 속성 중 하나 또는 둘 다의 이름을 바꿉니다.

## <a name="examples"></a>예

다음 예제에서 두 번째 값을 다음과 같이 프로젝트할 때와 같이 `NameInfo`별칭에 사용되는 AS 키워드는 선택 사항입니다.

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [select 절](sql-query-select.md)
- [FROM 절](sql-query-from.md)
