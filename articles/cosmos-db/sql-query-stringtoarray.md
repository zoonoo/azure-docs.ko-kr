---
title: Azure Cosmos DB 쿼리 언어의 StringToArray
description: Azure Cosmos DB의 SQL 시스템 함수 StringToArray에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7ae1f69e92e890daae528eb1f4dfb95f76560043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93337985"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 배열로 변환된 식을 반환합니다. 식을 변환할 수 없는 경우 undefined을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 배열 식으로 구문 분석할 문자열 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
  배열 식 또는 undefined를 반환합니다. 
  
## <a name="remarks"></a>설명
  중첩된 문자열 값은 큰따옴표를 사용하여 유효한 JSON이 되도록 작성해야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)를 참조하세요. 이 시스템 함수는 인덱스를 활용하지 않습니다.
  
## <a name="examples"></a>예
  
  다음 예는 `StringToArray`이 여러 유형에서 작동하는 방식을 보여줍니다. 
  
 다음은 유효한 입력을 사용한 예입니다.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

결과 집합은 다음과 같습니다.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

다음은 유효하지 않은 입력의 예입니다. 
   
 배열 내의 작은따옴표는 유효한 JSON이 아닙니다.
쿼리 내에서 유효하더라도 이는 유효한 배열로 구문 분석되지 않습니다. 배열 문자열 내의 문자열은 "[\\"\\"]" 또는 주변 따옴표가 작은따옴표 '[""]'여야 합니다.

```sql
SELECT
    StringToArray("['5','6','7']")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```

다음은 잘못된 입력을 사용한 예입니다.
   
 전달된 식은 JSON 배열로 구문 분석됩니다. 다음은 형식 배열로 평가되지 않으므로 undefined를 반환합니다.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

결과 집합은 다음과 같습니다.

```json
[{}]
```

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
