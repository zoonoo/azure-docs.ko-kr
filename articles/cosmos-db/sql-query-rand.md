---
title: Azure 코스모스 DB 쿼리 언어의 RAND
description: Azure Cosmos DB에서 SQL 시스템 함수 RAND에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302222"
---
# <a name="rand-azure-cosmos-db"></a>랜드 (푸른 코스모스 DB)
 [0,1]에서 임의로 생성된 숫자 값을 반환합니다.
 
## <a name="syntax"></a>구문
  
```sql
RAND ()  
```  

## <a name="return-types"></a>반환 형식

  숫자 식을 반환합니다.

## <a name="remarks"></a>설명

  `RAND`는 비결정 함수입니다. 반복 호출은 `RAND` 동일한 결과를 반환하지 않습니다.

## <a name="examples"></a>예
  
  다음 예제는 임의로 생성된 숫자 값을 반환합니다.
  
```sql
SELECT RAND() AS rand 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [수학적 함수 Azure 코스모스 DB](sql-query-mathematical-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
