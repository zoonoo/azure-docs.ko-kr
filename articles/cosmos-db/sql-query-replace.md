---
title: Azure 코스모스 DB 쿼리 언어로 바꾸기
description: Azure Cosmos DB에서 SQL 시스템 기능 REPLACE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302205"
---
# <a name="replace-azure-cosmos-db"></a>교체 (Azure 코스모스 DB)
 지정된 문자열 값의 모든 항목을 다른 문자열 값으로 바꿉니다.  
  
## <a name="syntax"></a>구문
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   찾을 문자열 표현식입니다.  
  
*str_expr3*  
   *str_expr1* *str_expr2* 발생을 대체하는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 쿼리에서 `REPLACE` 사용하는 방법을 보여 주며 있습니다.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
