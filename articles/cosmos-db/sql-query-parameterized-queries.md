---
title: Azure Cosmos DB의 매개 변수가 있는 쿼리
description: SQL 매개 변수가 있는 쿼리를 통해 사용자 입력의 강력한 처리 및 이스케이프를 제공 하 고 SQL 주입을 통해 실수로 데이터가 노출 되는 것을 방지 하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: f66bc89ef56dd0c2291903d531a4637210abd8df
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496987"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB의 매개 변수가 있는 쿼리

Azure Cosmos DB는 익숙한 @ 표기법으로 표현 된 매개 변수가 있는 쿼리를 지원 합니다. 매개 변수가 있는 SQL은 사용자 입력의 강력한 처리 및 이스케이프를 제공 하 고 SQL 주입을 통해 실수로 데이터가 노출 되는 것을 방지 합니다.

## <a name="examples"></a>예제

예를 들어 및를 매개 변수로 사용 하는 쿼리를 작성 하 `lastName` `address.state` 고 `lastName` `address.state` 사용자 입력을 기준으로 다양 한 값에 대해 실행할 수 있습니다.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

그러면 다음과 같이이 요청을 매개 변수가 있는 JSON 쿼리로 Azure Cosmos DB 보낼 수 있습니다.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

다음 예에서는 매개 변수가 있는 쿼리를 사용 하 여 TOP 인수를 설정 합니다.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

매개 변수 값은 문자열, 숫자, 부울, null, 배열 또는 중첩 된 JSON의 모든 유효한 JSON이 될 수 있습니다. Azure Cosmos DB은 스키마 형식 이므로 모든 형식에 대해 매개 변수의 유효성이 검사 되지 않습니다.

다음은 각 Azure Cosmos DB SDK의 매개 변수가 있는 쿼리에 대 한 예입니다.

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.JS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
