---
title: Azure 코스모스 DB 쿼리 언어의 StringToNull
description: Azure 코스모스 DB의 SQL 시스템 함수 StringToNull에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296442"
---
# <a name="stringtonull-azure-cosmos-db"></a>스트링토널 (Azure 코스모스 DB)
 null로 변환된 식을 반환합니다. 식을 번역할 수 없는 경우 정의되지 않은 반환을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   null 식으로 구문 분석할 문자열 식입니다.
  
## <a name="return-types"></a>반환 형식
  
  null 식 또는 정의되지 않은 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToNull` 서로 다른 형식에서 어떻게 행동하는지 보여 주며, 

다음은 올바른 입력이 있는 예제입니다.

 공백은 "null" 이전 또는 이후에만 허용됩니다.

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

다음은 잘못된 입력이 있는 예제입니다.

Null은 대/소문자에 민감하며 모든 소문자(예: "null")로 작성해야 합니다.

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
[{}]
```  

전달된 식은 null 식으로 구문 분석됩니다. 이러한 입력은 null을 입력하도록 평가하지 않으므로 정의되지 않은 것으로 반환됩니다.

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

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
