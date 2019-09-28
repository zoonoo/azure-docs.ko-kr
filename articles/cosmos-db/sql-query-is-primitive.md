---
title: Azure Cosmos DB 쿼리 언어의 IS_PRIMITIVE
description: Azure Cosmos DB의 SQL 시스템 함수 IS_PRIMITIVE에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 960c6cbe6b60ad477f630b14ce0953601e71c34e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349815"
---
# <a name="is_primitive-azure-cosmos-db"></a>IS_PRIMITIVE (Azure Cosmos DB)
 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 null)인지 여부를 나타내는 부울 값을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
IS_PRIMITIVE(<expr>)  
```  
  
## <a name="arguments"></a>인수
  
*expr*  
   임의의 식입니다.  
  
## <a name="return-types"></a>반환 유형
  
  부울 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `IS_PRIMITIVE` 함수를 사용 하 여 JSON 부울, 숫자, 문자열, null, 개체, 배열 및 정의 되지 않은 형식의 개체를 검사 합니다.  
  
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

## <a name="next-steps"></a>다음 단계

- [형식 검사 함수 Azure Cosmos DB](sql-query-type-checking-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
