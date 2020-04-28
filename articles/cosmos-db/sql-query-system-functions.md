---
title: Azure Cosmos DB 쿼리 언어의 시스템 함수
description: Azure Cosmos DB의 기본 제공 및 사용자 정의 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74870533"
---
# <a name="system-functions-azure-cosmos-db"></a>시스템 함수 (Azure Cosmos DB)

 Cosmos DB는 많은 기본 제공 SQL 함수를 제공합니다. 기본 제공 함수의 범주는 다음과 같습니다.  
  
|함수 그룹|설명|작업|  
|--------------|-----------------|-----------------| 
|[배열 함수](sql-query-array-functions.md)|배열 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울 또는 배열 값을 반환합니다. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[날짜 및 시간 함수](sql-query-date-time-functions.md)|날짜 및 시간 함수를 사용 하 여 현재 UTC 날짜와 시간을 두 가지 형식으로 가져올 수 있습니다. 값이 Unix epoch 인 숫자 타임 스탬프 (밀리초) 이거나 ISO 8601 형식을 준수 하는 문자열입니다. | [Getcurrentdatetime](sql-query-getcurrentdatetime.md), [getcurrentdatetime](sql-query-getcurrenttimestamp.md) |
|[수치 연산 함수](sql-query-mathematical-functions.md)|수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [천장](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[공간 함수](sql-query-spatial-functions.md)|공간 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[문자열 함수](sql-query-string-functions.md)|문자열 함수는 문자열 입력 값에 대한 연산을 수행하고, 문자열, 숫자 또는 부울 값을 반환합니다. | [CONCAT](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REPLACE](sql-query-replace.md), [복제](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [stringtoarray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [stringtoobject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[형식 검사 함수](sql-query-type-checking-functions.md)|형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>기본 제공 함수 및 Udf (사용자 정의 함수)

현재 기본 제공 함수가 제공 되는 UDF (사용자 정의 함수)를 사용 중인 경우 해당 하는 기본 제공 함수를 실행 하는 것이 더 빠르고 효율적입니다.

## <a name="built-in-versus-ansi-sql-functions"></a>기본 제공 및 ANSI SQL 함수

Cosmos DB 함수와 ANSI SQL 함수 간의 주요 차이점은 Cosmos DB 함수는 스키마 없는 데이터 및 혼합 스키마 데이터에서 잘 작동 하도록 설계 되었다는 것입니다. 예를 들어 속성이 누락 되었거나와 같은 `unknown`숫자가 아닌 값을 가지는 경우 오류를 반환 하는 대신 항목을 건너뜁니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [배열 함수](sql-query-array-functions.md)
- [날짜 및 시간 함수](sql-query-date-time-functions.md)
- [수치 연산 함수](sql-query-mathematical-functions.md)
- [공간 함수](sql-query-spatial-functions.md)
- [문자열 함수](sql-query-string-functions.md)
- [형식 검사 함수](sql-query-type-checking-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
