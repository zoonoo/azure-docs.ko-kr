---
title: Azure Cosmos DB 쿼리 언어의 StringToBoolean
description: Azure Cosmos DB의 SQL 시스템 함수 StringToBoolean에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "78296544"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 부울로 변환 된 식을 반환 합니다. 식을 변환할 수 없는 경우는 undefined를 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   부울 식으로 구문 분석 되는 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식을 반환 하거나 undefined를 반환 합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는가 `StringToBoolean` 서로 다른 형식에서 동작 하는 방법을 보여 줍니다. 
 
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

공백은 "true"/"false" 전후에만 사용할 수 있습니다.

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

입력이 잘못 된 예는 다음과 같습니다.

 부울은 대/소문자를 구분 하며 모든 소문자 (예: "true" 및 "false")로 작성 되어야 합니다.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

결과 집합은 다음과 같습니다.  
  
```json
[{}]
``` 

전달 된 식은 부울 식으로 구문 분석 됩니다. 이러한 입력은 부울 형식으로 계산 되지 않으므로 undefined로 반환 됩니다.

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

이 시스템 함수는 인덱스를 활용 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
