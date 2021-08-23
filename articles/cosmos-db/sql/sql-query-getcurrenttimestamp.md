---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTimestamp
description: Azure Cosmos DB의 SQL 시스템 함수 GetCurrentTimestamp에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4ddb01175f0affad4e2ed3c441e7fa3919b6f174
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567626"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 1970년 1월 1일 목요일 00:00:00 이후 경과된 시간(밀리초)을 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>반환 형식
  
부호 있는 숫자 값, Unix 기간 이후 경과된 현재 시간(밀리초)을 반환합니다(즉, 1970년 1월 1일 목요일 00:00:00 이후 경관된 시간(밀리초)).

## <a name="remarks"></a>설명

GetCurrentTimestamp()는 비결정적 함수입니다. 반환된 결과는 UTC(협정 세계시)입니다.

> [!NOTE]
> 이 시스템 함수는 인덱스를 활용하지 않습니다. 현재 시간에 대한 값을 비교해야 하는 경우 쿼리 실행 전 현재 시간을 가져온 다음 `WHERE` 절에서 해당 상수 문자열 값을 사용합니다.

## <a name="examples"></a>예
  
  다음 예제에서는 GetCurrentTimestamp() 기본 제공 함수를 사용하여 현재 타임스탬프를 가져오는 방법을 보여줍니다.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 다음은 결과 집합의 예입니다.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
