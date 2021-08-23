---
title: Azure Cosmos DB의 StringToBoolean 쿼리 언어
description: Azure Cosmos DB의 SQL 시스템 함수 StringToBoolean에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d221fb9a738d1b508b08e605ffa3c5d1c927317a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122567623"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 부울로 변환된 식을 반환합니다. 식을 변환할 수 없는 경우 undefined을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   부울 식으로 구문 분석되는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환하거나 undefined을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예는 `StringToBoolean`이 여러 유형에서 작동하는 방식을 보여줍니다. 
 
 다음은 유효한 입력을 사용한 예입니다.

공백은 "true"/"false" 이전 또는 이후에만 사용할 수 있습니다.

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

다음은 잘못된 입력을 사용한 예입니다.

 부울은 대/소문자를 구분하며 모두 소문자, 즉 "true"/"false"로 작성되어야 합니다.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

결과 집합은 다음과 같습니다.  
  
```json
[{}]
``` 

전달된 표현식은 부울 식으로 구문 분석됩니다. 이러한 입력은 부울 유형으로 평가되지 않으므로 undefined를 반환합니다.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

결과 집합은 다음과 같습니다.  
  
```json
[{}]
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](../introduction.md)
