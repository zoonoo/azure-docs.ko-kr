---
title: Azure Cosmos DB 쿼리 언어에서 바꾸기
description: Azure Cosmos DB의 SQL 시스템 함수 대체에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302205"
---
# <a name="replace-azure-cosmos-db"></a>REPLACE (Azure Cosmos DB)
 지정된 문자열 값의 모든 항목을 다른 문자열 값으로 바꿉니다.  
  
## <a name="syntax"></a>구문
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   찾을 문자열 식입니다.  
  
*str_expr3*  
   *Str_expr1*에서 *str_expr2* 항목을 대체 하는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  문자열 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예에서는 쿼리에서 `REPLACE`를 사용 하는 방법을 보여 줍니다.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
