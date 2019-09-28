---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTimestamp
description: Azure Cosmos DB에서 SQL 시스템 함수 GetCurrentTimestamp에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350999"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 00:00:00 목요일 1 월 1 1970 일 이후 경과 된 시간 (밀리초)을 반환 합니다. 
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>반환 유형
  
  숫자 값, Unix epoch 이후 경과한 현재 시간 (밀리초)을 반환 합니다. 즉, 00:00:00 목요일부터 1 월 1 1970 일에 경과 된 시간 (밀리초)입니다.

## <a name="remarks"></a>설명

  GetCurrentTimestamp ()는 비결 정적 함수입니다.
  
  반환 된 결과는 UTC (협정 세계시)입니다.

## <a name="examples"></a>예
  
  다음 예에서는 GetCurrentTimestamp () 기본 제공 함수를 사용 하 여 현재 타임 스탬프를 가져오는 방법을 보여 줍니다.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 예제 결과 집합은 다음과 같습니다.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 함수 Azure Cosmos DB](sql-query-date-time-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
