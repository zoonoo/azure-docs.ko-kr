---
title: Azure Cosmos DB 쿼리 언어의 DateTimePart
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimePart에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 97eaff405086190b60279ac7d5cf8bf441c8f840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336370"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

지정된 DateTime 사이에 지정된 DateTimePart의 값을 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>인수
  
*DateTimePart*  
   DateTimePart가 값을 반환할 날짜의 부분입니다. 이 표에서는 올바른 DateTimePart 인수가 모두 나열되어 있습니다.

| DateTimePart | 약어        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| 월        | "month", "mm", "m"   |
| 일          | "day", "dd", "d"     |
| 시간         | "hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| Second       | "second", "ss", "s"  |
| Millisecond  | "millisecond", "ms"  |
| Microsecond  | "microsecond", "mcs" |
| Nanosecond   | "nanosecond", "ns"   |

*DateTime*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값

## <a name="return-types"></a>반환 형식

양의 정수 값을 반환합니다.

## <a name="remarks"></a>설명

DateTimePart는 다음과 같은 이유로 `undefined`를 반환합니다.

- 지정된 DateTimePart 값이 잘못되었습니다.
- DateTime이 유효한 ISO 8601 DateTime이 아닙니다.

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="examples"></a>예

다음은 해당 월의 정수 값을 반환하는 예입니다.

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

다음은 마이크로초 수를 반환하는 예입니다.

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
