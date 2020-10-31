---
title: Azure Cosmos DB 쿼리 언어의 DateTimeDiff
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeDiff에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d51c8774a8646a4b961906ee77913d8edbe752ee
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095835"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

*지정 된 서 수* 와 *EndDate* 사이에 지정 된 DateTimePart 경계의 수를 부호 있는 정수 값으로 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>인수
  
*DateTimePart*  
   DateTimeAdd 정수를 추가 하는 날짜 부분입니다. 다음 표에서는 모든 유효한 DateTimePart 인수를 보여 줍니다.

| DateTimePart | 약어        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| Month        | "month", "mm", "m"   |
| 일          | "day", "dd", "d"     |
| 시간         | "시간", "hh"         |
| Minute       | "minute", "mi", "n"  |
| Second       | "second", "ss", "s"  |
| Millisecond  | "밀리초", "ms"  |
| 마이크로초  | "마이크로초", "mcs" |
| 나노초   | "나노초", "ns"   |

*StartDate*  
    UTC 날짜 및 시간 ISO 8601 문자열 값 (형식 `YYYY-MM-DDThh:mm:ss.fffffffZ` :
  
  |서식|설명|
  |-|-|
  |YYYY|네 자리 연도|
  |MM|두 자리 월 (01 = 1 월 등)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소 시작에 대 한 signifier|
  |hh|두 자리 시간 (00-23)|
  |mm|두 자리 분 (00-59)|
  |ss|두 자리 초 (00-59)|
  |. fffffff|7 자리 소수 자릿수 초|
  |Z|UTC (협정 세계시) 지정자||
  
  ISO 8601 형식에 대 한 자세한 내용은을 참조 하십시오 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

*EndDate*  
   형식의 UTC 날짜 및 시간 ISO 8601 문자열 값 `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>반환 형식

부호 있는 정수 값을 반환 합니다.

## <a name="remarks"></a>설명

DateTimeDiff는 `undefined` 다음과 같은 이유로를 반환 합니다.

- 지정 된 DateTimePart 값이 잘못 되었습니다.
- 날짜/시간 또는 EndDate이 잘못 된 ISO 8601 DateTime입니다.

DateTimeDiff는 항상 부호 있는 정수 값을 반환 하 고 시간 간격을 측정 하는 것이 아니라 DateTimePart 경계의 수를 측정 합니다.

## <a name="examples"></a>예
  
다음 예에서는와 사이의 날짜 경계 수를 계산 합니다 `2020-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

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

다음 예에서는와 사이의 연도 경계 수를 계산 합니다 `2028-01-01T01:02:03.1234527Z` `2020-01-03T01:02:03.1234567Z` .

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

다음 예에서는와 사이의 시간 경계 수를 계산 합니다 `2020-01-01T01:00:00.1234527Z` `2020-01-01T01:59:59.1234567Z` . 이러한 DateTime 값이 0.99 시간을 초과 하는 경우에도는 `DateTimeDiff` 시간 경계를 초과 하지 않았으므로 0을 반환 합니다.

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
- [Azure Cosmos DB 소개](introduction.md)
