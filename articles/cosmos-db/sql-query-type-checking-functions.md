---
title: Azure Cosmos DB 쿼리 언어의 형식 검사 함수
description: Azure Cosmos DB에서 SQL 시스템 함수를 확인 하는 방법에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349069"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>형식 검사 함수 (Azure Cosmos DB)

형식 검사 함수를 사용 하 여 SQL 쿼리 내에서 식의 형식을 확인할 수 있습니다. 형식 검사 함수를 사용 하 여 변수 이거나 알 수 없는 경우 즉석에서 항목 내의 속성 형식을 확인할 수 있습니다. 

## <a name="functions"></a>함수

다음 표에서는 지원 되는 기본 제공 형식 검사 함수를 제공 합니다.

다음 함수는 입력 값에 대한 형식 검사를 지원하며, 각 함수마다 부울 값을 반환합니다.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>다음 단계

- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
- [사용자 정의 함수](sql-query-udfs.md)
- [집계](sql-query-aggregates.md)
