---
title: Azure Cosmos DB 쿼리 언어의 DateTimeFromParts
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeFromParts에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aec567c642f8eb3cb421ef5a119fe58f3a5fe05a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86262061"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)

입력 값에서 생성 된 문자열 DateTime 값을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>인수
  
*숫자 연도* 형식의 연도에 대 한 정수 값입니다. `YYYY`

*숫자 월*  
   형식의 월에 대 한 정수 값입니다. `MM`

*숫자 일*  
   형식의 날짜에 대 한 정수 값 `DD`

*숫자 시간* (옵션) 형식의 시간에 대 한 정수 값 `hh`

*숫자 분* (선택 사항) 형식으로 된 분의 정수 값 `mm`

두 번째 형식으로 된 *숫자* (옵션)의 정수 값입니다.`ss`

*numberOfFractionsOfSecond* (선택 사항) 형식에서 초의 소수 부분에 대 한 정수 값입니다. `.fffffff`

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601 문자열 값을 다음 형식으로 반환 합니다 `YYYY-MM-DDThh:mm:ss.fffffffZ` .
  
  |형식|설명|
  |-|-|
  |YYYY|네 자리 연도|
  |MM|두 자리 월 (01 = 1 월 등)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소 시작에 대 한 signifier|
  |hh|두 자리 시간 (00-23)|
  |MM|두 자리 분 (00-59)|
  |ss|두 자리 초 (00-59)|
  |. fffffff|7 자리 소수 자릿수 초|
  |Z|UTC (협정 세계시) 지정자||
  
 ISO 8601 형식에 대 한 자세한 내용은을 참조 하십시오 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>설명

지정 된 정수가 잘못 된 날짜/시간을 만드는 경우 DateTimeFromParts는을 반환 `undefined` 합니다.

선택적 인수를 지정 하지 않으면 해당 값은 0이 됩니다.

## <a name="examples"></a>예

DateTime을 생성 하는 데 필요한 인수만 포함 하는 예제는 다음과 같습니다.

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

다음은 몇 가지 선택적 인수를 사용 하 여 DateTime을 생성 하는 또 다른 예입니다.

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

모든 선택적 인수를 사용 하 여 DateTime을 생성 하는 또 다른 예는 다음과 같습니다.

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
