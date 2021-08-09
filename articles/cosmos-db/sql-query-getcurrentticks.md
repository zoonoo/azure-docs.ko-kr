---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTicks
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentTicks에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524305"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

1970년 1월 1일 목요일 00:00:00 이후 경과된 100나노초 틱 수를 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값으로, Unix Epoch 이후 경과한 현재 100나노초 틱 수를 반환합니다. 즉, GetCurrentTicks는 1970년 1월 1일 목요일 00:00:00 이후 경과된 100나노초 틱 수를 반환합니다.

## <a name="remarks"></a>설명

GetCurrentTicks()는 비결정적 함수입니다. 반환된 결과는 UTC(협정 세계시)입니다.

> [!NOTE]
> 이 시스템 함수는 인덱스를 활용하지 않습니다. 현재 시간에 대한 값을 비교해야 하는 경우 쿼리 실행 전 현재 시간을 가져온 다음 `WHERE` 절에서 해당 상수 문자열 값을 사용합니다.

## <a name="examples"></a>예

다음은 틱으로 측정된 현재 시간을 반환하는 예입니다.

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
