---
title: Azure Cosmos DB 쿼리 언어의 INDEX_OF
description: Azure Cosmos DB의 SQL 시스템 함수 INDEX_OF에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3f757f4517bb8c45708c9d25f39052f7f95aad2b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101428"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 지정된 첫 번째 문자열 식 내의 두 번째 문자열 식에서 첫 번째로 나타나는 시작 위치를 반환하거나 문자열을 찾을 수 없는 경우 -1을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>인수
  
*str_expr1*  
   검색할 문자열 식입니다.  
  
*str_expr2*  
   검색할 문자열 식입니다.  

*numeric_expr* 검색을 시작할 위치를 설정 하는 선택적 숫자 식입니다. *Str_expr1* 의 첫 번째 위치는 0입니다. 
  
## <a name="return-types"></a>반환 형식
  
  숫자 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 "abc" 안에 다양한 부분 문자열의 인덱스를 반환합니다.  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
