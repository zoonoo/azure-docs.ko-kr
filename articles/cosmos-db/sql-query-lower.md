---
title: Azure 코스모스 DB 쿼리 언어의 하위
description: Azure Cosmos DB의 LOWER SQL 시스템 함수에 대해 알아보기하여 대문자 데이터를 소문자로 변환한 후 문자열 식을 반환합니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302273"
---
# <a name="lower-azure-cosmos-db"></a>로어 (Azure 코스모스 DB)
 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다.  

LOWER 시스템 함수는 인덱스를 사용하지 않습니다. 대/소문자를 구분하지 않는 빈번한 비교를 수행하려는 경우 LOWER 시스템 함수는 상당한 양의 RU를 소비할 수 있습니다. 이 경우 LOWER 시스템 함수를 사용하여 매번 비교를 위해 데이터를 정규화하는 대신 삽입 시 대/소문자를 정규화할 수 있습니다. 그런 다음 SELECT * FROM LOWER(c.name) = 'bob'과 같은 쿼리는 단순히 select * from c에서 c.name = 'bob'이 됩니다.

## <a name="syntax"></a>구문
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 쿼리에서 `LOWER` 사용하는 방법을 보여 주며 있습니다.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
