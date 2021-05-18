---
title: Azure Cosmos DB 쿼리 언어의 TimestampToDateTime
description: Azure Cosmos DB의 SQL 시스템 함수 TimestampToDateTime에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: e9a77ab0ac32d627d59e2cb0fa4a680f174a6833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587239"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

지정된 타임스탬프 값을 DateTime으로 변환합니다.
  
## <a name="syntax"></a>구문
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>인수

*Timestamp*  

부호 있는 숫자 값으로, Unix Epoch 이후 경과한 현재 밀리초 수입니다. 즉, 1970년 1월 1일 목요일 00:00:00 이후 경과된 시간(밀리초)을 반환합니다.

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

TimestampToDateTime은 지정된 타임스탬프 값이 잘못된 경우 `undefined`를 반환합니다.

## <a name="examples"></a>예제
  
다음 예에서는 타임스탬프를 DateTime으로 변환합니다.

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
