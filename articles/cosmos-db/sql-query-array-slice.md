---
title: Azure 코스모스 DB 쿼리 언어로 ARRAY_SLICE
description: Azure Cosmos DB의 Array 슬라이스 SQL 시스템 함수가 배열 식의 일부를 반환하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a98cb17d22f41776ff788d12ced6aa988ad0b10e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303327"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure 코스모스 DB)
 배열 식의 일부를 반환합니다.
  
## <a name="syntax"></a>구문
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>인수
  
*arr_expr*  
   모든 배열 식입니다.  
  
*num_expr*  
   배열을 시작할 0 기반 숫자 인덱스 배열의 마지막 요소에 관련된 시작 인덱스를 지정하는 데 음수 값을 사용할 수 있습니다. 즉, -1은 배열의 마지막 요소를 참조합니다.  

*num_expr* 결과 배열의 최대 요소 수를 설정하는 선택적 숫자 식입니다.    

## <a name="return-types"></a>반환 형식
  
  배열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 를 사용하여 `ARRAY_SLICE`배열의 다른 조각을 얻는 방법을 보여 주며 있습니다.  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [어레이 함수 Azure 코스모스 DB](sql-query-array-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
