---
title: Azure 코스모스 DB 쿼리 언어의 StringToBoolean
description: Azure 코스모스 DB의 SQL 시스템 함수 StringToBoolean에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296544"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>스트링토불리언 (Azure 코스모스 DB)
 부울로 변환된 식을 반환합니다. 식을 번역할 수 없는 경우 정의되지 않은 반환을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   부울 식으로 구문 분석할 문자열 식입니다.  
  
## <a name="return-types"></a>반환 형식
  
  부울 식 또는 정의되지 않은 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToBoolean` 서로 다른 형식에서 어떻게 행동하는지 보여 주며, 
 
 다음은 올바른 입력이 있는 예제입니다.

공백은 "true"/"false" 이전 또는 이후에만 허용됩니다.

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

다음은 잘못된 입력이 있는 예제입니다.

 부울은 대/소문자를 구분하며 모든 소문자(예: "true" 및 "false")로 작성해야 합니다.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

결과 집합은 다음과 같습니다.  
  
```json
[{}]
``` 

전달된 표현식은 부울 식으로 구문 분석됩니다. 이러한 입력은 부울을 입력하도록 평가되지 않으므로 정의되지 않은 반환을 반환합니다.

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

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
