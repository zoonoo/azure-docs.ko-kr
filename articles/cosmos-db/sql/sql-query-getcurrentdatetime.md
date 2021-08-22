---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentDateTime
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentDateTime에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 323986d06ec956ee891d0b4831415d68bc59700a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538143"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

현재 UTC(협정 세계시) 날짜와 시간을 ISO 8601 문자열로 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>반환 형식
  
현재 UTC 날짜 및 시간 ISO 8601 문자열 값을 다음과 같은 `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식으로 반환합니다
  
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

GetCurrentDateTime()은 비결정 함수입니다. 반환된 결과는 UTC입니다. 정밀도는 일곱 자릿수이며 정확도는 100나노초입니다.

> [!NOTE]
> 이 시스템 함수는 인덱스를 활용하지 않습니다. 현재 시간에 대한 값을 비교해야 하는 경우 쿼리 실행 전 현재 시간을 가져온 다음 `WHERE` 절에서 해당 상수 문자열 값을 사용합니다.

## <a name="examples"></a>예제
  
다음 예제에서는 GetCurrentDateTime() 기본 제공 함수를 사용하여 현재 UTC 날짜 시간을 가져오는 방법을 설명합니다.
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 다음은 예제 결과 집합입니다.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
