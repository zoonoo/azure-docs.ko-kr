---
title: Azure Cosmos DB의 문자열 함수 쿼리 언어
description: Azure Cosmos DB의 문자열 SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6051e659502a9dbccf653a2b5009affe343b9df4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539192"
---
# <a name="string-functions-azure-cosmos-db"></a>문자열 함수(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

문자열 함수를 사용하면 Azure Cosmos DB의 문자열에 대해 작업을 수행할 수 있습니다.

## <a name="functions"></a>Functions

다음 스칼라 반환 함수는 문자열 입력 값에 대해 작업을 수행하고 문자열, 숫자 또는 부울 값을 반환합니다. **인덱스 사용** 열은 해당하는 경우, 사용자가 문자열 시스템 함수를 같음 필터가 있는 다른 값과 비교한다고 가정합니다.

| 시스템 함수                                 | 인덱스 사용량        | [스칼라 집계 함수가 있는 쿼리의 인덱스 사용량](index-overview.md#index-utilization-for-scalar-aggregate-functions) | 설명                                                      |
| ----------------------------------------------- | ------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| [CONCAT](sql-query-concat.md)                   | 전체 검사          | 전체 검사                                              |                                                              |
| [CONTAINS](sql-query-contains.md)               | 전체 인덱스 검사    | 전체 검사                                              |                                                              |
| [ENDSWITH](sql-query-endswith.md)               | 전체 인덱스 검사    | 전체 검사                                              |                                                              |
| [INDEX_OF](sql-query-index-of.md)               | 전체 검사          | 전체 검사                                              |                                                              |
| [LEFT](sql-query-left.md)                       | 정확한 인덱스 검사 | 정확한 인덱스 검사                                     |                                                              |
| [LENGTH](sql-query-length.md)                   | 전체 검사          | 전체 검사                                              |                                                              |
| [LOWER](sql-query-lower.md)                     | 전체 검사          | 전체 검사                                              |                                                              |
| [LTRIM](sql-query-ltrim.md)                     | 전체 검사          | 전체 검사                                              |                                                              |
| [REGEXMATCH](sql-query-regexmatch.md)           | 전체 인덱스 검사    | 전체 검사                                              |                                                              |
| [REPLACE](sql-query-replace.md)                 | 전체 검사          | 전체 검사                                              |                                                              |
| [REPLICATE](sql-query-replicate.md)             | 전체 검사          | 전체 검사                                              |                                                              |
| [REVERSE](sql-query-reverse.md)                 | 전체 검사          | 전체 검사                                              |                                                              |
| [RIGHT](sql-query-right.md)                     | 전체 검사          | 전체 검사                                              |                                                              |
| [RTRIM](sql-query-rtrim.md)                     | 전체 검사          | 전체 검사                                              |                                                              |
| [STARTSWITH](sql-query-startswith.md)           | 정확한 인덱스 검사 | 정확한 인덱스 검사                                     | 대/소문자를 구분 옵션을 true로 설정하면 확장된 인덱스 검사가 이루어집니다. |
| [STRINGEQUALS](sql-query-stringequals.md)       | 인덱스 이동         | 인덱스 이동                                             | 대/소문자를 구분 옵션을 true로 설정하면 확장된 인덱스 검사가 이루어집니다. |
| [StringToArray](sql-query-stringtoarray.md)     | 전체 검사          | 전체 검사                                              |                                                              |
| [StringToBoolean](sql-query-stringtoboolean.md) | 전체 검사          | 전체 검사                                              |                                                              |
| [StringToNull](sql-query-stringtonull.md)       | 전체 검사          | 전체 검사                                              |                                                              |
| [StringToNumber](sql-query-stringtonumber.md)   | 전체 검사          | 전체 검사                                              |                                                              |

Azure Cosmos DB의 [인덱스 사용량](index-overview.md#index-usage)에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregate-functions.md)
