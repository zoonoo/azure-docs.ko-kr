---
title: Azure Cosmos DB 쿼리 언어 낮음
description: 대문자 데이터를 소문자로 변환한 후 문자열 식을 반환 하는 Azure Cosmos DB의 낮은 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78302273"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다.  

낮은 시스템 함수는 인덱스를 사용 하지 않습니다. 대/소문자를 구분 하지 않는 비교를 자주 수행 하려는 경우에는 낮은 시스템 함수에서 많은 양의를 사용할 수 있습니다. 이 경우 하위 시스템 함수를 사용 하 여 비교할 때마다 데이터를 정규화 하는 대신 삽입 시 대/소문자를 정규화 할 수 있습니다. 그런 다음 SELECT * FROM c WHERE (c. name) = ' bob '과 같은 쿼리는 단순히 SELECT * FROM c WHERE c.name = ' bob '이 됩니다.

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
  
  다음 예제에서는 쿼리에서를 사용 하는 방법을 보여 줍니다 `LOWER` .  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
