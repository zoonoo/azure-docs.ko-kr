---
title: Azure Cosmos DB 쿼리 언어의 StringToNumber
description: Azure Cosmos DB의 SQL 시스템 함수 StringToNumber에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339566"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 숫자로 변환된 식을 반환합니다. 식을 변환할 수 없는 경우 undefined를 반환합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 숫자 식으로 구문 분석할 문자열 식입니다. JSON의 숫자는 정수이거나 부동 소수점 숫자여야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/)를 참조하세요.  
  
## <a name="return-types"></a>반환 형식
  
  숫자 식 또는 undefined를 반환합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToNumber`가 여러 형식에서 작동하는 방식을 보여 줍니다. 

공백은 숫자 앞뒤에만 허용됩니다.

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

JSON에서 유효한 숫자는 정수이거나 부동 소수점 숫자여야 합니다.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{}}
```  

전달된 식은 숫자 식으로 구문 분석됩니다. 이러한 입력은 숫자 형식으로 평가되지 않으므로 undefined를 반환합니다. 

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

이 시스템 함수는 인덱스를 활용하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
