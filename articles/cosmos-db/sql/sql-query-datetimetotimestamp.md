---
title: Azure Cosmos DB 쿼리 언어의 DateTimeToTimestamp
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeToTimestamp에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 27cee804d8c16cd3a3e2af1ac79a69c6cfd4de67
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537847"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

지정된 Datetime을 타임스탬프로 변환합니다.
  
## <a name="syntax"></a>구문
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>인수

*DateTime*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값입니다. 여기서 다음이 적용됩니다.
  
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

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값, Unix 기간 이후 경과된 현재 시간(밀리초)을 반환합니다(즉, 1970년 1월 1일 목요일 00:00:00 이후 경관된 시간(밀리초)).

## <a name="remarks"></a>설명

지정된 DateTime 값이 잘못된 경우 DateTimeToTimestamp에서는 `undefined`를 반환합니다.

## <a name="examples"></a>예제
  
다음 예에서는 DateTime을 타임스탬프로 변환합니다.

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

다음은 또 다른 예제입니다.

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
