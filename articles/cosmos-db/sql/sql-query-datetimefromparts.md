---
title: Azure Cosmos DB 쿼리 언어의 DateTimeFromParts
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeFromParts에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4cf613b7ab58b22df9db613ebfadcbf9b0aad91a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567748"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

입력 값에서 생성된 문자열 DateTime 값을 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>인수
  
*numberYear* `YYYY` 형식의 연도에 대한 정수 값

*numberMonth*  
   `MM` 형식의 월에 대한 정수 값

*numberDay*  
   `DD` 형식의 일에 대한 정수 값

*numberHour*(선택 사항) `hh` 형식의 시간에 대한 정수 값

*numberMinute*(선택 사항) `mm` 형식의 분에 대한 정수 값

*numberSecond*(선택 사항) `ss` 형식의 초에 대한 정수 값

*numberOfFractionsOfSecond*(선택 사항) `.fffffff` 형식의 1초 미만의 시간에 대한 정수 값

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601 문자열 값을 `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식으로 반환합니다. 형식은 다음과 같습니다.
  
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
  
 ISO 8601형식에 대한 자세한 내용은 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)을 참조하세요

## <a name="remarks"></a>설명

지정된 정수가 유효하지 않은 DateTime을 생성하는 경우 DateTimeFromParts는 `undefined`를 반환합니다.

선택적 인수를 지정하지 않으면 해당 값은 0이 됩니다.

## <a name="examples"></a>예제

다음은 필요한 인수만 포함하여 DateTime을 생성하는 예입니다.

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

다음은 몇 가지 선택적 인수를 사용하여 DateTime을 생성하는 예입니다.

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

다음은 모든 선택적 인수를 사용하여 DateTime을 생성하는 예입니다.

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
- [Azure Cosmos DB 소개](../introduction.md)
