---
title: Azure Cosmos DB의 시스템 함수 쿼리 언어
description: Azure Cosmos DB의 기본 제공 및 사용자 정의 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526842"
---
# <a name="system-functions-azure-cosmos-db"></a>시스템 함수(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB는 많은 기본 제공 SQL 함수를 제공합니다. 기본 제공 함수의 범주는 다음과 같습니다.  
  
|함수 그룹|Description|작업|  
|--------------|-----------------|-----------------| 
|[배열 함수](sql-query-array-functions.md)|배열 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울 또는 배열 값을 반환합니다. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[날짜 및 시간 함수](sql-query-date-time-functions.md)|날짜 및 시간 함수를 사용하면 현재 UTC 날짜와 시간을 두 가지 형식(값이 Unix Epoch 밀리초인 숫자 형식의 타임스탬프, ISO 8601 형식을 준수하는 문자열 형식)으로 확인할 수 있습니다. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[수학 함수](sql-query-mathematical-functions.md)|수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[공간 함수](sql-query-spatial-functions.md)|공간 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[문자열 함수](sql-query-string-functions.md)|문자열 함수는 문자열 입력 값에 대한 연산을 수행하고, 문자열, 숫자 또는 부울 값을 반환합니다. | [CONCAT](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[REPLACE](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[형식 검사 함수](sql-query-type-checking-functions.md)|형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>기본 제공 함수와 UDF(사용자 정의 함수)

현재 제공되는 기본 제공 함수 대신에 UDF(사용자 정의 함수)를 사용하고 있다면, 해당하는 기본 제공 함수를 사용하는 것이 더 빨리 실행되며 더 효율적입니다.

## <a name="built-in-versus-ansi-sql-functions"></a>기본 제공 함수와 ANSI SQL 함수

Cosmos DB 함수와 ANSI SQL 함수 간의 주요 차이점은 Cosmos DB 함수는 스키마가 없거나 스키마가 혼합된 데이터와 잘 작동하도록 설계되었다는 것입니다. 예를 들어 속성이 누락되었거나 `undefined`와 같이 숫자가 아닌 값을 가지고 있으면 오류를 반환하는 대신 항목을 건너뜁니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [배열 함수](sql-query-array-functions.md)
- [날짜 및 시간 함수](sql-query-date-time-functions.md)
- [수학 함수](sql-query-mathematical-functions.md)
- [공간 함수](sql-query-spatial-functions.md)
- [문자열 함수](sql-query-string-functions.md)
- [형식 검사 함수](sql-query-type-checking-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregate-functions.md)
