---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTicks
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentTicks에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227371"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

틱 단위로 측정 되는 현재 날짜 및 시간을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>반환 형식

양의 정수 값을 반환 합니다.

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="examples"></a>예제

다음은 틱 단위로 측정 되는 현재 시간을 반환 하는 예입니다.

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
