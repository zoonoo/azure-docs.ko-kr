---
title: Azure Cosmos DB 쿼리 언어의 IS_OBJECT
description: Azure Cosmos DB의 SQL 시스템 함수 IS_OBJECT에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8e55cda57202ce5a08257f1448adc828764ac817
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349821"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*expr*  
   임의의 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `IS_OBJECT` 함수를 사용 하 여 JSON 부울, 숫자, 문자열, null, 개체, 배열 및 정의 되지 않은 형식의 개체를 검사 합니다.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="next-steps"></a>다음 단계

- [형식 검사 함수 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
