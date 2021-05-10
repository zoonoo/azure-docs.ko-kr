---
title: Azure Cosmos DB 쿼리 언어의 DateTimeAdd
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeAdd에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597660"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

지정된 DateTime 문자열에 지정된 숫자 값(부호 있는 정수)을 더하여 DateTime 문자열 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>인수
  
*DateTimePart*  
   DateTimeAdd가 정수를 추가하는 날짜 부분입니다. 이 표에서는 올바른 DateTimePart 인수가 모두 나열되어 있습니다.

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

*numeric_expr*  
   지정된 DateTime의 DateTimePart에 추가되는 부호 있는 정수 값입니다.

*DateTime*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값입니다. 여기서 다음이 적용됩니다.
  
|형식|Description|
|-|-|
|YYYY|4자리 연도|
|MM|두 자리 월(01 = 1월 등)|
|DD|월 (01-31)의 2 자리 숫자 일|
|T|시간 요소 시작에 대한 signifier|
|hh|두 자릿수 시간(00-23)|
|MM|두 자릿수 분(00 - 59)|
|ss|두 자릿수 초(00 - 59)|
|.fffffff|소수 일곱 자릿수 초|
|Z|UTC(협정 세계시) 지정자|
  
  ISO 8601 형식에 대한 자세한 내용은 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)을 참조하세요.

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601 문자열 값을 `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식으로 반환합니다. 형식은 다음과 같습니다.
  
|형식|Description|
|-|-|
|YYYY|4자리 연도|
|MM|두 자리 월(01 = 1월 등)|
|DD|월 (01-31)의 2 자리 숫자 일|
|T|시간 요소 시작에 대한 signifier|
|hh|두 자릿수 시간(00-23)|
|MM|두 자릿수 분(00 - 59)|
|ss|두 자릿수 초(00 - 59)|
|.fffffff|소수 일곱 자릿수 초|
|Z|UTC(협정 세계시) 지정자|

## <a name="remarks"></a>설명

DateTimeAdd는 다음과 같은 경우에 `undefined`를 반환합니다.

- 지정된 DateTimePart 값이 잘못된 경우
- 지정된 numeric_expr이 올바른 정수가 아닌 경우
- 인수 또는 결과의 DateTime이 유효한 ISO 8601 DateTime이 아닌 경우

## <a name="examples"></a>예제
  
다음 예제에서는 DateTime: `2020-07-09T23:20:13.4575530Z`에 1개월을 더합니다.

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

다음 예제에서는 DateTime: `2020-07-09T23:20:13.4575530Z`에서 2시간을 뺍니다.

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
