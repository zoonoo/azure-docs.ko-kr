---
title: Azure Cosmos DB의 StringToNull 쿼리 언어
description: Azure Cosmos DB의 SQL 시스템 함수 StringToNull에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339585"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 변환된 식을 null로 반환합니다. 표현식을 변환할 수 없는 경우 undefined를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   null 식으로 구문 분석할 문자열 식입니다.
  
## <a name="return-types"></a>반환 형식
  
  null 식 또는 undefined를 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예는 `StringToNull`이 여러 유형에서 작동하는 방식을 보여 줍니다. 

다음은 유효한 입력을 사용한 예입니다.

 공백은 "null" 앞뒤에만 허용됩니다.

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

다음은 잘못된 입력을 사용한 예입니다.

Null은 대/소문자를 구분하며 모두 소문자, 즉 "null"로 작성되어야 합니다.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{}]
```  

전달된 표현식은 null 표현식으로 구문 분석됩니다. 이러한 입력은 null 유형으로 평가되지 않으므로 undefined를 반환합니다.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
- [Azure Cosmos DB 소개](introduction.md)
