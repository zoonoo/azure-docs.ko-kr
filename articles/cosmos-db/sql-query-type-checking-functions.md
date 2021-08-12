---
title: Azure Cosmos DB 쿼리 언어의 형식 검사 함수
description: Azure Cosmos DB의 형식 검사 SQL 시스템 함수에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7555c1a1789cc33a75f78f9f3bf8ff4e5a3e3af2
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539226"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>형식 검사 함수(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다. 형식 검사 함수를 사용하여 항목이 변수이거나 알 수 없는 경우 즉시 항목 내의 속성 형식을 알아낼 수 있습니다. 

## <a name="functions"></a>Functions

다음 함수는 입력 값에 대한 형식 검사를 지원하며, 각 함수마다 부울 값을 반환합니다. **인덱스 사용** 열은 해당하는 경우, 사용자가 형식 검사 함수를 같음 필터가 있는 다른 값과 비교한다고 가정합니다.

| 시스템 함수                           | 인덱스 사용량 | [스칼라 집계 함수가 있는 쿼리의 인덱스 사용량](index-overview.md#index-utilization-for-scalar-aggregate-functions) | 설명 |
| ----------------------------------------- | ----------- | ------------------------------------------------------------ | ------- |
| [IS_ARRAY](sql-query-is-array.md)         | 전체 검사   | 전체 검사                                                    |         |
| [IS_BOOL](sql-query-is-bool.md)           | 인덱스 이동  | 인덱스 이동                                                   |         |
| [IS_DEFINED](sql-query-is-defined.md)     | 인덱스 이동  | 인덱스 이동                                                   |         |
| [IS_NULL](sql-query-is-null.md)           | 인덱스 이동  | 인덱스 이동                                                   |         |
| [IS_NUMBER](sql-query-is-number.md)       | 인덱스 이동  | 인덱스 이동                                                   |         |
| [IS_OBJECT](sql-query-is-object.md)       | 전체 검사   | 전체 검사                                                    |         |
| [IS_PRIMITIVE](sql-query-is-primitive.md) | 인덱스 이동  | 인덱스 이동                                                   |         |
| [IS_STRING](sql-query-is-string.md)       | 인덱스 이동  | 인덱스 이동                                                   |         

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregate-functions.md)
