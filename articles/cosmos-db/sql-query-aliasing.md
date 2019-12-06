---
title: Azure Cosmos DB 앨리어싱
description: Azure Cosmos DB SQL 쿼리에서 별칭을 사용 하 여 이름이 같은 두 개의 속성을 구분 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873474"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Azure Cosmos DB 앨리어싱

쿼리에서 값의 별칭을 명시적으로 지정할 수 있습니다. 쿼리에 이름이 같은 두 개의 속성이 있는 경우 별칭을 사용 하 여 두 속성 중 하나 또는 둘 다의 이름을 바꿔 프로젝션 된 결과에서 명확 하 게 구분 합니다.

## <a name="examples"></a>예시

두 번째 값을 `NameInfo`로 프로젝션 하는 경우 별칭 지정에 사용 되는 AS 키워드는 선택 사항입니다.

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
- [SELECT 절](sql-query-select.md)
- [FROM 절](sql-query-from.md)
