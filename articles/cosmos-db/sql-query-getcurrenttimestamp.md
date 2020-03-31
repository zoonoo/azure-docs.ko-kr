---
title: Azure 코스모스 DB 쿼리 언어의 현재 타임스탬프
description: Azure 코스모스 DB의 SQL 시스템 기능 GetCurrentTime스탬프에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "71350999"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrent타임스탬프 (Azure 코스모스 DB)
 1970년 1월 1일 목요일 00:00:00 이후 경과된 밀리초 수를 반환합니다. 
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>반환 형식
  
  1970년 1월 1일 목요일 00:00:00 이후 경과된 밀리초 수와 같은 유닉스 시대 이후 경과된 현재 밀리초 수인 숫자 값을 반환합니다.

## <a name="remarks"></a>설명

  GetCurrentTime스탬프()는 비결정적 함수입니다.
  
  반환된 결과는 UTC(조정된 유니버설 타임)입니다.

## <a name="examples"></a>예
  
  다음 예제에서는 GetCurrentTimestamp() 기본 제공 함수를 사용하여 현재 타임스탬프를 얻는 방법을 보여 주었습니다.
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 다음은 예제 결과 집합입니다.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>다음 단계

- [날짜 및 시간 기능 Azure 코스모스 DB](sql-query-date-time-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
