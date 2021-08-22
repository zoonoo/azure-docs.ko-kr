---
title: Azure Cosmos DB 쿼리 언어의 DateTimeToTicks
description: Azure Cosmos DB의 SQL 시스템 함수 DateTimeToTicks에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: f732de3d886ac187b54e0890569aec3dcb302a53
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122530190"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

지정된 DateTime을 틱으로 변환합니다. 틱 하나는 100나노초 또는 1/1000만 초를 나타냅니다. 

## <a name="syntax"></a>구문
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>인수
  
*DateTime*  
   `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식의 UTC 날짜 및 시간 ISO 8601 문자열 값

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값으로, Unix Epoch 이후 경과한 현재 100나노초 틱 수를 반환합니다. 즉, DateTimeToTicks는 1970년 1월 1일 목요일 00:00:00 이후 경과된 100나노초 틱 수를 반환합니다.

## <a name="remarks"></a>설명

DateTime이 유효한 ISO 8601 DateTime이 아닌 경우 DateTimeDateTimeToTicks는 `undefined`를 반환합니다.

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="examples"></a>예

다음은 틱 수를 반환하는 예제입니다.

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

다음은 소수 자릿수 초 수를 지정하지 않고 틱 수를 반환하는 예제입니다.

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
