---
title: Azure Cosmos DB 쿼리 언어의 형식 검사 함수
description: Azure Cosmos DB의 형식 검사 SQL 시스템 함수에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2becc9216d847dfe26d8fd3a433993112fff7980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546355"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>형식 검사 함수(Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다. 형식 검사 함수를 사용하여 항목이 변수이거나 알 수 없는 경우 즉시 항목 내의 속성 형식을 알아낼 수 있습니다. 

## <a name="functions"></a>Functions

다음은 지원되는 기본 제공 형식 검사 함수의 표입니다.

다음 함수는 입력 값에 대한 형식 검사를 지원하며, 각 함수마다 부울 값을 반환합니다.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregate-functions.md)
