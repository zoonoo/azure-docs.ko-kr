---
title: Azure Cosmos DB 쿼리 언어의 GetCurrentTimestamp
description: Azure Cosmos DB에서 SQL 시스템 함수 GetCurrentTimestamp에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524264"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 00:00:00 목요일 1 월 1 1970 일 이후 경과 된 시간 (밀리초)을 반환 합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>반환 형식
  
부호 있는 숫자 값, Unix epoch 이후 경과 된 현재 시간 (밀리초)을 반환 합니다 (예: 00:00:00 목요일 이후 경과 된 시간 (밀리초), 1 월 1970 일).

## <a name="remarks"></a>설명

GetCurrentTimestamp ()는 비결 정적 함수입니다. 반환 된 결과는 UTC (협정 세계시)입니다.

> [!NOTE]
> 이 시스템 함수는 인덱스를 활용 하지 않습니다. 현재 시간에 대 한 값을 비교 해야 하는 경우 쿼리 실행 전 현재 시간을 가져온 다음 절에서 해당 상수 문자열 값을 사용 합니다 `WHERE` .

## <a name="examples"></a>예제
  
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
