---
title: Azure Cosmos DB의 DateTimeDiff 쿼리 언어
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeDiff에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 520388668dd12173edbe3638daaeca07767d89b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567628"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
지정된 *StartDate* 와 *EndDate* 사이에 지정된 DateTimePart 경계의 수(부호 있는 정수 값으로)를 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
    `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값입니다. 형식은 다음과 같습니다.
  
|형식|Description|
|-|-|
|YYYY|네 자릿수 연도|
|MM|두 자릿수 월(01=1월 등)|
|DD|월 (01-31)의 2 자리 숫자 일|
|T|시간 요소 시작에 대한 기표|
|hh|두 자릿수 시간(00~23)|
|MM|두 자릿수 분(00~59)|
|ss|두 자릿수 초(00~59)|
|.fffffff|소수 일곱 자릿수 초|
|Z|UTC(협정 세계시) 지정자|
  
  ISO 8601 형식에 대한 자세한 내용은 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)을 참조하세요.

*EndDate*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값

## <a name="return-types"></a>반환 형식

부호 있는 정수 값을 반환합니다.

## <a name="remarks"></a>설명

DateTimeDiff는 다음과 같은 경우에 `undefined`를 반환합니다.

- 지정된 DateTimePart 값이 잘못된 경우
- StartDate 또는 EndDate가 올바른 ISO 8601 DateTime이 아닌 경우

DateTimeDiff는 항상 부호 있는 정수 값을 반환하며 시간 간격이 아닌 교차하는 DateTimePart 경계의 수를 측정합니다.

## <a name="examples"></a>예
  
다음 예제는 `2020-01-01T01:02:03.1234527Z`와 `2020-01-03T01:02:03.1234567Z` 간에 교차하는 날짜 경계의 수를 계산합니다.

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

다음 예제는 `2028-01-01T01:02:03.1234527Z`와 `2020-01-03T01:02:03.1234567Z` 간에 교차하는 연도 경계의 수를 계산합니다.

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

다음 예제는 `2020-01-01T01:00:00.1234527Z`와 `2020-01-01T01:59:59.1234567Z` 간에 교차는 시간 경계의 수를 계산합니다. 해당 DateTime 값이 0.99 시간을 초과하더라도 교차된 시간 경계가 없으므로 `DateTimeDiff`는 0을 반환합니다.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
