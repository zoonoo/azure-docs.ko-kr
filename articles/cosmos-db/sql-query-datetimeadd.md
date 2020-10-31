---
title: Azure Cosmos DB 쿼리 언어의 DateTimeAdd
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeAdd에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f6177426ce62f3369092403c52344fa764bcf2e6
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095856"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

지정 된 날짜/시간 문자열에 지정 된 숫자 값 (부호 있는 정수로)을 더하여 발생 하는 DateTime 문자열 값을 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
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

*numeric_expr*  
   지정 된 날짜/시간 DateTimePart에 추가 되는 부호 있는 정수 값입니다.

*DateTime*  
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

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601 문자열 값을 다음 형식으로 반환 합니다 `YYYY-MM-DDThh:mm:ss.fffffffZ` .
  
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

## <a name="remarks"></a>설명

DateTimeAdd는 `undefined` 다음과 같은 이유로를 반환 합니다.

- 지정 된 DateTimePart 값이 잘못 되었습니다.
- 지정 된 numeric_expr 올바른 정수가 아닙니다.
- 인수 또는 결과의 DateTime이 유효한 ISO 8601 DateTime이 아닙니다.

## <a name="examples"></a>예
  
다음 예에서는 날짜/시간에 1 개월을 더 합니다. `2020-07-09T23:20:13.4575530Z`

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

다음 예에서는 날짜/시간에서 2 시간을 뺍니다. `2020-07-09T23:20:13.4575530Z`

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
