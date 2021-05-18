---
title: Azure Cosmos DB 쿼리 언어의 TicksToDateTime
description: Azure Cosmos DB의 SQL 시스템 함수 TicksToDateTime에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9adf525f4edb4104978bae32bbbef15ffb131f68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587256"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

지정한 틱 값을 DateTime으로 변환합니다.
  
## <a name="syntax"></a>구문
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>인수

*Ticks*  

부호 있는 숫자 값으로, Unix Epoch 이후 경과한 현재 100나노초 틱 수입니다. 즉, 1970년 1월 1일 목요일 00:00:00부터 경과한 100나노초 틱의 수입니다.

## <a name="return-types"></a>반환 형식

UTC 날짜 및 시간 ISO 8601문자열 값을 `YYYY-MM-DDThh:mm:ss.fffffffZ` 형식으로 반환합니다. 형식은 다음과 같습니다.
  
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

지정한 틱 값이 유효하지 않으면 TicksToDateTime이 `undefined`를 반환합니다.

## <a name="examples"></a>예제
  
다음 예에서는 틱을 DateTime으로 변환합니다.

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
