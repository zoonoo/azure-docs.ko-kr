---
title: Azure 코스모스 DB 쿼리 언어로 IS_PRIMITIVE
description: Azure Cosmos DB에서 IS_PRIMITIVE SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04c8e41f1a431b329f2093851e4430e69ab6aee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303786"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure 코스모스 DB)
 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 null)인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*Expr*  
   어떤 표현식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 함수를 사용하여 JSON Boolean, 숫자, 문자열, null, `IS_PRIMITIVE` 개체, 배열 및 정의되지 않은 형식의 개체를 검사합니다.  
  
```sql
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 [범위 인덱스의](index-policy.md#includeexclude-strategy)이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [유형 검사 기능 Azure 코스모스 DB](sql-query-type-checking-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
