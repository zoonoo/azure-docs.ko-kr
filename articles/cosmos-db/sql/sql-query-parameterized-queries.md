---
title: Azure Cosmos DB의 매개 변수가 있는 쿼리
description: SQL 매개 변수가 있는 쿼리에서 사용자 입력의 강력한 처리 및 이스케이프를 제공하고 SQL 삽입을 통해 실수로 인한 데이터 공개를 방지하는 방법을 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 13c72407cba8258c9d8a4c326fe27a9fdb5c65eb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122538142"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Azure Cosmos DB의 매개 변수가 있는 쿼리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 익숙한 @ 표기법으로 표현된 매개 변수가 있는 쿼리를 지원합니다. 매개 변수가 있는 SQL은 사용자 입력의 강력한 처리 및 이스케이프를 제공하며 SQL 삽입을 통해 실수로 인한 데이터 노출을 방지합니다.

## <a name="examples"></a>예

예를 들어, `lastName` 및 `address.state`를 매개 변수로 사용하는 쿼리를 작성하고 사용자 입력에 따라 `lastName` 및 `address.state`의 다양한 값에 대해 해당 쿼리를 실행합니다.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

이후 다음과 같이 이 요청을 매개 변수가 있는 JSON 쿼리로 Azure Cosmos DB에 보낼 수 있습니다.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

다음 예제에서는 매개 변수가 있는 쿼리를 사용하여 TOP 인수를 설정합니다.

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

매개 변수 값은 유효한 JSON(문자열, 숫자, 부울, null, 짝수 배열 또는 중첩된 JSON)일 수 있습니다. Azure Cosmos DB는 스키마가 없으므로 매개 변수는 임의 형식에 대해 유효성이 검사되지 않습니다.

다음은 각 Azure Cosmos DB SDK의 매개 변수가 있는 쿼리에 대한 예제입니다.

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L195)
- [Java](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L392-L421)
- [Node.JS](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79)
- [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78)

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](../modeling-data.md)
