---
title: Azure 코스모스 DB 쿼리 언어의 시스템 기능
description: Azure Cosmos DB의 기본 제공 및 사용자 정의 SQL 시스템 기능에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 6f41adbb726313ef095084d079dc7852736e0c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870533"
---
# <a name="system-functions-azure-cosmos-db"></a>시스템 기능(Azure 코스모스 DB)

 Cosmos DB는 많은 기본 제공 SQL 함수를 제공합니다. 기본 제공 함수의 범주는 다음과 같습니다.  
  
|함수 그룹|설명|작업|  
|--------------|-----------------|-----------------| 
|[배열 함수](sql-query-array-functions.md)|배열 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울 또는 배열 값을 반환합니다. | [ARRAY_CONCAT,](sql-query-array-concat.md) [ARRAY_CONTAINS,](sql-query-array-contains.md) [ARRAY_LENGTH,](sql-query-array-length.md) [ARRAY_SLICE](sql-query-array-slice.md) |
|[날짜 및 시간 함수](sql-query-date-time-functions.md)|날짜 및 시간 함수를 사용하면 현재 UTC 날짜와 시간을 두 가지 형태로 얻을 수 있습니다. 값이 밀리초 단위로 유닉스 시대또는 ISO 8601 형식을 준수하는 문자열인 숫자 타임스탬프입니다. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [겟전류타임스탬프](sql-query-getcurrenttimestamp.md) |
|[수학 함수](sql-query-mathematical-functions.md)|수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다. | [ABS,](sql-query-abs.md) [ACOS,](sql-query-acos.md) [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [천장,](sql-query-ceiling.md) [COS,](sql-query-cos.md) [COT,](sql-query-cot.md) [도,](sql-query-degrees.md) [EXP](sql-query-exp.md), [바닥,](sql-query-floor.md) [로그,](sql-query-log.md) [LOG10,](sql-query-log10.md) [PI,](sql-query-pi.md) [파워,](sql-query-power.md) [라디안](sql-query-radians.md), [랜드,](sql-query-rand.md) [라운드,](sql-query-round.md) [기호,](sql-query-sign.md) [죄,](sql-query-sin.md) [SQRT,](sql-query-sqrt.md) [광장,](sql-query-square.md) [탄,](sql-query-tan.md) [트렁크](sql-query-trunc.md) |
|[공간 기능](sql-query-spatial-functions.md)|공간 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다. | [ST_DISTANCE,](sql-query-st-distance.md) [ST_INTERSECTS,](sql-query-st-intersects.md) [ST_ISVALID,](sql-query-st-isvalid.md) [ST_ISVALIDDETAILED,](sql-query-st-isvaliddetailed.md) [ST_WITHIN](sql-query-st-within.md) |
|[문자열 함수](sql-query-string-functions.md)|문자열 함수는 문자열 입력 값에 대한 연산을 수행하고, 문자열, 숫자 또는 부울 값을 반환합니다. | [CONCAT](sql-query-concat.md), [포함](sql-query-contains.md), [INDEX_OF](sql-query-endswith.md) [,](sql-query-index-of.md) [왼쪽](sql-query-left.md), [길이](sql-query-length.md), [아래쪽](sql-query-lower.md), [LTRIM,](sql-query-ltrim.md) [교체](sql-query-replace.md), [복제](sql-query-replicate.md), 역 , [오른쪽](sql-query-reverse.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) [RIGHT](sql-query-right.md) |
|[형식 검사 함수](sql-query-type-checking-functions.md)|형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다. | [IS_ARRAY,](sql-query-is-array.md) [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md) [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>기본 제공 과 사용자 정의 함수(UdF)

현재 기본 제공 함수를 사용할 수 있는 사용자 정의 함수(UDF)를 사용하는 경우 해당 기본 제공 함수를 더 빠르게 실행하고 효율성을 높일 수 있습니다.

## <a name="built-in-versus-ansi-sql-functions"></a>내장 대 ANSI SQL 함수

코스모스 DB 함수와 ANSI SQL 함수의 주요 차이점은 코스모스 DB 함수가 스키마가 없고 혼합 스키마 데이터와 잘 작동하도록 설계되었다는 점입니다. 예를 들어 속성이 없거나 `unknown`숫자값이 아닌 값이 있는 경우 오류를 반환하는 대신 항목이 건너뜁니다.

## <a name="next-steps"></a>다음 단계

- [Azure 코스모스 DB 소개](introduction.md)
- [배열 함수](sql-query-array-functions.md)
- [날짜 및 시간 함수](sql-query-date-time-functions.md)
- [수학 함수](sql-query-mathematical-functions.md)
- [공간 기능](sql-query-spatial-functions.md)
- [문자열 함수](sql-query-string-functions.md)
- [형식 검사 함수](sql-query-type-checking-functions.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
