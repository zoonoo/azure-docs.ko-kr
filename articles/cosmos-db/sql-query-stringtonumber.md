---
title: Azure 코스모스 DB 쿼리 언어의 StringToNumber
description: Azure 코스모스 DB의 SQL 시스템 함수 StringToNumber에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296425"
---
# <a name="stringtonumber-azure-cosmos-db"></a>스트링토넘(Azure 코스모스 DB)
 숫자로 변환된 식을 반환합니다. 식을 번역할 수 없는 경우 정의되지 않은 반환을 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 번호 식으로 구문 분석할 문자열 식입니다. JSON의 숫자는 정수 또는 부동 점여야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식 또는 정의되지 않은 식을 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToNumber` 서로 다른 형식에서 어떻게 행동하는지 보여 주며, 

공백은 숫자 앞이나 이후에만 허용됩니다.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

JSON에서 유효한 번호는 정수 또는 부동 소수점 번호여야 합니다.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{}}
```  

전달된 식은 숫자 식으로 구문 분석됩니다. 이러한 입력은 숫자를 입력하도록 평가되지 않으므로 정의되지 않은 반환을 반환합니다. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{}}
```  

## <a name="remarks"></a>설명

이 시스템 함수는 인덱스를 사용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure 코스모스 DB](sql-query-string-functions.md)
- [시스템 기능 Azure 코스모스 DB](sql-query-system-functions.md)
- [Azure 코스모스 DB 소개](introduction.md)
