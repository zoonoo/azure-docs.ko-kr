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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524305"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

00:00:00 목요일부터 1 월 1 1970 일에 경과한 100 나노초 틱 수를 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>반환 형식

부호 있는 숫자 값, Unix epoch 이후 경과한 현재 100 나노초 틱 수를 반환 합니다. 즉, GetCurrentTicks은 00:00:00 목요일부터 1 월 1 1970 일에 경과한 100 나노초 틱 수를 반환 합니다.

## <a name="remarks"></a>설명

GetCurrentTicks ()은 비결 정적 함수입니다. 반환 된 결과는 UTC (협정 세계시)입니다.

> [!NOTE]
> 이 시스템 함수는 인덱스를 활용 하지 않습니다. 현재 시간에 대 한 값을 비교 해야 하는 경우 쿼리 실행 전 현재 시간을 가져온 다음 절에서 해당 상수 문자열 값을 사용 합니다 `WHERE` .

## <a name="examples"></a>예제

다음은 틱 단위로 측정 되는 현재 시간을 반환 하는 예입니다.

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
