---
title: GetCurrentDateAzure Azure 코스모스 DB 쿼리 언어의 시간
description: Azure 코스모스 DB의 SQL 시스템 함수 GetCurrentDateTime에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d50b08ab85c7e299c465c3eb6f34e867d6634006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303905"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure 코스모스 DB)
 현재 UTC(조정된 유니버설 타임) 날짜와 시간을 ISO 8601 문자열로 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>반환 형식
  
  현재 UTC 날짜 및 시간 ISO 8601 `YYYY-MM-DDThh:mm:ss.fffffffZ` 문자열 값을 다음과 같은 형식으로 반환합니다.
  
  |||
  |-|-|
  |YYYY|4자리 연도|
  |MM|두 자리 월(01 = 1월 등)|
  |DD|월 (01-31)의 2 자리 숫자 일|
  |T|시간 요소의 시작에 대한 의미|
  |hh|두 자리 시간(00~23)|
  |MM|두 자리 분(00 ~ 59)|
  |ss|두 자리 초(00 ~ 59)|
  |.fffffff|7자리 소수 초|
  |Z|UTC(유니버설 타임) 지정자||
  
  ISO 8601 형식에 대한 자세한 내용은 [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>설명

  GetCurrentDateTime()은 결정적이지 않은 함수입니다. 
  
  반환된 결과는 UTC입니다.

  정밀도는 7자리이며 정확도는 100나노초입니다.

## <a name="examples"></a>예
  
  다음 예제에서는 GetCurrentDateTime() 기본 제공 함수를 사용하여 현재 UTC 날짜 시간을 얻는 방법을 보여 주었습니다.
  
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

- [날짜 및 시간 기능 Azure 코스모스 DB](sql-query-date-time-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
